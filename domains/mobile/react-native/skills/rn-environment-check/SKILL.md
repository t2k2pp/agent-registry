---
name: rn-environment-check
description: React Native CLI開発環境診断スキル。Node.js、Xcode、Android Studio、CocoaPods確認を支援。
---

# React Native CLI 開発環境診断スキル

React Native CLI（ベアワークフロー）の開発環境を確認する。

---

## 🔍 診断タイミング

| タイミング | 必須診断項目 |
|-----------|-------------|
| プロジェクト新規作成時 | 全環境確認 |
| ネイティブモジュール追加時 | ビルド環境確認 |
| ビルドエラー発生時 | 詳細診断 |
| RNアップグレード時 | 全体再診断 |

---

## 1. 基本診断コマンド

### React Native Doctor
```bash
npx react-native doctor
```

### Node.js / npm
```bash
node --version   # 推奨: 20.x以上
npm --version    # 推奨: 10.x以上
```

### Watchman（推奨）
```bash
watchman --version
```

---

## 2. iOS環境確認（macOS）

### Xcode
```bash
xcodebuild -version    # 推奨: 15.0+
xcode-select --print-path
```

### CocoaPods
```bash
pod --version          # 推奨: 1.14+
gem which cocoapods
```

### Ruby（CocoaPods用）
```bash
ruby --version         # 推奨: 3.0+
```

### Simulator
```bash
xcrun simctl list devices available
```

---

## 3. Android環境確認

### 環境変数
```bash
# macOS/Linux
echo $ANDROID_HOME
echo $JAVA_HOME

# Windows
echo %ANDROID_HOME%
echo %JAVA_HOME%
```

### Java
```bash
java -version          # 推奨: OpenJDK 17
```

### Gradle
```bash
./gradlew --version    # android/ ディレクトリで実行
```

### Emulator
```bash
emulator -list-avds
```

---

## 4. プロジェクト設定確認

### package.json
```json
{
  "dependencies": {
    "react": "18.3.x",
    "react-native": "0.76.x"
  }
}
```

### android/build.gradle
```groovy
buildscript {
    ext {
        buildToolsVersion = "34.0.0"
        minSdkVersion = 24
        compileSdkVersion = 34
        targetSdkVersion = 34
        kotlinVersion = "1.9.22"
    }
}
```

### ios/Podfile
```ruby
platform :ios, '13.4'
```

---

## 5. よくある問題と解決策

### Metro Bundlerエラー

**症状:**
```
error Unable to resolve module
```

**解決:**
```bash
npx react-native start --reset-cache
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
pod repo update
pod install
```

### Androidビルドエラー

**症状:**
```
Could not determine the dependencies of task ':app:compileDebugJavaWithJavac'
```

**解決:**
```bash
cd android
./gradlew clean
./gradlew --stop
cd ..
npx react-native run-android
```

### JDKバージョンエラー

**症状:**
```
Unsupported class file major version
```

**解決:**
- JDK 17をインストール
- JAVA_HOMEを設定

---

## 6. 環境診断スクリプト

### PowerShell（Windows）
```powershell
Write-Host "=== React Native Environment Check ===" -ForegroundColor Cyan

Write-Host "`n=== Node.js ===" -ForegroundColor Cyan
node --version
npm --version

Write-Host "`n=== Java ===" -ForegroundColor Cyan
java -version

Write-Host "`n=== Android ===" -ForegroundColor Cyan
Write-Host "ANDROID_HOME: $env:ANDROID_HOME"

Write-Host "`n=== React Native Doctor ===" -ForegroundColor Cyan
npx react-native doctor
```

### Bash/Zsh（macOS/Linux）
```bash
echo "=== React Native Environment Check ==="

echo "\n=== Node.js ==="
node --version
npm --version

echo "\n=== Xcode ==="
xcodebuild -version

echo "\n=== CocoaPods ==="
pod --version

echo "\n=== Java ==="
java -version

echo "\n=== React Native Doctor ==="
npx react-native doctor
```

---

## 7. 推奨環境（2026年2月時点）

| 項目 | 推奨バージョン |
|------|---------------|
| Node.js | 20.x LTS |
| npm | 10.x |
| React Native | 0.76+ |
| Xcode | 15.0+ |
| CocoaPods | 1.14+ |
| Java | OpenJDK 17 |
| Android Studio | 2024.x+ |
| Android SDK | 34 |

---

## チェックリスト

### 新規プロジェクト開始時
- [ ] `npx react-native doctor` で問題なし
- [ ] Node.js 20.x 以上
- [ ] Java 17 インストール済み
- [ ] ANDROID_HOME設定済み

### ネイティブモジュール追加時
- [ ] `npm install` 完了
- [ ] `cd ios && pod install` 完了
- [ ] Androidビルド成功
- [ ] iOSビルド成功
