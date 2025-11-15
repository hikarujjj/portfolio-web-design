# SUNNY SIDE CAFE - 都会のモダンカフェ

## デザインコンセプト

- **トーン:** モダン・ミニマリズム × エナジェティック
- **ターゲット:** 都会で働く若い世代、カフェでの仕事や読書を楽しむ人々
- **方向性:** 明るく元気な雰囲気で一日を始める。洗練されたモダンな空間で、太陽のような温かさを感じられるカフェ体験

イエロー・オレンジの大胆な配色で視覚的なエネルギーを生み出しながら、クリーンでミニマルなレイアウトで都会的な洗練さを表現。

## Frontend Aesthetics Guidelines

### トーン・方向性

- **モダン・ミニマリズム**: シャープなタイポグラフィ、大胆な余白、幾何学的な構成
- **エナジェティック**: ビビッドなイエロー・オレンジで活力と明るさを表現
- **都会的洗練**: クリーンなレイアウト、スムーズなアニメーション、高品質な写真
- **温かく親しみやすい**: サンシャインカラーで明るく前向きな印象

### カラースキーム

```css
--color-primary: #FDB71A;        /* サンシャインイエロー */
--color-secondary: #FF9F43;      /* ブライトオレンジ */
--color-accent: #FF6B6B;         /* コーラル */
--color-bg: #FFFBF5;             /* ライトクリーム（背景） */
--color-bg-light: #FFFFFF;       /* 白（セクション背景） */
--color-text: #2D3436;           /* ダークグレー（本文） */
--color-text-light: #636E72;     /* ミディアムグレー（サブテキスト） */
--color-text-lighter: #B2BEC3;   /* ライトグレー */
```

**戦略:**
- **メインカラー**: イエロー・オレンジのグラデーションで太陽の光を表現
- **背景**: クリームがかった白でソフトで温かみのある印象
- **テキスト**: ダークグレーで可読性を確保しつつ、黒よりも柔らかく
- **アクセント**: コーラルで視覚的なポイントを作成
- **避けたもの**: 寒色系、暗い色調、抑制されすぎた色使い

### タイポグラフィ

```css
--font-display: 'Outfit', sans-serif;            /* 見出し用 */
--font-body: 'Plus Jakarta Sans', sans-serif;    /* 本文用 */
```

**選定理由:**
- **Outfit**: モダンでシャープなSans-serif。都会的で洗練された印象
- **Plus Jakarta Sans**: 読みやすく親しみやすい。温かみのあるモダンフォント

**タイポグラフィパターン:**
```css
.hero-title {
    font-family: var(--font-display);
    font-size: 5rem;
    font-weight: 800;
    letter-spacing: -0.03em;   /* タイトなレタースペースで力強さ */
    line-height: 1.1;
}

.section-label {
    font-size: 0.9rem;
    font-weight: 700;
    letter-spacing: 0.15em;    /* 大きな字間で高級感 */
    text-transform: uppercase;
}

.menu-item-name {
    font-family: var(--font-display);
    font-size: 1.8rem;
    font-weight: 700;
}
```

**避けたフォント**: Inter, Roboto, Arial（汎用的で個性がない）

### モーション・アニメーション

**方針:**
- ページロード時の統一感あるスタガードアニメーション
- ホバー時のダイナミックなトランジション
- スクロール時のフェードイン（Intersection Observer）

**実装パターン:**

```css
/* ヒーローセクションのスタガード */
.hero-label { animation: fadeInUp 0.8s ease-out 0.2s backwards; }
.hero-title { animation: fadeInUp 0.8s ease-out 0.4s backwards; }
.hero-description { animation: fadeInUp 0.8s ease-out 0.6s backwards; }
.hero-cta { animation: fadeInUp 0.8s ease-out 0.8s backwards; }

@keyframes fadeInUp {
    from {
        opacity: 0;
        transform: translateY(30px);
    }
    to {
        opacity: 1;
        transform: translateY(0);
    }
}

/* メニューアイテムのホバー */
.menu-item:hover {
    transform: translateY(-10px);
    border-color: var(--color-primary);
    box-shadow: 0 20px 50px rgba(253, 183, 26, 0.2);
}

.menu-item:hover .menu-item-image {
    transform: scale(1.1);  /* 画像ズーム */
}

/* ボタンホバー */
.btn-primary:hover {
    transform: translateY(-3px);
    box-shadow: 0 15px 40px rgba(253, 183, 26, 0.4);
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
}, { threshold: 0.1, rootMargin: '0px 0px -50px 0px' });
```

### 画像・ビジュアル

**画像選定基準:**
- **Unsplash使用**: 高品質でプロフェッショナルな写真のみ
- **トーン**: 明るく温かみのある自然光、洗練された都会的な雰囲気
- **被写体**: カフェインテリア、コーヒー、フード、ラテアート
- **絵文字の使用**: コンタクトセクションのアイコンのみ（シンプルで最小限）

**使用画像:**
- ヒーロー: `photo-1554118811-1e0d58224f24` (明るいカフェインテリア)
- メニュー: コーヒー、フレンチトースト、アボカドトースト、ケーキ、スムージー等
- ギャラリー: 店内の雰囲気、コーヒーメイキング、ラテアート

**画像処理:**
```css
.hero-image img {
    border-radius: 30px;
    box-shadow: 0 30px 80px rgba(45, 52, 54, 0.15);
}

.menu-item-image,
.gallery-item img {
    object-fit: cover;
    transition: transform 0.6s ease;
}
```

### 空間構成・レイアウト

**レイアウト原則:**
- **非対称グリッド**: ヒーロー（1.2fr + 1fr）、ギャラリー（複雑なグリッド）で視覚的な動き
- **大胆な余白**: セクション間padding: 8rem（モバイル: 4rem）で呼吸する空間
- **グリッドシステム**: CSS Gridで柔軟かつモダンなレイアウト
- **階層**: section-header（中央揃え）→ コンテンツ（グリッド展開）

