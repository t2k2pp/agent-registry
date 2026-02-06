---
description: React Native CLI開発フロー全体。設計→環境確認→実装→テスト→ビルド→リリースの流れ。
---

# React Native CLI開発フロー

## フェーズ概要

```
Phase 1: DESIGN
    ↓
Phase 2: ENVIRONMENT_CHECK
    ↓
Phase 3: IMPLEMENT (rn-developer)
    ↓
Phase 4: TEST
    ↓
Phase 5: BUILD
    ↓
Phase 6: RELEASE
```

---

## Phase 1: 設計

### 成果物
- `docs/design/[feature]-design.md` - 機能設計書
- 画面遷移図

### 完了条件
- [ ] 機能要件が定義されている
- [ ] ネイティブモジュール要件が確認されている
- [ ] 画面設計が完了している

---

## Phase 2: 環境チェック

### 参照スキル
- `rn-environment-check`

### チェック項目
- [ ] `npx react-native doctor` で問題なし
- [ ] Node.js 20.x 以上
- [ ] Java 17 インストール済み
- [ ] Xcode/CocoaPods確認（iOS）
- [ ] Android SDK/Emulator確認

---

## Phase 3: 実装

### 実行エージェント
- `rn-developer`

### 参照スキル
- `rn-guidelines`（必須）
- `rn-development`

### 成果物
- `src/` - アプリケーションコード
- `android/` - Android設定（必要時）
- `ios/` - iOS設定（必要時）

### 完了条件
- [ ] 設計書に基づき実装完了
- [ ] `npx tsc --noEmit` エラーなし
- [ ] ネイティブモジュール追加後 pod install
- [ ] Androidビルド成功
- [ ] iOSビルド成功

---

## Phase 4: テスト

### ツール
- Jest + React Native Testing Library

### 完了条件
- [ ] ユニットテスト作成
- [ ] 全テスト通過

---

## Phase 5: ビルド

### Android
```bash
# デバッグ
npx react-native run-android

# リリース AAB
cd android
./gradlew bundleRelease
```

### iOS
```bash
# デバッグ
npx react-native run-ios

# リリース
# Xcode → Product → Archive → Distribute
```

### 完了条件
- [ ] デバッグビルド成功
- [ ] リリースビルド成功
- [ ] 実機テスト完了

---

## Phase 6: リリース

### Play Store
```bash
cd android
./gradlew bundleRelease
# Google Play Console でアップロード
```

### App Store
- Xcode → Archive → Submit to App Store

### チェックリスト
- [ ] バージョン番号更新
- [ ] CHANGELOG更新
- [ ] ストアメタデータ更新
