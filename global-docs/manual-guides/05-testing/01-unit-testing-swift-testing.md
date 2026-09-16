# Module 05.01: Unit Testing with Swift Testing

This document guides the creation of a modern unit test suite using Apple's official **Swift Testing** framework (`import Testing` / `@Test`) to validate the core engine functionalities of Anti Dispatch.

---

## 1. Complete Test Suite Code (`Anti_DispatchTests.swift`)

Save this code in the directory `Anti DispatchTests/Anti_DispatchTests.swift`:

```swift
import Testing
import Foundation
@testable import Anti_Dispatch

@Suite("Anti Dispatch Core Engine Tests")
struct AntiDispatchCoreTests {

    // MARK: - 1. PathHasher Tests
    @Test("Verify 6-character MD5 short hash computation")
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

    @Test("Verify branch name sanitization for worktree folder naming")
    func testBranchSanitization() {
        let branch = "feat/user:auth*test?"
        let sanitized = PathHasher.sanitizeBranchForFolderName(branch)
        
        #expect(!sanitized.contains("/"))
        #expect(!sanitized.contains(":"))
        #expect(!sanitized.contains("?"))
        #expect(sanitized == "feat-user-auth-test")
    }

    @Test("Verify complete isolated data directory resolution")
    func testDataDirectoryResolution() {
        let workspace = URL(fileURLWithPath: "/Users/raka/Projects/backend")
        let resolved = PathHasher.resolveDataDirectory(for: workspace, branch: "feat/oauth")
        
        #expect(resolved.path.contains(".antigravity-multi"))
        #expect(resolved.lastPathComponent.contains("backend_feat-oauth"))
    }

    // MARK: - 2. Launcher Port Allocation Tests
    @Test("Verify port scanner returns valid port in 9221-9229 range")
    func testAvailablePortFinder() async {
        let launcher = LauncherService()
        let port = await launcher.findAvailablePort(startPort: 9221, endPort: 9229)
        
        #expect(port >= 9221)
        #expect(port <= 9229)
    }

    // MARK: - 3. CDP Extracted State Decoder Tests
    @Test("Verify JSON decoding for CDPExtractedState")
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

## 2. Running the Tests
1. Open the project in Xcode (`Anti Dispatch.xcodeproj`).
2. Press **⌘ + U** (*Command + U*) or click the *Play* icon next to `@Suite`.
3. All `#expect` assertions will be evaluated concurrently with rich graphical feedback in Xcode's Test Navigator.
