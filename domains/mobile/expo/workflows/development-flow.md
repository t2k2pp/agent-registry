---
description: Expo開発フロー全体。設計→実装→テスト→ビルド→リリースの流れ。
---

# Expo開発フロー

## フェーズ概要

```
Phase 1: DESIGN
    ↓
Phase 2: ENVIRONMENT_CHECK ← 初回/依存追加時
    ↓
Phase 3: IMPLEMENT (expo-developer)
    ↓
Phase 4: TEST
    ↓
Phase 5: BUILD (EAS Build)
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
- [ ] 画面設計が完了している
- [ ] API設計が完了している

---

## Phase 2: 環境チェック

### 参照スキル
- `expo-environment-check`

### チェック項目
- [ ] `npx expo doctor` で問題なし
- [ ] Node.js 20.x 以上
- [ ] Expo SDK バージョン確認
- [ ] 追加パッケージのSDK互換性確認

---

## Phase 3: 実装

### 実行エージェント
- `expo-developer`

### 参照スキル
- `expo-guidelines`（必須）
- `expo-development`

### 成果物
- `app/` - 画面コンポーネント
- `components/` - UIコンポーネント
- `lib/` - ビジネスロジック

### 完了条件
- [ ] 設計書に基づき実装完了
- [ ] `npx tsc --noEmit` エラーなし
- [ ] ESLint警告なし
- [ ] `expo start` で起動確認
- [ ] 実機/シミュレータで動作確認

---

## Phase 4: テスト

### ツール
- Jest + React Native Testing Library

### 成果物
- `__tests__/` - テストファイル

### 完了条件
- [ ] ユニットテストカバレッジ 80%+
- [ ] コンポーネントテスト作成
- [ ] 全テスト通過

---

## Phase 5: ビルド（EAS Build）

### コマンド
```bash
# 開発ビルド
eas build --profile development --platform all

# プレビュービルド（内部配布）
eas build --profile preview --platform all

# 本番ビルド
eas build --profile production --platform all
```

### 完了条件
- [ ] ビルド成功
- [ ] インストール・起動確認

---

## Phase 6: リリース

### OTA更新（ストア審査不要な変更）
```bash
eas update --branch production --message "説明"
```

### ストア提出
```bash
eas submit --platform ios
eas submit --platform android
```

### チェックリスト
- [ ] バージョン番号更新
- [ ] CHANGELOG更新
- [ ] ストアメタデータ更新
