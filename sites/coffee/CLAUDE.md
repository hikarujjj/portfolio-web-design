# TERRA BEANS - コーヒー豆販売専門サイト

## デザインコンセプト

- **トーン:** エディトリアル・エレガンス × ナチュラル・ラグジュアリー
- **ターゲット:** ギフト需要を重視。贈る人も受け取る人も特別な体験を感じられる上質なブランド体験
- **方向性:** 温かみのあるミニマリズム。雑誌のような洗練されたレイアウトと、自然素材の温もりを融合

コーヒー豆という商品の特性（オーガニック、産地のストーリー、職人技）と、ギフトとしての特別感（上質、洗練、心遣い）を両立させるデザイン。

## Frontend Aesthetics Guidelines

### トーン・方向性

- **エディトリアル（雑誌的）**: 洗練されたタイポグラフィ、計算された余白、読みやすい情報階層
- **ナチュラル**: 紙のようなテクスチャ、大地を感じさせる色合い、オーガニックな印象
- **ラグジュアリー**: 上質な写真、繊細なアニメーション、抑制された色使い
- **温かみ**: ベージュ・クリーム基調、柔らかなフォント、人間味のあるストーリーテリング

### カラースキーム

```css
--color-primary: #faf8f3;        /* クリーム（メイン背景） */
--color-secondary: #f5f1e8;      /* ベージュ（セクション背景） */
--color-tertiary: #ebe5da;       /* ライトベージュ（アクセント背景） */
--color-accent: #c17855;         /* テラコッタ（CTAボタン、強調） */
--color-accent-dark: #9d5e42;    /* ダークテラコッタ（ホバー） */
--color-text: #2d2520;           /* ダークブラウン（本文） */
--color-text-light: #6b5d56;     /* ミディアムブラウン（サブテキスト） */
--color-white: #ffffff;          /* 白（カード背景） */
```

**戦略:**
- **ベースカラー**: クリーム・ベージュのグラデーションで清潔感と温かみを両立
- **アクセント**: テラコッタ（大地のオレンジブラウン）でコーヒー豆の自然な色合いを想起
- **テキスト**: ダークブラウンで可読性を確保しつつ、黒よりも柔らかい印象
- **避けたもの**: 寒色系、人工的な蛍光色、過度なグラデーション

### タイポグラフィ

```css
--font-display: 'Crimson Text', serif;     /* 見出し用 */
--font-body: 'Nunito Sans', sans-serif;    /* 本文用 */
```

**選定理由:**
- **Crimson Text**: エレガントで読みやすいSerif。高級感とクラシックな雰囲気を演出
- **Nunito Sans**: 丸みのある優しいSans-serif。親しみやすさと現代的な洗練を両立

**タイポグラフィパターン:**
```css
.hero-title {
    font-family: var(--font-display);
    font-size: 5rem;          /* デスクトップで大胆に */
    font-weight: 400;         /* 太すぎない優雅さ */
    letter-spacing: 0.02em;   /* わずかな字間で品格 */
    line-height: 1.2;
}

.section-subtitle {
    font-size: 0.9rem;
    letter-spacing: 0.2em;    /* 大きな字間で高級感 */
    text-transform: uppercase;
    font-weight: 600;
}

.product-name {
    font-family: var(--font-display);
    font-size: 1.8rem;
    font-weight: 600;         /* 商品名は少し強調 */
}
```

**避けたフォント**: Inter, Roboto, Arial（汎用的すぎてブランドの個性が出ない）

### モーション・アニメーション

**方針:**
- ページロード時の統一感あるスタガードアニメーション
- ホバー時の繊細なトランジション（transform + box-shadow）
- スクロール時のフェードイン（Intersection Observer）

**実装パターン:**

