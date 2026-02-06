---
name: rn-development
description: React Native CLI開発スキル。ネイティブモジュール連携、React Navigation、状態管理を支援。
---

# React Native CLI 開発スキル

React Native CLI（ベアワークフロー）を使用したモバイルアプリ開発のベストプラクティス。

> **Note:** Expoではなく、ネイティブコードへの直接アクセスが必要なプロジェクト向け

---

## 🏗️ プロジェクト構造

### 推奨ディレクトリ構成
```
src/
├── screens/               # 画面コンポーネント
│   ├── Home/
│   │   ├── index.tsx
│   │   └── styles.ts
│   └── Settings/
├── components/            # 共通コンポーネント
│   ├── ui/
│   └── forms/
├── navigation/            # ナビゲーション設定
│   ├── AppNavigator.tsx
│   └── types.ts
├── lib/                   # ビジネスロジック
│   ├── api/
│   ├── stores/
│   └── hooks/
├── native/                # ネイティブモジュール
│   └── bridges/
└── utils/

android/                   # Androidネイティブ
ios/                       # iOSネイティブ
```

---

## 🧭 ナビゲーション（React Navigation）

### 基本設定
```tsx
// src/navigation/AppNavigator.tsx
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

const Stack = createNativeStackNavigator<RootStackParamList>();
const Tab = createBottomTabNavigator<TabParamList>();

function TabNavigator() {
  return (
    <Tab.Navigator>
      <Tab.Screen name="Home" component={HomeScreen} />
      <Tab.Screen name="Settings" component={SettingsScreen} />
    </Tab.Navigator>
  );
}

export function AppNavigator() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen 
          name="Main" 
          component={TabNavigator} 
          options={{ headerShown: false }}
        />
        <Stack.Screen name="Detail" component={DetailScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

### 型定義
```tsx
// src/navigation/types.ts
import { NativeStackScreenProps } from '@react-navigation/native-stack';

export type RootStackParamList = {
  Main: undefined;
  Detail: { id: string };
};

export type DetailScreenProps = NativeStackScreenProps<
  RootStackParamList, 
  'Detail'
>;
```

---

## 🔧 ネイティブモジュール連携

### TurboModule（New Architecture）
```tsx
// src/native/bridges/MyModule.ts
import { TurboModuleRegistry } from 'react-native';

interface MyModuleSpec {
  multiply(a: number, b: number): Promise<number>;
  getDeviceInfo(): Promise<DeviceInfo>;
}

export const MyModule = TurboModuleRegistry.getEnforcing<MyModuleSpec>('MyModule');
```

### ネイティブモジュール追加手順

1. パッケージインストール
```bash
npm install react-native-[module]
```

2. iOSリンク
```bash
cd ios
pod install
cd ..
```

3. Android設定（必要な場合）
```groovy
// android/app/build.gradle
dependencies {
    implementation project(':react-native-[module]')
}
```

4. ビルド確認
```bash
npx react-native run-android
npx react-native run-ios
```

---

## 🔄 状態管理

### Zustand（推奨）
```tsx
// src/lib/stores/authStore.ts
import { create } from 'zustand';
import { persist, createJSONStorage } from 'zustand/middleware';
import AsyncStorage from '@react-native-async-storage/async-storage';

interface AuthState {
  user: User | null;
  token: string | null;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      token: null,
      login: async (email, password) => {
        const response = await api.login(email, password);
        set({ user: response.user, token: response.token });
      },
      logout: () => set({ user: null, token: null }),
    }),
    {
      name: 'auth-storage',
      storage: createJSONStorage(() => AsyncStorage),
    }
  )
);
```

---

## 📱 パフォーマンス最適化

### FlatListの最適化
```tsx
<FlatList
  data={items}
  renderItem={renderItem}
  keyExtractor={(item) => item.id}
  // パフォーマンス設定
  removeClippedSubviews={true}
  maxToRenderPerBatch={10}
  windowSize={5}
  initialNumToRender={10}
  // アイテムレイアウト最適化
  getItemLayout={(data, index) => ({
    length: ITEM_HEIGHT,
    offset: ITEM_HEIGHT * index,
    index,
  })}
/>
```

### メモ化
```tsx
import { memo, useCallback, useMemo } from 'react';

const MemoizedItem = memo(({ item, onPress }) => (
  <TouchableOpacity onPress={() => onPress(item.id)}>
    <Text>{item.title}</Text>
  </TouchableOpacity>
));

function List() {
  const onPress = useCallback((id: string) => {
    navigation.navigate('Detail', { id });
  }, [navigation]);

  const sortedItems = useMemo(() => 
    items.sort((a, b) => a.order - b.order),
    [items]
  );

  return <FlatList data={sortedItems} renderItem={...} />;
}
```

---

## 🚀 ビルド & リリース

### デバッグビルド
```bash
# Android
npx react-native run-android

# iOS
npx react-native run-ios
```

### リリースビルド
```bash
# Android AAB
cd android
./gradlew bundleRelease

# iOS
# Xcode で Archive → Submit
```

### トラブルシューティング
```bash
# キャッシュクリア
npx react-native start --reset-cache

# 完全リセット
rm -rf node_modules ios/Pods
npm install
cd ios && pod install
```

---

## チェックリスト

### 実装時
- [ ] TypeScript厳格モード
- [ ] React Navigation 7.x使用
- [ ] ネイティブモジュール追加後pod install

### ビルド前
- [ ] `npx tsc --noEmit` 通過
- [ ] Android: `npx react-native run-android` 成功
- [ ] iOS: `npx react-native run-ios` 成功
- [ ] リリースビルド確認
