---
name: rn-guidelines
description: AI駆動React Native開発ガイドライン。LLMコーディングアシスタントの典型的な問題と対策、禁止行動、ベストプラクティスを定義。
---

# AI駆動React Native開発ガイドライン

AI（LLM）を活用したReact Native CLI開発で発生しやすい問題と、その対策を定義する。

> **Note:** ExpoではなくReact Native CLI（ベアワークフロー）向けのガイドライン

---

## 🚫 禁止行動（MUST NOT）

### 1. 簡易実装への逃げ
```
❌ 禁止: エラーが困難だからと言って、簡易的な処理で完了とする
❌ 禁止: ネイティブモジュール連携を諦めてモックで代替する

✅ 必須: 困難な場合はユーザーに相談し、代替案を提示する
```

### 2. ビルド検証の省略【重要】
```
❌ 禁止: TypeScript型チェックのみで完了とする
❌ 禁止: ネイティブリンク後にビルド検証をスキップする
❌ 禁止: Metro Bundlerだけで動作確認とする

✅ 必須: Android: npx react-native run-android でビルド・起動確認
✅ 必須: iOS: cd ios && pod install && cd .. && npx react-native run-ios
✅ 必須: リリースビルドも確認
```

### 3. ネイティブ設定の無視
```
❌ 禁止: android/ や ios/ の設定ファイルを無視する
❌ 禁止: pod install を忘れる
❌ 禁止: ネイティブモジュールのリンク設定を確認しない

✅ 必須: ネイティブモジュール追加時は必ず設定確認
✅ 必須: iOS: Podfile, Info.plist
✅ 必須: Android: build.gradle, AndroidManifest.xml
```

### 4. パッケージ互換性確認の省略
```
❌ 禁止: React Nativeバージョンとの互換性を確認しない
❌ 禁止: auto-linkingが効かないモジュールを見落とす

✅ 必須: パッケージのREADMEでRNバージョン要件確認
✅ 必須: ネイティブリンクが必要か確認
```

---

## ⚠️ 注意すべき問題と対策

### 1. ネイティブモジュールリンク

**問題:** 自動リンクが効かないケースがある

**対策:**
```bash
# iOS
cd ios && pod install && cd ..

# 手動リンクが必要な場合
npx react-native link [package-name]
```

### 2. Gradleビルドエラー

**問題:** Android依存関係の競合

**対策:**
```groovy
// android/app/build.gradle
android {
    compileSdkVersion 34
    
    defaultConfig {
        minSdkVersion 24
        targetSdkVersion 34
    }
}

// 依存関係強制
configurations.all {
    resolutionStrategy {
        force 'com.facebook.react:react-native:0.76.0'
    }
}
```

### 3. Metro Bundler問題

**症状:**
```
Unable to resolve module
```

**解決:**
```bash
# キャッシュクリア
npx react-native start --reset-cache

# 完全リセット
rm -rf node_modules
rm -rf ios/Pods ios/Podfile.lock
npm install
cd ios && pod install
```

### 4. 非推奨API

**チェックすべき非推奨パターン:**
| 非推奨 | 現在の推奨 | 備考 |
|-------|-----------|------|
| `AsyncStorage` (community) | `@react-native-async-storage/async-storage` | パッケージ移行 |
| `NetInfo` (core) | `@react-native-community/netinfo` | パッケージ移行 |
| `ListView` | `FlatList` / `SectionList` | RN 0.60+ |
| `componentWillMount` | `useEffect` | React 18+ |

---

## ✅ 必須プラクティス

### 作業前確認
1. React Nativeバージョンを確認（package.json）
2. android/ と ios/ の設定を確認
3. 既存のネイティブモジュールを確認

### 作業中
1. パッケージ追加後は必ず `cd ios && pod install`
2. ネイティブ変更後はビルド確認
3. 不明点はユーザーに質問

### 作業後
1. 全エラー・警告を解消
2. 両プラットフォームでビルド確認
3. 削除した機能があれば報告

---

## クイックリファレンス

### 現在の推奨バージョン（2026年2月）
| パッケージ | 推奨バージョン |
|-----------|---------------|
| React Native | 0.76+ |
| React | 18.3+ |
| TypeScript | 5.3+ |
| @react-navigation/native | 7.x |
| zustand | 5.x |

### 禁止チェックリスト
- [ ] 簡易実装で完了としていない
- [ ] TypeScriptエラーが残っていない
- [ ] pod install を実行した
- [ ] Android/iOSビルド確認した
- [ ] ネイティブ設定を確認した
