---
name: mobile-app-uiux
description: モバイルアプリUI/UX設計スキル。Material Design 3準拠のデザイン、レスポンシブレイアウト、アクセシビリティ対応、デザインシステム構築を支援。UI設計、UX改善、デザインレビュー時に使用。
---

# モバイルアプリUI/UX設計スキル

## Material Design 3 基本原則

### カラーシステム
```dart
// Dynamic Color Theme
final colorScheme = ColorScheme.fromSeed(
  seedColor: const Color(0xFF6750A4),
  brightness: Brightness.light,
);

// Dark Mode対応
ThemeData(
  useMaterial3: true,
  colorScheme: colorScheme,
  // カスタムカラー拡張
  extensions: [
    CustomColors(
      success: Colors.green,
      warning: Colors.orange,
    ),
  ],
)
```

### Typography
```dart
// Material 3 Typography
TextTheme textTheme = const TextTheme(
  displayLarge: TextStyle(fontSize: 57, fontWeight: FontWeight.w400),
  displayMedium: TextStyle(fontSize: 45, fontWeight: FontWeight.w400),
  displaySmall: TextStyle(fontSize: 36, fontWeight: FontWeight.w400),
  headlineLarge: TextStyle(fontSize: 32, fontWeight: FontWeight.w400),
  headlineMedium: TextStyle(fontSize: 28, fontWeight: FontWeight.w400),
  headlineSmall: TextStyle(fontSize: 24, fontWeight: FontWeight.w400),
  titleLarge: TextStyle(fontSize: 22, fontWeight: FontWeight.w500),
  titleMedium: TextStyle(fontSize: 16, fontWeight: FontWeight.w500),
  titleSmall: TextStyle(fontSize: 14, fontWeight: FontWeight.w500),
  bodyLarge: TextStyle(fontSize: 16, fontWeight: FontWeight.w400),
  bodyMedium: TextStyle(fontSize: 14, fontWeight: FontWeight.w400),
  bodySmall: TextStyle(fontSize: 12, fontWeight: FontWeight.w400),
  labelLarge: TextStyle(fontSize: 14, fontWeight: FontWeight.w500),
  labelMedium: TextStyle(fontSize: 12, fontWeight: FontWeight.w500),
  labelSmall: TextStyle(fontSize: 11, fontWeight: FontWeight.w500),
);
```

## レスポンシブデザイン

### ブレークポイント
| 種別 | 幅 | 対象デバイス |
|------|-----|-------------|
| Compact | < 600dp | スマートフォン |
| Medium | 600-839dp | 小型タブレット |
| Expanded | ≥ 840dp | 大型タブレット |

### 実装パターン
```dart
class ResponsiveLayout extends StatelessWidget {
  final Widget mobile;
  final Widget? tablet;
  final Widget? desktop;

  const ResponsiveLayout({
    required this.mobile,
    this.tablet,
    this.desktop,
  });

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        if (constraints.maxWidth >= 840) {
          return desktop ?? tablet ?? mobile;
        } else if (constraints.maxWidth >= 600) {
          return tablet ?? mobile;
        }
        return mobile;
      },
    );
  }
}
```

### Adaptive Widgets
```dart
// Platform-aware navigation
NavigationBar(  // Mobile
  destinations: [...],
)

NavigationRail(  // Tablet
  destinations: [...],
)

NavigationDrawer(  // Desktop
  children: [...],
)
```

---

## Apple Human Interface Guidelines (HIG)

### iOS固有デザイン原則

| 原則 | 内容 |
|------|------|
| 明確性 | テキスト読みやすく、アイコン明確 |
| 敬意 | コンテンツが主役、UIは控えめ |
| 深度 | レイヤーとモーションで空間表現 |

### iOSらしいナビゲーション

```dart
// Cupertinoウィジェット使用（iOS風）
CupertinoPageScaffold(
  navigationBar: const CupertinoNavigationBar(
    middle: Text('タイトル'),
    // iOS標準の戻るボタン自動表示
  ),
  child: SafeArea(
    child: content,
  ),
)

// プラットフォーム判定で切り替え
Widget build(BuildContext context) {
  if (Platform.isIOS) {
    return CupertinoPageScaffold(...);
  }
  return Scaffold(...);  // Material Design
}
```

### iOS標準コンポーネント

```dart
// アクションシート（iOS風）
showCupertinoModalPopup(
  context: context,
  builder: (context) => CupertinoActionSheet(
    title: const Text('オプションを選択'),
    actions: [
      CupertinoActionSheetAction(
        onPressed: () {},
        child: const Text('編集'),
      ),
      CupertinoActionSheetAction(
        isDestructiveAction: true,
        onPressed: () {},
        child: const Text('削除'),
      ),
    ],
    cancelButton: CupertinoActionSheetAction(
      onPressed: () => Navigator.pop(context),
      child: const Text('キャンセル'),
    ),
  ),
);

// ピッカー（iOS風）
CupertinoDatePicker(
  mode: CupertinoDatePickerMode.date,
  onDateTimeChanged: (DateTime date) {},
)

// スライダー
CupertinoSlider(
  value: _value,
  onChanged: (v) => setState(() => _value = v),
)

// スイッチ
CupertinoSwitch(
  value: _enabled,
  onChanged: (v) => setState(() => _enabled = v),
)
```

