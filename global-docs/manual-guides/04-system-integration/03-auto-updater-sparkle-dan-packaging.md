# Modul 04.03: Auto-Updater Sparkle 2 & Packaging

Dokumen ini memandu integrasi framework pembaruan otomatis **Sparkle 2** dan pembuatan formula distribusi **Homebrew Cask**.

---

## 1. Integrasi Sparkle 2 via Swift Package Manager (SPM)

1. Di Xcode, pilih **File** -> **Add Package Dependencies...**
2. Masukkan URL repositori Sparkle:
   ```
   https://github.com/sparkle-project/Sparkle
   ```
3. Pilih version rule: `Up to Next Major` (versi 2.x).
4. Tambahkan produk `Sparkle` ke Target **Anti Dispatch**.

---

## 2. Inisialisasi Updater Controller

Di dalam `AppDelegate.swift` atau modul updater:

```swift
import Foundation
import Sparkle

public final class AppUpdater {
    public static let shared = AppUpdater()
    public let updaterController: SPUStandardUpdaterController
    
    private init() {
        self.updaterController = SPUStandardUpdaterController(
            startingUpdater: true,
            updaterDelegate: nil,
            userDriverDelegate: nil
        )
    }
    
    public func checkForUpdates() {
        updaterController.checkForUpdates(nil)
    }
}
```

Tambahkan kunci `SUFeedURL` pada `Info.plist`:
```xml
<key>SUFeedURL</key>
<string>https://raw.githubusercontent.com/rakafebriansy/anti-dispatch-mac/main/appcast.xml</string>
<key>SUPublicEDKey</key>
<string>YOUR_PUBLIC_ED25519_KEY_HERE</string>
```

---

## 3. Pembuatan Formula Homebrew Cask

Buat file formula `Casks/anti-notch.rb` pada repositori Homebrew tap Anda:

```ruby
cask "anti-notch" do
  version "1.0.0"
  sha256 "CHECKSUM_OF_DMG_HERE"

  url "https://github.com/rakafebriansy/anti-dispatch-mac/releases/download/v#{version}/Anti-Dispatch-#{version}.dmg"
  name "Anti Dispatch"
  desc "Dynamic Island & Workspace Orchestrator for Antigravity IDE"
  homepage "https://github.com/rakafebriansy/anti-dispatch-mac"

  depends_on macos: ">= :sonoma"

  app "Anti Dispatch.app"

  zap trash: [
    "~/.antigravity-multi",
    "~/Library/Preferences/com.antigravity.anti-dispatch.plist",
  ]
end
```

Pengguna dapat menginstal dengan perintah:
```bash
brew install --cask anti-notch
```