**背景・視覚的詳細:**
```css
/* ヒーローのグラデーション背景 */
.hero {
    background: linear-gradient(135deg, #FFFBF5 0%, #FFF5E6 50%, #FFE8CC 100%);
}

/* 浮遊する装飾要素 */
.hero::before {
    background: radial-gradient(circle, rgba(253, 183, 26, 0.2) 0%, transparent 70%);
    border-radius: 50%;
    animation: float 20s ease-in-out infinite;
}

@keyframes float {
    0%, 100% { transform: translate(0, 0) scale(1); }
    50% { transform: translate(-50px, -50px) scale(1.1); }
}
```

## セクション構成

### 1. ナビゲーション (`.navbar`)
- 固定ヘッダー、半透明背景 + backdrop-filter
- ロゴ（左、グラデーションテキスト）+ ナビリンク（右）
- スクロール時にパディングとシャドウを変化

### 2. ヒーロー (`.hero`)
- 2列グリッド（1.2fr + 1fr）、テキスト（左）+ 画像（右）
- グラデーション背景 + 浮遊する装飾要素
- スタガードアニメーション（0.2s刻み）
- バッジ要素（営業開始時間）

### 3. メニュー (`#menu.menu`)
- 6アイテムのグリッド（`repeat(auto-fit, minmax(320px, 1fr))`）
- カード構成: 画像 + カテゴリバッジ + 商品名 + 説明 + 価格
- ホバー時: translateY(-10px) + ボーダーカラー変化 + シャドウ

### 4. ギャラリー (`#gallery.gallery`)
- 複雑なグリッドレイアウト（4列、一部アイテムは複数セルを占有）
- ホバー時: 画像ズーム + グラデーションオーバーレイ
- 6枚の画像で店舗の雰囲気を伝える

### 5. コンタクト (`#contact.contact`)
- ダークグレー背景で視覚的に区切り
- 2列グリッド: 連絡先情報（左）+ 営業時間（右）
- アイコン付き連絡先詳細
- 営業時間リスト

### 6. フッター (`<footer>`)
- シンプルなコピーライト

## コードパターン

### CSS変数の活用
```css
:root {
    --color-primary: #FDB71A;
    --font-display: 'Outfit', sans-serif;
}

.btn-primary {
    background: linear-gradient(135deg, var(--color-primary), var(--color-secondary));
}
```

### グラデーションテキスト
```css
.logo {
    background: linear-gradient(135deg, var(--color-primary) 0%, var(--color-secondary) 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
}
```

### レスポンシブ対応
```css
@media (max-width: 768px) {
    .hero {
        grid-template-columns: 1fr;  /* 2列 → 1列 */
    }
    .hero-title { font-size: 2.5rem; }  /* 5rem → 2.5rem */
}
```

## 今後の拡張・更新時の注意点

### デザインの一貫性を保つために

1. **カラーパレットの厳守**: イエロー・オレンジの明るいトーンを維持。寒色系や暗い色は避ける
2. **タイポグラフィの統一**: Outfit（見出し）、Plus Jakarta Sans（本文）を継続
3. **余白のリズム**: セクション間padding: 8rem、要素間gap: 2-3remを維持
4. **アニメーションの統一感**: fadeInUpパターン、0.2s刻みのスタガード遅延

### 追加セクションの例

**お客様の声 (Testimonials):**
```css
.testimonials {
    background: var(--color-bg-light);
    padding: 8rem 4rem;
}

.testimonial-card {
    background: linear-gradient(135deg, rgba(253, 183, 26, 0.05), rgba(255, 159, 67, 0.05));
    padding: 2.5rem;
    border-radius: 20px;
    border: 2px solid transparent;
}

.testimonial-card:hover {
    border-color: var(--color-primary);
}
```

**オンライン注文 (Online Order):**
```css
.order {
    background: linear-gradient(135deg, var(--color-primary), var(--color-secondary));
    color: white;
    padding: 6rem 4rem;
    text-align: center;
}
```

### 避けるべき要素

- ❌ **暗い色調**: イエロー・オレンジの明るさを損なう暗い配色
- ❌ **寒色系**: ブルー、グリーン等の寒色は雰囲気に合わない
- ❌ **過度な装飾**: シンプルでクリーンなモダンさを維持
- ❌ **汎用フォント**: Inter, Roboto等への変更禁止
- ❌ **絵文字の乱用**: アイコン代わりに使うのは最小限に

## 技術仕様

- **言語**: HTML5, CSS3, Vanilla JavaScript
- **CSS機能**: CSS Grid, Flexbox, CSS Custom Properties, CSS Animations, Backdrop-filter, Gradient
- **JavaScript**: Intersection Observer API, スクロールイベント、スムーズスクロール
- **ブラウザ対応**: モダンブラウザ（Chrome, Firefox, Safari, Edge最新版）
- **レスポンシブ**: モバイルファースト、ブレークポイント768px
- **パフォーマンス**:
  - Preconnect for Google Fonts
  - 画像最適化（Unsplash: w=800-1200, q=80）
  - CSS-onlyアニメーション（GPU加速）

## 参考資料

- **Google Fonts**: [Outfit](https://fonts.google.com/specimen/Outfit), [Plus Jakarta Sans](https://fonts.google.com/specimen/Plus+Jakarta+Sans)
- **画像**: [Unsplash](https://unsplash.com/) - カフェ関連の高品質写真
- **カラーパレット**: サンシャインイエロー、ブライトオレンジで太陽のような明るさ
- **デザイン参考**: モダンカフェ、都会的なミニマリズム、エナジェティックなブランディング
