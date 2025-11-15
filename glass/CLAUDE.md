# CRYSTALLINE - ガラスコップ販売専門サイト

このドキュメントは、CRYSTALLINEランディングページのデザインガイドラインと制作意図を記録しています。関連ページや更新を行う際は、このガイドラインに従って一貫性を保ってください。

## デザインコンセプト

**トーン:** 洗練されたミニマリズム、透明感、涼しげ、エレガント

ガラス製品の持つ透明感、光の反射、水のような涼しげな美しさを表現。シンプルで洗練されたデザインの中に、ガラス特有の繊細さと高級感を演出。訪問者に「美しいガラス製品を手にしたい」と思わせる、視覚的に魅力的な空間を創出。

## Frontend Aesthetics Guidelines

### トーン・方向性
- **Refined Minimalism**: シンプルで洗練された余白の美学
- **Crystalline & Ethereal**: ガラスと水の透明感、涼しげで澄んだ雰囲気
- **Elegant & Contemporary**: モダンで上品、高品質な印象
- **Luminous**: 光と影の繊細な演出、ガラスの輝き

### カラースキーム

```css
--color-crystal: #E8F4F8;      /* クリスタルブルー（背景） */
--color-ice: #D4EBF7;          /* アイスブルー（背景グラデーション） */
--color-water: #A8D5E2;        /* ウォーターブルー（アクセント） */
--color-ocean: #5A9FBF;        /* オーシャンブルー（強調） */
--color-deep: #2C5F7F;         /* ディープブルー（テキスト・強調） */
--color-glass: rgba(255, 255, 255, 0.25); /* ガラス効果 */
--color-text: #1A3D52;         /* メインテキスト */
--color-text-light: #4A6B7C;  /* サブテキスト */
```

**カラー戦略:**
- **ブルー系**: 透明感、涼しげさ、水とガラスの連想を喚起
- **グラデーション**: 水のような流動的な美しさを表現
- **半透明**: rgba()とbackdrop-filterでガラスの質感を再現
- **白ベース**: 清潔感、明るさ、ガラスの透明度を強調

### タイポグラフィ

```css
--font-display: 'Libre Baskerville', serif;  /* 見出し */
--font-body: 'Nunito Sans', sans-serif;      /* 本文 */
```

**フォント選定理由:**
- **Libre Baskerville**: クラシカルで優雅なセリフ体。ガラス製品の伝統的な美しさとエレガンスを表現
- **Nunito Sans**: 柔らかく読みやすいサンセリフ体。モダンで親しみやすい印象

**タイポグラフィパターン:**
- 見出し: `letter-spacing: 0.1em～0.2em`（洗練された印象）
- 本文: `font-weight: 300`（軽やかさ、透明感）
- ロゴ: `letter-spacing: 0.2em`、全て大文字で高級感

### モーション・アニメーション

**アニメーション方針:**
- **流動的で優雅**: 水やガラスのような滑らかな動き
- **段階的表示**: `animation-delay`で要素を順番に表示
- **繊細なトランジション**: 透明度、位置、スケールの微細な変化

**実装パターン:**
```css
/* フェードインアップ（汎用） */
@keyframes fadeInUp {
    from { opacity: 0; transform: translateY(40px); }
    to { opacity: 1; transform: translateY(0); }
}

/* ズームイン（背景画像） */
@keyframes zoomIn {
    from { transform: scale(1.1); }
    to { transform: scale(1); }
}

/* フェードインスケール（ギャラリー） */
@keyframes fadeInScale {
    from { opacity: 0; transform: scale(0.9); }
    to { opacity: 1; transform: scale(1); }
}

/* スライドダウン（ナビゲーション） */
@keyframes slideDown {
    from { transform: translateY(-100%); opacity: 0; }
    to { transform: translateY(0); opacity: 1; }
}
```

### 画像・ビジュアル

**画像選定基準:**
- **ソース**: Unsplash（高品質、商用利用可）
- **テーマ**: ガラス製品、ワイングラス、タンブラー、テーブルセッティング
- **品質**: `?w=800&q=80`～`?w=1200&q=80`
- **雰囲気**: 透明感のある写真、光の反射が美しい写真、ミニマルな構図

**ビジュアル戦略:**
- 高品質な商品写真でガラスの美しさを強調
- 光と影の演出で立体感と輝きを表現
- ホバー時にズーム＋オーバーレイで没入感を演出

### 空間構成・レイアウト

**レイアウト原則:**
- **十分な余白**: セクション間 `padding: 8rem 5%`で開放感
- **中央揃え**: 最大幅 `max-width: 1400px`
- **グリッドシステム**: `grid-template-columns: repeat(auto-fit, minmax(320px, 1fr))`
- **アスペクト比**: ギャラリー画像は `aspect-ratio: 1`で正方形グリッド

