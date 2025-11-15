# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

静的なランディングページのポートフォリオサイト。複数のランディングページデモを集約したコレクション。完全静的なHTML/CSS/JavaScriptで実装され、ビルドプロセスやバンドラーは不要。

## ファイル構造

```
/landing-page/
├── index.html           # メインポートフォリオページ（ダークモード）
├── lumiere/
│   └── index.html      # LUMIÈRE美容院ランディングページ（ホワイトテーマ）
└── other-landing-page/ 
```

### 各ランディングページの特徴

- **index.html（Portfolio）**: ダークモード、カスタムカーソル、グレインテクスチャ、プロジェクトカードグリッド
- **lumiere/index.html**: ミニマル・エレガント、白基調、ゴールドアクセント(#c9a969)

## 開発ワークフロー

### 起動・確認
```bash
# ブラウザで直接開く（ローカルサーバー不要）
open index.html
```

### 新規ランディングページ追加
1. 新しいディレクトリを作成（例: `cafe/`）
2. 自己完結型のHTMLファイルを作成
3. **個別ディレクトリに `CLAUDE.md` を作成**（重要）:
   - デザインコンセプト（トーン、方向性）
   - Frontend Aesthetics Guidelines（カラー、タイポグラフィ、アニメーション）
   - セクション構成と実装パターン
   - 今後の拡張時の注意点
   - 参考: `lumiere/CLAUDE.md`をテンプレートとして活用
4. `index.html`のプロジェクトグリッドにカードを追加：
   ```html
   <div class="project-card">
       <img src="..." class="project-image">
       <div class="project-overlay">
           <div class="project-category">Category</div>
           <h3 class="project-title">Title</h3>
           <p class="project-description">説明...</p>
           <div class="project-tags">
               <span class="tag">Tag1</span>
           </div>
       </div>
       <a href="./cafe/index.html" class="project-link"></a>
   </div>
   ```

**CLAUDE.md作成の重要性:**
各ランディングページのデザインガイドラインを文書化することで、将来の更新や関連ページ作成時に一貫性を保てます。特にfrontend-designスキルで作成したページは、カラー、タイポグラフィ、アニメーション方針などを明確に記録することが重要です。

## CSS設計パターン

### CSSカスタムプロパティ
各ページは独立した`:root`変数セットで管理：
```css
:root {
    --color-primary: #ffffff;
    --color-accent: #c9a969;
    --font-display: 'Cormorant Garamond', serif;
    --font-body: 'Montserrat', sans-serif;
}
```

### 共通アニメーション
- `fadeInUp`: 下から上へフェードイン
- `slideDown`: 上からスライドダウン
- `fadeInScale`: スケール付きフェードイン
- `float`: 浮遊アニメーション

### レスポンシブブレークポイント
- デスクトップ: デフォルト
- タブレット/モバイル: `@media (max-width: 768px)`

## デザイン慣例

### タイポグラフィパターン
- **見出し**: Serif系（Playfair Display, Cormorant Garamond, Libre Baskerville）
- **本文**: Sans-serif（Work Sans, Montserrat）
- **letter-spacing**: 0.1em～0.3em（高級感のため）

### カラーパレット
各ページは独自のテーマを持つ：
- **Portfolio**: ダーク (#0a0a0a) + 赤アクセント (#ff6b6b)
- **LUMIÈRE**: ホワイト (#ffffff) + ゴールド (#c9a969)

### ホバーエフェクト
標準パターン：`transform: translateY(-10px) + box-shadow`

## 外部リソース

### Google Fonts
各ページで異なるフォントセットを動的読み込み。Preconnectで最適化済み。

### 画像
Unsplash APIを使用：
```
https://images.unsplash.com/photo-xxx?w=1200&q=80
```
- 幅: 800-1600px
- 品質: q=80

## 技術スタック

- **言語**: HTML5, CSS3, Vanilla JavaScript
- **CSS機能**: Grid, Flexbox, Custom Properties, Animations, backdrop-filter
- **JavaScript**: DOM操作, requestAnimationFrame, イベントリスニング
- **依存関係**: なし（外部フレームワーク不使用）

## コード編集時の注意

### HTML
- セクション構造（.hero, .services, .gallery等）を維持
- クラス名はCSSと密結合のため変更に注意

### CSS
1. カラー変更は`:root`変数を編集
2. アニメーション遅延は`nth-child`で`animation-delay`を制御
3. レスポンシブは`@media`内で上書き

### JavaScript
- `requestAnimationFrame`を使用したスムーズアニメーション維持
- `querySelector`はDOMツリー構造に依存

## パフォーマンス最適化

- SVGノイズテクスチャはdata URIで埋め込み
- 画像は幅1200px以下、品質80で最適化
- アニメーションはCSS transition/animation優先（GPU加速）
