---
name: expo-guidelines
description: AI駆動Expo開発ガイドライン。LLMコーディングアシスタントの典型的な問題と対策、禁止行動、ベストプラクティスを定義。
---

# AI駆動Expo開発ガイドライン

AI（LLM）を活用したExpo開発で発生しやすい問題と、その対策を定義する。

---

## 🚫 禁止行動（MUST NOT）

### 1. 簡易実装への逃げ
```
❌ 禁止: エラーが困難だからと言って、簡易的な処理で完了とする
❌ 禁止: 複雑な処理をスキップして「TODO: 後で実装」で済ませる

✅ 必須: 困難な場合はユーザーに相談し、代替案を提示する
```

### 2. モック・スタブでの代替
```
❌ 禁止: 本番コードをモックで置き換えて完了とする
❌ 禁止: API未実装を理由にハードコード値で代替

✅ 必須: 実際の実装を行う
```

### 3. 無断のコード削除・変更
```
❌ 禁止: 理解できないコードを「不要」と判断して削除する
❌ 禁止: 既存機能を無断で削除・変更する

✅ 必須: 削除前にユーザーに確認する
```

### 4. エラー・警告の無視
```
❌ 禁止: TypeScriptエラーを放置する
❌ 禁止: ESLint警告を無視する
❌ 禁止: コンソール警告を無視してpushする

✅ 必須: 全エラー・警告を解消してから完了とする
```

### 5. ビルド検証の省略【重要】
```
❌ 禁止: TypeScript型チェックのみで完了とする
❌ 禁止: ネイティブモジュール導入後にビルド検証をスキップする

✅ 必須: 実機/シミュレータでの動作確認
✅ 必須: expo start でエラーなく起動することを確認
✅ 必須: EAS Build実行前にローカルビルド確認
```

### 6. パッケージ互換性確認の省略
```
❌ 禁止: パッケージを追加する際にExpo SDK互換性を確認しない
❌ 禁止: expo installではなくnpm/yarn installを使用する

✅ 必須: expo install を使用してSDK互換パッケージをインストール
✅ 必須: ネイティブモジュールはExpo対応版を確認
```

---

## ⚠️ 注意すべき問題と対策

### 1. Expo SDK互換性

**問題:** 非対応パッケージを導入してビルド失敗

**対策:**
- `expo install` でパッケージをインストール
- [Expo Directory](https://docs.expo.dev/versions/latest/) で互換性確認
- ネイティブモジュールはEAS Build必須

```bash
# ✅ 正しい
expo install react-native-reanimated

# ❌ 間違い
npm install react-native-reanimated
```

### 2. 古いAPI使用

**問題:** 学習データに含まれる古いExpo APIを使用

**対策:**
- 現在の日付: 2026年2月
- Expo SDK 52+を前提とする
- 非推奨APIを使わない

**チェックすべき非推奨パターン:**
| 非推奨 | 現在の推奨 | 備考 |
|-------|-----------|------|
| `expo-app-loading` | `expo-splash-screen` | SDK 46+ |
| `expo-permissions` | 各パッケージ内の permissions | SDK 41+ |
| `Notifications.createChannelAndroidAsync` | `setNotificationChannelAsync` | SDK 44+ |
| `AppLoading` component | `SplashScreen.preventAutoHideAsync()` | SDK 46+ |

### 3. Navigation設定ミス

**問題:** React Navigation設定の誤り

**対策:**
```tsx
// ✅ 正しい: Expo Router使用（推奨）
// app/_layout.tsx
import { Stack } from 'expo-router';

export default function Layout() {
  return <Stack />;
}

// ❌ 古い: React Navigation直接使用（非推奨）
```

### 4. 状態管理

**推奨パターン:**
| ライブラリ | 用途 |
|-----------|------|
| Zustand | シンプルな状態管理（推奨） |
| TanStack Query | サーバー状態管理 |
| Redux Toolkit | 大規模アプリ |

```tsx
// ✅ Zustand例
import { create } from 'zustand';

interface AuthState {
  user: User | null;
  login: (user: User) => void;
  logout: () => void;
}

export const useAuthStore = create<AuthState>((set) => ({
  user: null,
  login: (user) => set({ user }),
  logout: () => set({ user: null }),
}));
```

---

## ✅ 必須プラクティス

### 作業前確認
1. Expo SDKバージョンを確認（app.json）
2. 既存のパッケージ構成を確認
3. TypeScript設定を確認

### 作業中
1. 各変更後に`npx tsc --noEmit`を実行
2. `expo start`でエラーなく起動することを確認
3. 不明点はユーザーに質問

### 作業後
1. 全エラー・警告を解消
2. 削除した機能があれば報告
3. 制限事項・注意点を明示

---

## クイックリファレンス

### 現在の推奨バージョン（2026年2月）
| パッケージ | 推奨バージョン |
|-----------|---------------|
| Expo SDK | 52+ |
| React Native | 0.76+ |
| TypeScript | 5.3+ |
| expo-router | 4.x |
| zustand | 5.x |
| @tanstack/react-query | 5.x |

### 禁止チェックリスト
- [ ] 簡易実装で完了としていない
- [ ] モックで本番コードを代替していない
- [ ] コードを無断削除していない
- [ ] TypeScriptエラーが残っていない
- [ ] expo install を使用した
- [ ] 実機/シミュレータで動作確認した