**背景・視覚的詳細:**
- **ガラスエフェクト**: `backdrop-filter: blur(20px)`で本物のガラスのような質感
- **半透明レイヤー**: `rgba()`で光の透過を表現
- **グラデーション**: 水のような流動的な背景
- **繊細な影**: `box-shadow: 0 20px 50px rgba(90, 159, 191, 0.15)`

## セクション構成

### 1. ナビゲーション
- **固定ヘッダー**: `position: fixed`でスクロール時も表示
- **半透明ガラス背景**: `backdrop-filter: blur(20px)`
- **ロゴ**: レタースペーシング `0.2em`、全て大文字
- **ナビリンク**: ホバー時にグラデーションアンダーライン

### 2. ヒーローセクション
- **フルスクリーン**: `height: 100vh`
- **背景画像**: ガラス製品のプロフェッショナル写真
- **グラデーションオーバーレイ**: ブルー系の半透明グラデーション
- **CTAボタン**: ガラスエフェクト付き、ホバー時に浮き上がる

### 3. コレクションセクション
- **3列グリッド**: タンブラー、ワイングラス、ロックグラス
- **カード型レイアウト**: 半透明背景、ガラスエフェクト
- **商品画像**: 300px高さ、ホバー時にズーム
- **価格表示**: Libre Baskervilleでエレガントに

### 4. ギャラリーセクション
- **6列レスポンシブグリッド**: 正方形のギャラリー
- **オーバーレイ**: ホバー時にブルーのグラデーションオーバーレイ
- **段階的表示**: `animation-delay`で順番にフェードイン

### 5. クラフツマンシップセクション
- **2列レイアウト**: テキストと画像を並列表示
- **特徴リスト**: 罫線で区切られたリスト形式
- **左右からのアニメーション**: テキストは左から、画像は右から

### 6. コンタクトセクション
- **中央揃え**: シンプルな問い合わせ情報
- **グラデーション背景**: 涼しげなブルー系
- **CTAボタン**: ガラスエフェクト付き

### 7. フッター
- **ダーク背景**: `var(--color-deep)`で落ち着いた印象
- **シンプル**: ロゴとコピーライトのみ

## コードパターン

### HTML構造
```html
<section class="collections">
    <h2 class="section-title">Collections</h2>
    <p class="section-subtitle">サブタイトル</p>
    <div class="collections-grid">
        <div class="collection-card">...</div>
    </div>
</section>
```

### CSS変数の活用
```css
color: var(--color-ocean);
font-family: var(--font-display);
backdrop-filter: blur(20px);
```

### ガラスエフェクト
```css
background: rgba(255, 255, 255, 0.6);
backdrop-filter: blur(20px) saturate(180%);
border: 1px solid rgba(90, 159, 191, 0.2);
```

### レスポンシブ対応
```css
@media (max-width: 768px) {
    .collections-grid { grid-template-columns: 1fr; }
}
```

## 今後の拡張・更新時の注意点

### デザインの一貫性を保つために

1. **カラー**: 必ず定義済みのブルー系CSS変数を使用。新色追加は慎重に
2. **フォント**: Libre Baskerville × Nunito Sansの組み合わせを維持
3. **ガラスエフェクト**: `backdrop-filter: blur()`を活用した半透明表現
4. **余白**: セクション間は `8rem`、要素間は `2～4rem`を基準に
5. **画像**: Unsplashから透明感のあるガラス製品の写真を選定

### 追加セクションの例

新しいセクション（例: お客様の声、ブランドストーリーなど）を追加する場合:
- 既存のセクション構造（`.section-title` + `.section-subtitle` + グリッド）を踏襲
- 半透明のカード型レイアウトで統一感を保つ
- ホバーエフェクトは `translateY(-10px)`パターンを使用
- アニメーション遅延は `0.1s`刻みで設定

### 避けるべき要素

- ❌ 絵文字（💧✨など）- プロフェッショナルさを損なう
- ❌ 強い彩度の色（ビビッドカラー等）- 透明感と涼しげな雰囲気を損なう
- ❌ 過度な装飾 - ミニマリズムに反する
- ❌ 重い背景パターン - 透明感を損なう

## 技術仕様

- **言語**: HTML5, CSS3, Vanilla JavaScript（フレームワーク不使用）
- **ブラウザ対応**: モダンブラウザ（Chrome, Firefox, Safari, Edge最新版）
- **レスポンシブ**: モバイルファースト、768px以下でレイアウト変更
- **パフォーマンス**:
  - Google Fonts preconnectで最適化
  - 画像は幅800～1200px、品質80
  - CSS transitionでGPU加速
  - backdrop-filter対応ブラウザ推奨

## 参考資料

- **Google Fonts**: https://fonts.google.com/
- **Unsplash**: https://unsplash.com/
- **カラーパレット**: ブルー系 (#E8F4F8, #5A9FBF, #2C5F7F)
- **ガラスエフェクト**: backdrop-filter, rgba()

---

**最終更新**: 2025年作成
**デザイナー**: Claude Code (frontend-design skill)
**ブランド**: CRYSTALLINE