### Safe Area（ノッチ/Dynamic Island対応）

```dart
// 必ずSafeAreaで囲む
SafeArea(
  // 各辺の余白を個別制御可能
  top: true,
  bottom: true,
  left: true,
  right: true,
  child: Column(
    children: [...],
  ),
)

// BottomNavigationBar下の余白
Padding(
  padding: EdgeInsets.only(
    bottom: MediaQuery.of(context).viewPadding.bottom,
  ),
  child: content,
)
```

### iOS固有タッチ操作

| 操作 | 用途 |
|------|------|
| 左エッジスワイプ | 戻る（標準動作） |
| 下スワイプ | モーダル閉じる |
| 長押し | コンテキストメニュー |

```dart
// エッジスワイプで戻る（デフォルト有効）
CupertinoPageRoute(
  builder: (_) => NextScreen(),
  // fullscreenDialog: true でモーダル表示
)

// コンテキストメニュー
CupertinoContextMenu(
  actions: [
    CupertinoContextMenuAction(
      onPressed: () {},
      trailingIcon: CupertinoIcons.share,
      child: const Text('共有'),
    ),
  ],
  child: Image.network(imageUrl),
)
```

### SF Symbols（iOSネイティブアイコン）

```dart
// Cupertinoアイコン使用
const Icon(CupertinoIcons.home)
const Icon(CupertinoIcons.settings)
const Icon(CupertinoIcons.person)

// プラットフォーム別アイコン
Icon(
  Platform.isIOS ? CupertinoIcons.back : Icons.arrow_back,
)
```

---

## アクセシビリティ

### 必須対応項目
1. **コントラスト比**: WCAG 2.1 AA準拠（4.5:1以上）
2. **タッチターゲット**: 最小48x48dp
3. **セマンティクスラベル**: 全インタラクティブ要素に設定
4. **スクリーンリーダー対応**: TalkBack/VoiceOver互換

### 実装例
```dart
// Semanticsでアクセシビリティ情報を提供
Semantics(
  label: 'プロフィール画像',
  button: true,
  onTapHint: 'タップして編集',
  child: GestureDetector(
    onTap: _editProfile,
    child: CircleAvatar(
      backgroundImage: NetworkImage(user.avatarUrl),
    ),
  ),
)

// ExcludeSemantics: 装飾的要素を除外
ExcludeSemantics(
  child: Icon(Icons.decorative_icon),
)
```

## コンポーネント設計

### 再利用可能Widget設計原則
1. **単一責任**: 1つのWidgetは1つの目的
2. **構成可能性**: 小さなWidgetを組み合わせて大きなUIを構築
3. **テスト容易性**: 依存性注入でモック可能に

### カスタムウィジェット例
```dart
class AppButton extends StatelessWidget {
  final String label;
  final VoidCallback? onPressed;
  final ButtonVariant variant;
  final bool isLoading;

  const AppButton({
    required this.label,
    this.onPressed,
    this.variant = ButtonVariant.primary,
    this.isLoading = false,
  });

  @override
  Widget build(BuildContext context) {
    return FilledButton(
      onPressed: isLoading ? null : onPressed,
      child: isLoading
          ? const SizedBox(
              width: 20,
              height: 20,
              child: CircularProgressIndicator(strokeWidth: 2),
            )
          : Text(label),
    );
  }
}
```

## アニメーション

### パフォーマンスガイドライン
- 60fps維持を目標
- `RepaintBoundary`で再描画範囲を限定
- 複雑なアニメーションは`Isolate`で計算

### 推奨ライブラリ
- `flutter_animate`: 宣言的アニメーション
- `rive`: 複雑なベクターアニメーション
- `lottie`: After Effectsアニメーション

```dart
// flutter_animateの例
Text('Hello')
  .animate()
  .fadeIn(duration: 300.ms)
  .slideY(begin: 0.3, end: 0);
```

## デザイントークン

### 定義例
```dart
abstract class AppSpacing {
  static const double xs = 4;
  static const double sm = 8;
  static const double md = 16;
  static const double lg = 24;
  static const double xl = 32;
}

abstract class AppRadius {
  static const double sm = 4;
  static const double md = 8;
  static const double lg = 16;
  static const double full = 999;
}

abstract class AppDuration {
  static const Duration fast = Duration(milliseconds: 150);
  static const Duration normal = Duration(milliseconds: 300);
  static const Duration slow = Duration(milliseconds: 500);
}
```

## チェックリスト

UI/UX設計完了前に確認:
- [ ] Material Design 3コンポーネントを使用している
- [ ] ライト/ダークモード両対応している
- [ ] レスポンシブレイアウトが実装されている
- [ ] タッチターゲットが48dp以上
- [ ] 色のコントラスト比がWCAG AA準拠
- [ ] セマンティクスラベルが設定されている
- [ ] ローディング状態が実装されている
- [ ] エラー状態が実装されている
- [ ] 空状態が実装されている