```css
/* ヒーローセクションのスタガード */
.hero-subtitle { animation: fadeInUp 1s ease-out 0.2s backwards; }
.hero-title { animation: fadeInUp 1s ease-out 0.4s backwards; }
.hero-description { animation: fadeInUp 1s ease-out 0.6s backwards; }
.hero-cta { animation: fadeInUp 1s ease-out 0.8s backwards; }

@keyframes fadeInUp {
    from {
        opacity: 0;
        transform: translateY(40px);
    }
    to {
        opacity: 1;
        transform: translateY(0);
    }
}

/* カードホバー */
.product-card:hover {
    transform: translateY(-10px);
    box-shadow: 0 20px 60px rgba(45, 37, 32, 0.15);
}

.product-card:hover .product-image {
    transform: scale(1.05);  /* 画像ズーム */
}

/* ボタンホバー */
.btn-primary:hover {
    background: var(--color-accent-dark);
    transform: translateY(-3px);
    box-shadow: 0 10px 30px rgba(193, 120, 85, 0.3);
}
```

**JavaScript アニメーション:**
```javascript
// スクロールトリガーアニメーション
const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            entry.target.style.opacity = '1';
            entry.target.style.transform = 'translateY(0)';
        }
    });
}, { threshold: 0.1, rootMargin: '0px 0px -100px 0px' });
```

### 画像・ビジュアル

**画像選定基準:**
- **Unsplash使用**: 高品質なプロフェッショナル写真のみ
- **トーン**: 温かみのある自然光、控えめな彩度、オーガニックな雰囲気
- **被写体**: コーヒー豆、焙煎風景、農園、ギフトボックス、抽出シーン
- **絵文字厳禁**: プロフェッショナルな写真またはSVGアイコンのみ

**使用画像:**
- ヒーロー背景: `photo-1447933601403` (コーヒー豆のクローズアップ)
- 商品カード: `photo-1559056199641` (エチオピア), `photo-1511920170033` (コロンビア), `photo-1514432324607` (ブラジル)
- ストーリー: `photo-1442512595331` (農園), `photo-1495474472287` (焙煎), `photo-1509042239860` (抽出)
- ギフト: `photo-1513558161293` (ギフトボックス), `photo-1591084728795` (セット)

**画像処理:**
```css
.hero-background {
    opacity: 0.15;           /* 背景は控えめに */
    filter: grayscale(30%);  /* わずかにグレースケール */
}

.product-image {
    object-fit: cover;
    transition: transform 0.6s ease;
}
```

### 空間構成・レイアウト

**レイアウト原則:**
- **余白重視**: セクション間のpadding: 8rem（モバイル: 4rem）で呼吸する空間
- **グリッドシステム**: CSS Grid (`grid-template-columns`) で柔軟なレイアウト
- **非対称**: 商品カード3列、ストーリー2列、ギフト1.2fr + 1frで視覚的な変化
- **階層**: section-header（中央揃え）→ コンテンツ（左右構成）で明確な情報階層

**背景・視覚的詳細:**
```css
/* 紙テクスチャオーバーレイ */
body::before {
    content: '';
    position: fixed;
    background-image: url("data:image/svg+xml,<SVGノイズフィルター>");
    opacity: 0.03;
    pointer-events: none;
    z-index: 9999;
}

/* セクション背景のバリエーション */
.products { background: var(--color-secondary); }
.story { background: var(--color-primary); }
.gift { background: var(--color-tertiary); }
.cta { background: var(--color-text); color: var(--color-primary); }
```

## セクション構成

### 1. ナビゲーション (`.navbar`)
- 固定ヘッダー、半透明背景 + backdrop-filter
- ロゴ（左）+ ナビリンク（右）
- スクロール時に背景の不透明度とシャドウを変化

### 2. ヒーロー (`.hero`)
- フルスクリーン（100vh）、背景画像 + オーバーレイ
- 中央配置のキャッチコピー
- スタガードアニメーション（0.2s刻み）
- 2つのCTAボタン（プライマリ + セカンダリ）

### 3. 商品ラインナップ (`#products.products`)
- 3列グリッド（`grid-template-columns: repeat(auto-fit, minmax(320px, 1fr))`）
- カード構成: 画像 + 産地 + 商品名 + 説明 + フレーバータグ
- ホバー時: translateY(-10px) + シャドウ + 画像ズーム

### 4. こだわり・ストーリー (`#story.story`)
- 2列グリッド: テキスト（左）+ 画像（右）
- ハイライト数値（15+ 契約農園、100% フェアトレード等）
- 画像は3枚構成（1枚は横長、2枚は正方形）

