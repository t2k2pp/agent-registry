---
name: ios-store-guidelines
description: App Store審査ガイドラインスキル。リジェクト回避、Human Interface Guidelines準拠、プライバシー要件、メタデータ最適化を支援。App Store申請・審査対応時に使用。
---

# App Store 審査ガイドラインスキル

## 審査統計（2024年）

- 年間申請数: 約770万件
- リジェクト率: 約25%（4件に1件）
- 平均審査時間: 24-48時間

---

## よくあるリジェクト理由と対策

### 1. プライバシー違反（最多）

**問題:**
- データ収集の説明不足
- 権限要求の理由が不明確

**対策:**
```xml
<!-- Info.plist - 必ず具体的な理由を記載 -->
<!-- ❌ NG: 曖昧な説明 -->
<key>NSCameraUsageDescription</key>
<string>カメラを使用します</string>

<!-- ✅ OK: 具体的な用途 -->
<key>NSCameraUsageDescription</key>
<string>プロフィール写真の撮影と商品のバーコード読み取りにカメラを使用します</string>
```

**App Store Connect設定:**
- App Privacy（プライバシーラベル）を正確に設定
- 収集するデータの種類と用途を明示

---

### 2. クラッシュ・バグ

**問題:**
- 審査中にアプリがクラッシュ
- 機能が動作しない

**対策:**
```dart
// 全ての外部通信にエラーハンドリング
try {
  final result = await api.fetchData();
} catch (e) {
  // ユーザーフレンドリーなエラー表示
  showErrorDialog('データの取得に失敗しました。再度お試しください。');
}
```

**テスト必須:**
- 実機テスト（iOS 17+必須）
- ネットワークオフライン状態
- 低メモリ状態
- 各種デバイスサイズ

---

### 3. 不完全なアプリ

**問題:**
- プレースホルダーコンテンツ
- Coming Soon機能
- リンク切れ

**対策:**
- 全機能が動作することを確認
- 「準備中」画面を削除
- 全リンクの有効性確認

---

### 4. UIデザイン問題

**問題:**
- Apple Human Interface Guidelines (HIG) 非準拠
- ナビゲーションが分かりにくい
- 異なるデバイスサイズで崩れる

**対策:** → HIG準拠セクション参照

---

### 5. メタデータ問題

**問題:**
- スクリーンショットがアプリと一致しない
- 説明文が不正確
- キーワードスタッフィング

**対策:**
- 最新UIでスクリーンショット作成
- 実際の機能を正確に説明
- 関連キーワードのみ使用

---

### 6. In-App Purchase違反

**問題:**
- デジタルコンテンツをApple IAP以外で販売
- 課金への誘導が外部リンク経由

**対策:**
```dart
// ✅ デジタルコンテンツはIAP必須
// in_app_purchase パッケージ使用
final bool available = await InAppPurchase.instance.isAvailable();
if (available) {
  await InAppPurchase.instance.buyNonConsumable(purchaseParam: param);
}
```

**例外:**
- 物理商品（ECアプリ等）
- アプリ外で消費するサービス

---

## Human Interface Guidelines (HIG) 準拠

### 基本原則

| 原則 | 内容 |
|------|------|
| 明確性 | 情報が読みやすく理解しやすい |
| 遵守 | iOSの標準動作を尊重 |
| 一貫性 | アプリ内で一貫したデザイン |
| フィードバック | ユーザー操作への即座な反応 |

### iOSらしいデザイン

```dart
// ✅ iOS標準ナビゲーション（Cupertino Widgets）
CupertinoNavigationBar(
  middle: const Text('Title'),
  leading: CupertinoButton(
    padding: EdgeInsets.zero,
    child: const Icon(CupertinoIcons.back),
    onPressed: () => Navigator.pop(context),
  ),
)

// ✅ iOS標準アクションシート
showCupertinoModalPopup(
  context: context,
  builder: (context) => CupertinoActionSheet(
    title: const Text('オプションを選択'),
    actions: [
      CupertinoActionSheetAction(
        onPressed: () {},
        child: const Text('オプション1'),
      ),
    ],
    cancelButton: CupertinoActionSheetAction(
      isDestructiveAction: true,
      onPressed: () => Navigator.pop(context),
      child: const Text('キャンセル'),
    ),
  ),
);
```

### タッチターゲット
```dart
// ✅ 最小44x44pt
SizedBox(
  width: 44,
  height: 44,
  child: IconButton(
    icon: const Icon(Icons.menu),
    onPressed: () {},
  ),
)
```

### Safe Area対応
```dart
// ✅ ノッチ/ダイナミックアイランド対応
SafeArea(
  child: Scaffold(
    body: YourContent(),
  ),
)
```

---

## アクセシビリティ要件

### 必須対応
```dart
// VoiceOver対応
Semantics(
  label: '検索ボタン',
  hint: 'タップして検索画面を開きます',
  child: IconButton(
    icon: const Icon(Icons.search),
    onPressed: _openSearch,
  ),
)

// Dynamic Type対応
Text(
  'テキスト',
  style: TextStyle(
    fontSize: MediaQuery.textScalerOf(context).scale(16),
  ),
)
```

### 色コントラスト
- テキスト: 4.5:1 以上
- 大きなテキスト: 3:1 以上

---

## 申請前チェックリスト

### App Store Connect準備
- [ ] アプリ名（30文字以内）
- [ ] サブタイトル（30文字以内）
- [ ] 説明文（4000文字以内）
- [ ] キーワード（100文字以内、カンマ区切り）
- [ ] サポートURL
- [ ] プライバシーポリシーURL
- [ ] 年齢制限設定
- [ ] カテゴリ選択
- [ ] 著作権表示

### スクリーンショット
| デバイス | サイズ | 必須 |
|---------|-------|------|
| iPhone 6.7" | 1290 x 2796 | ✅ |
| iPhone 6.5" | 1284 x 2778 | ✅ |
| iPhone 5.5" | 1242 x 2208 | ✅ |
| iPad Pro 12.9" | 2048 x 2732 | iPadアプリの場合 |

### デモアカウント
```
ログイン機能がある場合、審査用アカウントを提供:
- メール: review@example.com
- パスワード: ReviewPass123
※ App Store Connect → App Review Information に記載
```

### プライバシー
- [ ] プライバシーポリシー公開
- [ ] App Privacy設定完了
- [ ] 必要な権限説明をInfo.plistに追加
- [ ] アカウント削除機能（ログイン機能がある場合）

---

## 審査後の対応

### リジェクト時
1. **Resolution Centerで理由確認**
2. **具体的な問題点を特定**
3. **修正実施**
4. **修正内容を説明して再申請**

### 異議申し立て
```
App Store Connect → Resolution Center → Reply
→ 丁寧に反論を記載（根拠を明示）
```

---

## 審査通過のコツ

1. **最初から品質を高く** - 第一印象が重要
2. **説明は具体的に** - 曖昧さを排除
3. **完全な状態で申請** - 「後で修正」は避ける
4. **実機テスト徹底** - シミュレータだけでは不十分
5. **ガイドライン熟読** - 最新のApp Store Review Guidelinesを確認

---

## 参考リンク

- [App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/)
- [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [App Store Connect Help](https://developer.apple.com/help/app-store-connect/)
