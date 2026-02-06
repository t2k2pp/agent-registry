---
name: expo-environment-check
description: Expo開発環境診断スキル。Node.js、Expo CLI、EAS CLI、シミュレータ確認を支援。
---

# Expo 開発環境診断スキル

プロジェクト初期化時およびビルド問題発生時に、開発環境の整合性を確認する。

---

## 🔍 診断タイミング

| タイミング | 必須診断項目 |
|-----------|-------------|
| プロジェクト新規作成時 | Node.js, Expo CLI確認 |
| EAS Build設定時 | EAS CLI, eas.json確認 |
| ビルドエラー発生時 | 詳細環境診断 |
| SDK アップグレード時 | 全体再診断 |

---

## 1. 基本診断コマンド

### Node.js / npm
```bash
node --version   # 推奨: 20.x以上
npm --version    # 推奨: 10.x以上
```

### Expo CLI
```bash
npx expo --version   # 最新版推奨
```

### EAS CLI
```bash
eas --version        # 推奨: 12.x以上
eas whoami           # ログイン確認
```

### 一括診断
```bash
npx expo doctor
```

---

## 2. プロジェクト設定確認

### app.json / app.config.js
```json
{
  "expo": {
    "name": "MyApp",
    "slug": "my-app",
    "version": "1.0.0",
    "sdkVersion": "52.0.0",
    "platforms": ["ios", "android"],
    "ios": {
      "bundleIdentifier": "com.example.myapp",
      "supportsTablet": true
    },
    "android": {
      "package": "com.example.myapp",
      "adaptiveIcon": {
        "foregroundImage": "./assets/adaptive-icon.png"
      }
    }
  }
}
```

### eas.json
```json
{
  "cli": {
    "version": ">= 12.0.0"
  },
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal"
    },
    "preview": {
      "distribution": "internal",
      "android": {
        "buildType": "apk"
      }
    },
    "production": {}
  }
}
```

---

## 3. iOS環境確認（macOS）

### Xcode
```bash
xcode-select --version
xcodebuild -version
```

### シミュレータ
```bash
xcrun simctl list devices available
```

### CocoaPods
```bash
pod --version   # 推奨: 1.14+
```

---

## 4. Android環境確認

### Android Studio
```bash
# 環境変数確認
echo $ANDROID_HOME
# または Windows
echo %ANDROID_HOME%
```

### エミュレータ
```bash
emulator -list-avds
```

---

## 5. よくある問題と解決策

### Metro Bundlerエラー

**症状:**
```
Unable to resolve module
```

**解決:**
```bash
npx expo start --clear
# または
rm -rf node_modules && npm install
```

### EAS Buildエラー

**症状:**
```
Build failed: Unable to resolve dependencies
```

**解決:**
```bash
# キャッシュクリア
eas build --clear-cache

# ローカル確認
npx expo prebuild --clean
```

### iOS Podエラー

**症状:**
```
[!] CocoaPods could not find compatible versions
```

**解決:**
```bash
cd ios
rm -rf Pods Podfile.lock
pod install --repo-update
```

### Expo SDK互換性エラー

**症状:**
```
This package is not compatible with your SDK version
```

**解決:**
```bash
# パッケージをSDK互換版にダウングレード
expo install [package-name]

# SDKをアップグレード
expo upgrade
```

---

## 6. 環境診断スクリプト

### 一括診断（PowerShell）
```powershell
Write-Host "=== Expo Environment Check ===" -ForegroundColor Cyan

# Node.js
Write-Host "`n=== Node.js ===" -ForegroundColor Cyan
node --version
npm --version

# Expo
Write-Host "`n=== Expo ===" -ForegroundColor Cyan
npx expo --version

# EAS
Write-Host "`n=== EAS ===" -ForegroundColor Cyan
eas --version

# Expo Doctor
Write-Host "`n=== Expo Doctor ===" -ForegroundColor Cyan
npx expo doctor
```

### 一括診断（Bash/Zsh）
```bash
echo "=== Expo Environment Check ==="

echo "\n=== Node.js ==="
node --version
npm --version

echo "\n=== Expo ==="
npx expo --version

echo "\n=== EAS ==="
eas --version

echo "\n=== Expo Doctor ==="
npx expo doctor
```

---

## 7. 推奨環境（2026年2月時点）

| 項目 | 推奨バージョン |
|------|---------------|
| Node.js | 20.x LTS |
| npm | 10.x |
| Expo SDK | 52+ |
| EAS CLI | 12.x+ |
| Xcode | 15.0+ |
| Android Studio | 2024.x+ |

---

## チェックリスト

### 新規プロジェクト開始時
- [ ] Node.js 20.x 以上
- [ ] `npx create-expo-app` で作成
- [ ] `npx expo doctor` で問題なし
- [ ] EAS CLIでログイン済み

### EAS Build前
- [ ] eas.json設定確認
- [ ] app.json/app.config.js確認
- [ ] `npx expo prebuild` 成功
- [ ] ネイティブモジュール互換性確認
