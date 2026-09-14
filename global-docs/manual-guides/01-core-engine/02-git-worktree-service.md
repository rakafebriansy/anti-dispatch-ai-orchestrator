# Modul 01.02: Git Worktree Service Native

Dokumen ini memandu pembuatan `GitWorktreeService` — sebuah Swift `actor` yang membungkus perintah Git CLI (`/usr/bin/git`) secara native tanpa memerlukan skrip shell eksternal.

---

## 1. Fungsi Utama `GitWorktreeService`
1. **Validasi Repositori:** Memeriksa apakah direktori yang dipilih adalah repositori Git yang sah via `git rev-parse --is-inside-work-tree`.
2. **Pencarian Worktree Eksis:** Memeriksa apakah branch sudah pernah dibuatkan worktree sebelumnya via `git worktree list --porcelain`.
3. **Pembuatan Worktree Baru:** Menjalankan `git worktree add` ke direktori terisolasi (`<repo>-<sanitized-branch>`).

---

## 2. Kode Swift Lengkap (`GitWorktreeService.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Core/Services/GitWorktreeService.swift`:

```swift
import Foundation

public actor GitWorktreeService {
    
    public init() {}
    
    /// Cek apakah direktori adalah repositori Git yang valid.
    public func isGitRepository(at url: URL) async -> Bool {
        let result = await runGit(args: ["rev-parse", "--is-inside-work-tree"], currentDir: url)
        return result.status == 0 && result.output.trimmingCharacters(in: .whitespacesAndNewlines) == "true"
    }
    
    /// Cari path worktree yang sudah ada untuk suatu branch.
    public func findExistingWorktree(for branch: String, in repoURL: URL) async -> URL? {
        let result = await runGit(args: ["worktree", "list", "--porcelain"], currentDir: repoURL)
        guard result.status == 0 else { return nil }
        
        let lines = result.output.components(separatedBy: "\n")
        var currentWorktreePath: String?
        
        for line in lines {
            if line.hasPrefix("worktree ") {
                currentWorktreePath = String(line.dropFirst(9)).trimmingCharacters(in: .whitespaces)
            } else if line.hasPrefix("branch refs/heads/\(branch)") {
                if let path = currentWorktreePath {
                    return URL(fileURLWithPath: path)
                }
            }
        }
        return nil
    }
    
    /// Resolusi cerdas cabang dasar (preferredBase -> main -> master -> HEAD fallback)
    public func resolveDefaultBaseBranch(in repoURL: URL, preferredBase: String? = nil) async -> String {
        // 1. Jika ada preferensi khusus yang ditentukan pengguna dan branch tersebut valid
        if let preferred = preferredBase, !preferred.trimmingCharacters(in: .whitespaces).isEmpty {
            let check = await runGit(args: ["show-ref", "--verify", "--quiet", "refs/heads/\(preferred)"], currentDir: repoURL)
            if check.status == 0 { return preferred }
        }
        
        // 2. Cek apakah ada branch "main"
            let mainCheck = await runGit(args: ["show-ref", "--verify", "--quiet", "refs/heads/main"], currentDir: repoURL)
            if mainCheck.status == 0 { return "main" }
            
            // 3. Cek apakah ada branch "master" (repositori lawas)
            let masterCheck = await runGit(args: ["show-ref", "--verify", "--quiet", "refs/heads/master"], currentDir: repoURL)
            if masterCheck.status == 0 { return "master" }
        
        // 4. Fallback absolut ke pointer HEAD aktif saat ini
        return "HEAD"
    }
    
    /// Siapkan worktree: Gunakan yang sudah ada atau buat worktree baru.
    public func prepareWorktree(
        branch: String,
        in repoURL: URL,
        baseBranch: String? = nil
    ) async throws -> URL {
        // 1. Jika sudah ada, gunakan kembali
        if let existing = await findExistingWorktree(for: branch, in: repoURL) {
            return existing
        }
        
        // 2. Format folder target di samping direktori repo induk
        let safeBranchFolder = branch.replacingOccurrences(of: "/", with: "-")
                                     .replacingOccurrences(of: ":", with: "-")
        let parentDir = repoURL.deletingLastPathComponent()
        let projectName = repoURL.lastPathComponent
        let targetWorktreeURL = parentDir.appendingPathComponent("\(projectName)-\(safeBranchFolder)")
        
        // 3. Periksa apakah branch lokal sudah ada
        let refCheck = await runGit(args: ["show-ref", "--verify", "--quiet", "refs/heads/\(branch)"], currentDir: repoURL)
        
        if refCheck.status == 0 {
            // Branch lokal sudah ada -> Sambungkan ke worktree
            let res = await runGit(args: ["worktree", "add", targetWorktreeURL.path, branch], currentDir: repoURL)
            guard res.status == 0 else {
                throw NSError(domain: "GitWorktreeError", code: 1, userInfo: [NSLocalizedDescriptionKey: res.output])
            }
        } else {
            // Branch belum ada -> Resolusi branch induk (preferred -> main -> master -> HEAD)
            let resolvedBase = await resolveDefaultBaseBranch(in: repoURL, preferredBase: baseBranch)
            
            let res = await runGit(args: ["worktree", "add", "-b", branch, targetWorktreeURL.path, resolvedBase], currentDir: repoURL)
            guard res.status == 0 else {
                throw NSError(domain: "GitWorktreeError", code: 2, userInfo: [NSLocalizedDescriptionKey: res.output])
            }
        }
        
        return targetWorktreeURL
    }
    
    /// Eksekusi perintah /usr/bin/git melalui Process
    private func runGit(args: [String], currentDir: URL) async -> (status: Int32, output: String) {
        let process = Process()
        process.executableURL = URL(fileURLWithPath: "/usr/bin/git")
        process.arguments = args
        process.currentDirectoryURL = currentDir
        
        let pipe = Pipe()
        process.standardOutput = pipe
        process.standardError = pipe
        
        do {
            try process.run()
            process.waitUntilExit()
            let data = pipe.fileHandleForReading.readDataToEndOfFile()
            let output = String(data: data, encoding: .utf8) ?? ""
            return (process.terminationStatus, output)
        } catch {
            return (-1, error.localizedDescription)
        }
    }
}
```

---

## 3. Penjelasan Desain
* Digunakan tipe `actor` untuk menjamin eksekusi perintah Git berjalan aman dari *race condition* (misalnya jika pengguna membuka dua worktree secara bersamaan).
* Menggunakan path sistem standar `/usr/bin/git` yang selalu tersedia pada instalasi Command Line Tools macOS.