### 5. ギフト案内 (`#gift.gift`)
- 2列グリッド: 画像ショーケース（左）+ テキスト（右）
- メイン画像 + アクセント画像（position: absolute で重ね配置）
- チェックリスト形式の特徴説明

### 6. CTA (`#contact.cta`)
- ダークブラウン背景で視覚的に区切り
- 中央揃え、シンプルなメッセージ + ボタン

### 7. フッター (`<footer>`)
- 4列グリッド: ブランド紹介 + 商品 + 会社情報 + サポート
- シンプルなコピーライト

## コードパターン

### CSS変数の活用
```css
:root {
    --color-primary: #faf8f3;
    --font-display: 'Crimson Text', serif;
}

.hero-title {
    color: var(--color-text);
    font-family: var(--font-display);
}
```

### レスポンシブ対応
```css
@media (max-width: 768px) {
    .hero-title { font-size: 2.5rem; }  /* デスクトップ: 5rem */
    .products-grid { grid-template-columns: 1fr; }  /* 1列に */
    .story-content { grid-template-columns: 1fr; }  /* 縦積み */
}
```

### スムーズスクロール
```javascript
document.querySelectorAll('a[href^="#"]').forEach(anchor => {
    anchor.addEventListener('click', function (e) {
        e.preventDefault();
        document.querySelector(this.getAttribute('href')).scrollIntoView({
            behavior: 'smooth'
        });
    });
});
```

## 今後の拡張・更新時の注意点

### デザインの一貫性を保つために

1. **カラーパレットの厳守**: 新規セクション追加時も必ずCSS変数を使用。独自の色を追加しない
2. **タイポグラフィの統一**: 見出しはCrimson Text、本文はNunito Sansを継続
3. **余白のリズム**: セクション間padding: 8rem、要素間margin: 2-3remの基準を維持
4. **アニメーションの統一感**: fadeInUpパターンを継続、スタガード遅延は0.2s刻み

### 追加セクションの例

**お客様の声 (Testimonials):**
```css
.testimonials {
    background: var(--color-primary);
    padding: 8rem 3rem;
}

.testimonial-card {
    background: var(--color-white);
    padding: 2.5rem;
    border-radius: 2px;
    font-style: italic;
    color: var(--color-text-light);
}
```

**定期便案内 (Subscription):**
```css
.subscription {
    background: var(--color-tertiary);
    padding: 8rem 3rem;
}

.subscription-plans {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 2rem;
}
```

### 避けるべき要素

- ❌ **絵文字の使用** (🌍☕📖等): プロフェッショナルな写真やSVGアイコンに置き換える
- ❌ **過度なグラデーション**: 単色または2色の控えめなグラデーションのみ
- ❌ **汎用フォント**: Inter, Roboto, Arial等への変更禁止
- ❌ **原色の使用**: 彩度の高い色（#ff0000, #00ff00等）は世界観を壊す
- ❌ **複雑なアニメーション**: シンプルで洗練されたモーションを維持

## 技術仕様

- **言語**: HTML5, CSS3, Vanilla JavaScript
- **CSS機能**: CSS Grid, Flexbox, CSS Custom Properties, CSS Animations, backdrop-filter
- **JavaScript**: Intersection Observer API, イベントリスニング, スムーズスクロール
- **ブラウザ対応**: モダンブラウザ（Chrome, Firefox, Safari, Edge最新版）
- **レスポンシブ**: モバイルファースト、ブレークポイント768px
- **パフォーマンス**:
  - Preconnect for Google Fonts
  - SVGノイズテクスチャ（data URI埋め込み）
  - 画像最適化（Unsplash: w=800-1200, q=80）
  - CSS-onlyアニメーション（GPU加速）

## 参考資料

- **Google Fonts**: [Crimson Text](https://fonts.google.com/specimen/Crimson+Text), [Nunito Sans](https://fonts.google.com/specimen/Nunito+Sans)
- **画像**: [Unsplash](https://unsplash.com/) - コーヒー関連の高品質写真
- **カラーパレット**: 大地・紙・コーヒーをイメージした自然なトーン
- **デザイン参考**: エディトリアルデザイン、ナチュラル系ECサイト、高級食品ブランド
