# Modul 01.01: Path Hasher & Direktori Isolasi Data

Dokumen ini memandu pembuatan utilitas pembuat direktori isolasi data (`PathHasher`) agar memori, cache, dan context window agen AI tidak saling tercampur antar-proyek atau antar-branch.

---

## 1. Konsep & Masalah yang Dipecahkan

Jika pengguna memiliki dua repositori dengan nama folder yang sama di direktori berbeda:
- `~/work/backend-api`
- `~/personal/backend-api`

Jika hanya menggunakan nama folder (`backend-api`), cache dan context window Antigravity IDE akan bertabrakan. Untuk mencegah hal ini, Anti Dispatch menghasilkan hash 6-karakter dari absolute path folder proyek.

### Formula Direktori:
```
~/.antigravity-multi/<6-char-md5-hash>_<project-name>[_<sanitized-branch>]
```

---

## 2. Kode Swift Lengkap (`PathHasher.swift`)

Simpan kode ini di dalam direktori `Anti Dispatch/Utilities/PathHasher.swift`:

```swift
import Foundation
import CryptoKit

public enum PathHasher: Sendable {
    
    /// Menghitung 6 karakter heksadesimal awal dari MD5 hash sebuah string path.
    public nonisolated static func computeShortHash(for path: String) -> String {
        let cleanPath = (path as NSString).standardizingPath
        let inputData = Data(cleanPath.utf8)
        let digest = Insecure.MD5.hash(data: inputData)
        
        let hexString = digest.map { String(format: "%02hhx", $0) }.joined()
        return String(hexString.prefix(6))
    }
    
    /// Menghasilkan URL direktori isolasi lengkap untuk sebuah workspace dan branch.
    public nonisolated static func resolveDataDirectory(
        for workspaceURL: URL,
        branch: String? = nil,
        customRoot: URL? = nil
    ) -> URL {
        let standardizedURL = workspaceURL.standardizedFileURL
        let pathHash = computeShortHash(for: standardizedURL.path)
        let projectName = standardizedURL.lastPathComponent
        
        var directoryName = "\(pathHash)_\(projectName)"
        
        if let branch = branch, !branch.isEmpty {
            let safeBranch = sanitizeBranchForFolderName(branch)
            directoryName += "_\(safeBranch)"
        }
        
        let root = customRoot ?? FileManager.default.homeDirectoryForCurrentUser.appendingPathComponent(".antigravity-multi")
        return root.appendingPathComponent(directoryName)
    }
    
    /// Sanitasi nama branch agar aman dijadikan nama folder di sistem file.
    public nonisolated static func sanitizeBranchForFolderName(_ branch: String) -> String {
        let invalidCharacters = CharacterSet(charactersIn: "/\\:?*\"<>| ").inverted
        let components = branch.components(separatedBy: invalidCharacters.inverted)
        return components.filter { !$0.isEmpty }.joined(separator: "-")
    }
}
```

---

## 3. Cara Penggunaan & Pengujian Cepat

Anda dapat menguji utilitas ini dengan memanggil:

```swift
let projectURL = URL(fileURLWithPath: "/Users/developer/Developer/my-api")
let isolatedDir = PathHasher.resolveDataDirectory(for: projectURL, branch: "feat/auth-v2")

print("Target Isolated Data Directory: \(isolatedDir.path)")
// Output: /Users/developer/.antigravity-multi/a1b2c3_my-api_feat-auth-v2
```

Modul ini siap digunakan oleh `LauncherService` pada modul berikutnya.
