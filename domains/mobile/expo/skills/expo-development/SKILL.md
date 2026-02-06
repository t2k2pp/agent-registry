---
name: expo-development
description: Expo開発スキル。Expo Router、状態管理（Zustand）、UIコンポーネント実装、EAS Build/Updateを支援。
---

# Expo 開発スキル

Expo SDKを使用したモバイルアプリ開発のベストプラクティス。

---

## 🏗️ プロジェクト構造

### 推奨ディレクトリ構成（Expo Router）
```
app/
├── (tabs)/                 # タブナビゲーション
│   ├── _layout.tsx
│   ├── index.tsx          # ホーム
│   └── settings.tsx       # 設定
├── (auth)/                # 認証フロー
│   ├── _layout.tsx
│   ├── login.tsx
│   └── register.tsx
├── _layout.tsx            # ルートレイアウト
└── +not-found.tsx         # 404

components/
├── ui/                    # 汎用コンポーネント
├── forms/                 # フォームコンポーネント
└── [feature]/             # 機能別

lib/
├── api/                   # API通信
├── stores/                # Zustand stores
├── hooks/                 # カスタムhooks
└── utils/                 # ユーティリティ

assets/
├── images/
├── fonts/
└── icons/
```

---

## 🧭 ナビゲーション（Expo Router）

### 基本設定
```tsx
// app/_layout.tsx
import { Stack } from 'expo-router';
import { StatusBar } from 'expo-status-bar';

export default function RootLayout() {
  return (
    <>
      <StatusBar style="auto" />
      <Stack>
        <Stack.Screen name="(tabs)" options={{ headerShown: false }} />
        <Stack.Screen name="(auth)" options={{ headerShown: false }} />
      </Stack>
    </>
  );
}
```

### タブナビゲーション
```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { Ionicons } from '@expo/vector-icons';

export default function TabLayout() {
  return (
    <Tabs>
      <Tabs.Screen
        name="index"
        options={{
          title: 'ホーム',
          tabBarIcon: ({ color, size }) => (
            <Ionicons name="home" size={size} color={color} />
          ),
        }}
      />
    </Tabs>
  );
}
```

---

## 🔄 状態管理（Zustand）

### Store定義
```tsx
// lib/stores/authStore.ts
import { create } from 'zustand';
import { persist, createJSONStorage } from 'zustand/middleware';
import AsyncStorage from '@react-native-async-storage/async-storage';

interface User {
  id: string;
  email: string;
  name: string;
}

interface AuthState {
  user: User | null;
  token: string | null;
  isLoading: boolean;
  
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set, get) => ({
      user: null,
      token: null,
      isLoading: false,

      login: async (email, password) => {
        set({ isLoading: true });
        try {
          const response = await api.login(email, password);
          set({ user: response.user, token: response.token });
        } finally {
          set({ isLoading: false });
        }
      },

      logout: () => {
        set({ user: null, token: null });
      },
    }),
    {
      name: 'auth-storage',
      storage: createJSONStorage(() => AsyncStorage),
    }
  )
);
```

### 使用方法
```tsx
// コンポーネントで使用
import { useAuthStore } from '@/lib/stores/authStore';

export default function ProfileScreen() {
  const { user, logout } = useAuthStore();
  
  return (
    <View>
      <Text>{user?.name}</Text>
      <Button title="ログアウト" onPress={logout} />
    </View>
  );
}
```

---

## 🌐 API通信

### TanStack Query設定
```tsx
// lib/api/queryClient.ts
import { QueryClient } from '@tanstack/react-query';

export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60 * 5, // 5分
      gcTime: 1000 * 60 * 30,   // 30分
      retry: 2,
    },
  },
});
```

### API Hooks
```tsx
// lib/api/hooks/useUsers.ts
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { api } from '../client';

export function useUsers() {
  return useQuery({
    queryKey: ['users'],
    queryFn: () => api.getUsers(),
  });
}

export function useCreateUser() {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: api.createUser,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
    },
  });
}
```

---

## 📱 UIコンポーネント

### 推奨ライブラリ
| ライブラリ | 用途 |
|-----------|------|
| NativeWind | TailwindCSS in RN |
| Tamagui | 高速UIキット |
| React Native Paper | Material Design |
| Gluestack UI | アクセシブルUI |

### NativeWind例
```tsx
// tailwind.config.js
module.exports = {
  content: ['./app/**/*.tsx', './components/**/*.tsx'],
  theme: { extend: {} },
  plugins: [],
};

// コンポーネント
import { View, Text, Pressable } from 'react-native';

export function Button({ title, onPress }) {
  return (
    <Pressable 
      className="bg-blue-500 px-4 py-2 rounded-lg active:bg-blue-600"
      onPress={onPress}
    >
      <Text className="text-white font-semibold text-center">{title}</Text>
    </Pressable>
  );
}
```

---

## 🚀 EAS Build / Update

### 初期設定
```bash
# EAS CLI インストール
npm install -g eas-cli

# プロジェクト設定
eas build:configure

# ビルドプロファイル確認
cat eas.json
```

### eas.json設定
```json
{
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal"
    },
    "preview": {
      "distribution": "internal"
    },
    "production": {}
  },
  "submit": {
    "production": {}
  }
}
```

### OTA更新（EAS Update）
```bash
# 更新をプッシュ
eas update --branch production --message "バグ修正"

# ブランチ一覧
eas update:list
```

---

## チェックリスト

### 実装時
- [ ] Expo Router使用
- [ ] TypeScript厳格モード
- [ ] Zustand/TanStack Query使用
- [ ] expo install でパッケージ追加

### ビルド前
- [ ] `npx tsc --noEmit` 通過
- [ ] `expo start` で起動確認
- [ ] 実機/シミュレータで動作確認
