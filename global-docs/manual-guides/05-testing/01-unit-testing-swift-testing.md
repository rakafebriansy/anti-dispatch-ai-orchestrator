# Modul 05.01: Unit Testing dengan Swift Testing

Dokumen ini memandu penulisan test suite modern menggunakan framework resmi Apple **`Swift Testing`** (`import Testing` / `@Test`) untuk memvalidasi fungsi-fungsi inti Anti Dispatch.

---

## 1. Kode Test Suite Lengkap (`Anti_DispatchTests.swift`)

Simpan kode ini di dalam direktori `Anti DispatchTests/Anti_DispatchTests.swift`:

```swift
import Testing
import Foundation
@testable import Anti_Dispatch

@Suite("Anti Dispatch Core Engine Tests")
struct AntiDispatchCoreTests {

    // MARK: - 1. PathHasher Tests
    @Test("Uji komputasi short hash MD5 6 karakter")
    func testShortHashComputation() {
        let pathA = "/Users/raka/Developer/my-app"
        let pathB = "/Users/raka/Developer/my-app"
        let pathC = "/Users/raka/Developer/other-app"
        
        let hashA = PathHasher.computeShortHash(for: pathA)
        let hashB = PathHasher.computeShortHash(for: pathB)
        let hashC = PathHasher.computeShortHash(for: pathC)
        
        #expect(hashA.count == 6)
        #expect(hashA == hashB)
        #expect(hashA != hashC)
    }

    @Test("Uji sanitasi nama branch untuk nama folder worktree")
    func testBranchSanitization() {
        let branch = "feat/user:auth*test?"
        let sanitized = PathHasher.sanitizeBranchForFolderName(branch)
        
        #expect(!sanitized.contains("/"))
        #expect(!sanitized.contains(":"))
        #expect(!sanitized.contains("?"))
        #expect(sanitized == "feat-user-auth-test")
    }

    @Test("Uji resolusi direktori isolasi data lengkap")
    func testDataDirectoryResolution() {
        let workspace = URL(fileURLWithPath: "/Users/raka/Projects/backend")
        let resolved = PathHasher.resolveDataDirectory(for: workspace, branch: "feat/oauth")
        
        #expect(resolved.path.contains(".antigravity-multi"))
        #expect(resolved.lastPathComponent.contains("backend_feat-oauth"))
    }

    // MARK: - 2. Launcher Port Allocation Tests
    @Test("Uji port scanner mengembalikan port valid antara 9221-9229")
    func testAvailablePortFinder() async {
        let launcher = LauncherService()
        let port = await launcher.findAvailablePort(startPort: 9221, endPort: 9229)
        
        #expect(port >= 9221)
        #expect(port <= 9229)
    }

    // MARK: - 3. CDP Extracted State Decoder Tests
    @Test("Uji JSON decoding untuk CDPExtractedState")
    func testCDPExtractedStateDecoding() throws {
        let jsonString = """
        {
            "workspaceName": "ProjectAlpha",
            "state": "RUNNING",
            "headline": "Analyzing auth.ts",
            "subSteps": ["Models/User.swift", "Controllers/Auth.swift"],
            "timestamp": 1726034500000
        }
        """
        
        let data = try #require(jsonString.data(using: .utf8))
        let decoded = try JSONDecoder().decode(CDPExtractedState.self, from: data)
        
        #expect(decoded.workspaceName == "ProjectAlpha")
        #expect(decoded.state == "RUNNING")
        #expect(decoded.headline == "Analyzing auth.ts")
        #expect(decoded.subSteps.count == 2)
    }
}
```

---

## 2. Cara Menjalankan Test
1. Buka project di Xcode (`Anti Dispatch.xcodeproj`).
2. Tekan pintasan **⌘ + U** (*Command + U*) atau klik tombol *Play* di samping `@Suite`.
3. Seluruh assertions `#expect` akan dievaluasi secara konkuren dan cepat dengan output laporan grafis di Xcode Test Navigator.
