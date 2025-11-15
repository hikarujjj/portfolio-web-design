# 技術アーキテクチャ - デザイン参考ライブラリ

**最終更新**: 2025年11月15日
**ステータス**: 設計段階

---

## システム概要

完全自動化されたデザイン生成・配信プラットフォーム。Claude Code Skillによる高品質デザイン生成と、サブスクリプション課金を組み合わせたSaaS型サービス。

### アーキテクチャ方針

1. **完全自動化**: 人力は週30分の品質チェックのみ
2. **スケーラビリティ**: ユーザー数増加に自動対応
3. **低コスト**: 既存リソース最大活用（Vercel Pro, Claude Code等）
4. **セキュリティ**: 決済情報はStripeで管理、個人情報は最小限

---

## 技術スタック

### フロントエンド

```typescript
// 選定技術
- Framework: Next.js 14 (App Router)
- Language: TypeScript
- Styling: Tailwind CSS
- UI Components: shadcn/ui
- State Management: React Hooks + Context API
- Forms: React Hook Form + Zod
```

**選定理由**:
- ✅ 既存ブログでNext.js使用（経験あり）
- ✅ Vercel Pro利用中（追加コストゼロ）
- ✅ SEOに強い（SSR/SSG）
- ✅ 高速（Edge Runtime対応）

### バックエンド

```typescript
// 選定技術
- Database: Supabase (PostgreSQL)
- Auth: Supabase Auth
- Storage: Supabase Storage
- API: Next.js API Routes / Server Actions
- Payments: Stripe Billing
- Email: Resend
```

**Supabase選択（公式 vs Selfhost）**:

| 項目 | 公式（推奨） | Selfhost（NAS） |
|------|------------|----------------|
| **初期コスト** | $0（Free tier） | $0（既存NAS） |
| **月額コスト** | $0〜25 | $0 |
| **信頼性** | 99.9% | 自宅回線依存 |
| **スケール** | 自動 | 手動 |
| **メンテナンス** | 不要 | 必要 |
| **セキュリティ** | プロ | 自己責任 |

**判断**: 初期は公式Free tier、100ユーザー超えたら再検討

### AI/生成系

```python
# 選定技術
- Code Generation: Claude Code API ($100プラン利用中)
- Image Generation: Stable Diffusion 3.5（selfhost or Replicate）
- Screenshot: Playwright
```

**Stable Diffusion選択肢**:

| 方法 | コスト | 速度 | 品質 |
|------|--------|------|------|
| **Selfhost (Synology NAS)** | $0 | 遅い | 高 |
| **Replicate API** | $0.01/枚 | 速い | 高 |
| **Stability AI API** | $0.02/枚 | 速い | 高 |

**判断**: 初期はReplicate（開発速度優先）、スケール後にselfhost検討

### インフラ

```yaml
# ホスティング
- Frontend: Vercel Pro（利用中）
- Database: Supabase（公式クラウド）
- Storage: Supabase Storage
- CDN: Vercel Edge Network
- Domain: Cloudflare（DNS管理）

# CI/CD
- GitHub Actions（デザイン自動生成）
- Vercel（自動デプロイ）
```

---

## データベース設計

### ERD（Entity-Relationship Diagram）

```
users (Supabase Auth)
  ├─ id (UUID, PK)
  ├─ email (TEXT, UNIQUE)
  ├─ created_at (TIMESTAMP)
  └─ metadata (JSONB)

profiles (Extended User Info)
  ├─ id (UUID, PK, FK -> users.id)
  ├─ subscription_tier (TEXT) -- 'free', 'basic', 'pro'
  ├─ stripe_customer_id (TEXT)
  ├─ created_at (TIMESTAMP)
  └─ updated_at (TIMESTAMP)

subscriptions (Stripe Sync)
  ├─ id (UUID, PK)
  ├─ user_id (UUID, FK -> users.id)
  ├─ stripe_subscription_id (TEXT, UNIQUE)
  ├─ status (TEXT) -- 'active', 'canceled', 'past_due'
  ├─ tier (TEXT) -- 'basic', 'pro'
  ├─ current_period_end (TIMESTAMP)
  ├─ created_at (TIMESTAMP)
  └─ updated_at (TIMESTAMP)

designs
  ├─ id (UUID, PK)
  ├─ title (TEXT)
  ├─ slug (TEXT, UNIQUE)
  ├─ category (TEXT) -- 'hero', 'services', 'footer', etc.
  ├─ style (TEXT) -- 'minimal', 'dark', 'vivid', etc.
  ├─ industry (TEXT, NULLABLE) -- 'beauty', 'cafe', 'saas', etc.
  ├─ thumbnail_url (TEXT)
  ├─ demo_url (TEXT)
  ├─ code_zip_url (TEXT) -- Supabase Storage URL
  ├─ metadata (JSONB) -- colors, fonts, etc.
  ├─ is_published (BOOLEAN, DEFAULT false)
  ├─ created_at (TIMESTAMP)
  └─ published_at (TIMESTAMP, NULLABLE)

downloads (Optional - Analytics)
  ├─ id (UUID, PK)
  ├─ user_id (UUID, FK -> users.id)
  ├─ design_id (UUID, FK -> designs.id)
  ├─ downloaded_at (TIMESTAMP)
  └─ ip_address (INET, NULLABLE)
```

### Supabase RLS (Row Level Security)

```sql
-- ユーザーは自分のプロフィールのみ閲覧・更新可能
CREATE POLICY "Users can view own profile"
  ON profiles FOR SELECT
  USING (auth.uid() = id);

CREATE POLICY "Users can update own profile"
  ON profiles FOR UPDATE
  USING (auth.uid() = id);

-- 公開デザインは全員閲覧可能
CREATE POLICY "Published designs are viewable by everyone"
  ON designs FOR SELECT
  USING (is_published = true);

-- デザインのZIPダウンロードは有料会員のみ
CREATE POLICY "Paid users can download designs"
  ON designs FOR SELECT
  USING (
    is_published = true
    AND (
      -- Basicプラン以上
      auth.uid() IN (
        SELECT user_id
        FROM subscriptions
        WHERE status = 'active'
        AND tier IN ('basic', 'pro')
      )
      -- または7日間トライアル中
      OR auth.uid() IN (
        SELECT id
        FROM profiles
        WHERE created_at > NOW() - INTERVAL '7 days'
      )
    )
  );
```

---

## システムアーキテクチャ

### 全体フロー

```
┌─────────────────────────────────────────────────────┐
│ 【ユーザーフロー】                                    │
└─────────────────────────────────────────────────────┘
                           │
                           ▼
            ┌──────────────────────────┐
            │   Next.js Frontend       │
            │   (Vercel)               │
            └──────────┬───────────────┘
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
┌──────────────┐ ┌─────────────┐ ┌─────────────┐
│  Supabase    │ │   Stripe    │ │  Resend     │
│  (Auth/DB)   │ │  (Billing)  │ │  (Email)    │
└──────────────┘ └─────────────┘ └─────────────┘

┌─────────────────────────────────────────────────────┐
│ 【自動生成フロー】                                    │
└─────────────────────────────────────────────────────┘
                           │
                           ▼
            ┌──────────────────────────┐
            │  GitHub Actions          │
            │  (Cron: 毎週月曜 9:00)    │
            └──────────┬───────────────┘
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
┌──────────────┐ ┌─────────────┐ ┌──────────────┐
│ Claude Code  │ │ Stable      │ │ Playwright   │
│ API          │ │ Diffusion   │ │ (Screenshot) │
└──────┬───────┘ └──────┬──────┘ └──────┬───────┘
       │                │               │
       └────────────────┴───────────────┘
                        │
                        ▼
            ┌──────────────────────────┐
            │  Supabase Storage        │
            │  + DB Insert             │
            └──────────┬───────────────┘
                       │
                       ▼
            ┌──────────────────────────┐
            │  Vercel Auto Deploy      │
            └──────────┬───────────────┘
                       │
                       ▼
            ┌──────────────────────────┐
            │  Email Notification      │
            │  (Resend)                │
            └──────────────────────────┘
```

---

## 自動生成パイプライン

### GitHub Actions Workflow

```yaml
# .github/workflows/generate-designs.yml

name: Generate Weekly Designs

on:
  schedule:
    # 毎週月曜 9:00 JST (= 日曜 24:00 UTC)
    - cron: '0 0 * * 0'
  workflow_dispatch: # 手動実行も可能

jobs:
  generate:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install Dependencies
        run: npm install

      - name: Generate Designs (Claude Code API)
        env:
          CLAUDE_API_KEY: ${{ secrets.CLAUDE_API_KEY }}
          REPLICATE_API_KEY: ${{ secrets.REPLICATE_API_KEY }}
        run: |
          node scripts/generate-designs.js \
            --count 10 \
            --category random

      - name: Take Screenshots (Playwright)
        run: |
          npx playwright install
          node scripts/screenshot-designs.js

      - name: Upload to Supabase Storage
        env:
          SUPABASE_URL: ${{ secrets.SUPABASE_URL }}
          SUPABASE_SERVICE_KEY: ${{ secrets.SUPABASE_SERVICE_KEY }}
        run: |
          node scripts/upload-to-storage.js

      - name: Insert to Database
        run: |
          node scripts/insert-to-db.js

      - name: Deploy Demos to Vercel
        env:
          VERCEL_TOKEN: ${{ secrets.VERCEL_TOKEN }}
        run: |
          npx vercel --prod --token=$VERCEL_TOKEN

      - name: Send Email Notification
        env:
          RESEND_API_KEY: ${{ secrets.RESEND_API_KEY }}
        run: |
          node scripts/send-newsletter.js
```

### スクリプト詳細

#### `scripts/generate-designs.js`

```javascript
import Anthropic from '@anthropic-ai/sdk';
import fs from 'fs';

const client = new Anthropic({
  apiKey: process.env.CLAUDE_API_KEY,
});

async function generateDesign(category) {
  const message = await client.messages.create({
    model: 'claude-sonnet-4-5-20250929',
    max_tokens: 8000,
    messages: [{
      role: 'user',
      content: `frontend-designスキルを使って、${category}セクションのランディングページデザインを生成してください。

要件:
- カテゴリ: ${category}
- スタイル: ランダム（ミニマル、ダーク、ビビッド等）
- 業種: ランダム
- レスポンシブ対応
- 高品質なデザイン

HTMLファイルを完全に生成してください。`,
    }],
  });

  // Claude Code Skillの応答からHTMLを抽出
  const htmlContent = extractHTML(message.content);

  // ファイル保存
  const filename = `generated/${category}-${Date.now()}.html`;
  fs.writeFileSync(filename, htmlContent);

  return filename;
}

// 10個生成
const categories = ['hero', 'services', 'footer', 'cta', 'gallery'];
for (let i = 0; i < 10; i++) {
  const category = categories[i % categories.length];
  await generateDesign(category);
}
```

#### `scripts/screenshot-designs.js`

```javascript
import { chromium } from 'playwright';
import fs from 'fs';

const files = fs.readdirSync('./generated').filter(f => f.endsWith('.html'));

(async () => {
  const browser = await chromium.launch();
  const page = await browser.newPage({
    viewport: { width: 1920, height: 1080 }
  });

  for (const file of files) {
    const htmlPath = `./generated/${file}`;
    await page.goto(`file://${process.cwd()}/${htmlPath}`);

    // スクリーンショット撮影
    const screenshotPath = htmlPath.replace('.html', '.png');
    await page.screenshot({
      path: screenshotPath,
      fullPage: true
    });

    console.log(`Screenshot saved: ${screenshotPath}`);
  }

  await browser.close();
})();
```

#### `scripts/upload-to-storage.js`

```javascript
import { createClient } from '@supabase/supabase-js';
import fs from 'fs';
import path from 'path';

const supabase = createClient(
  process.env.SUPABASE_URL,
  process.env.SUPABASE_SERVICE_KEY
);

const files = fs.readdirSync('./generated');

for (const file of files) {
  const filePath = `./generated/${file}`;
  const fileContent = fs.readFileSync(filePath);

  // Supabase Storageにアップロード
  const { data, error } = await supabase.storage
    .from('designs')
    .upload(`designs/${file}`, fileContent, {
      contentType: file.endsWith('.html') ? 'text/html' : 'image/png',
      upsert: true
    });

  if (error) {
    console.error(`Upload failed: ${file}`, error);
  } else {
    console.log(`Uploaded: ${file}`);
  }
}
```

---

## 認証・決済フロー

### ユーザー登録〜サブスク開始

```
1. ユーザーがメール登録
   ↓
   Supabase Auth → users テーブル作成
   ↓
   profiles テーブル作成（subscription_tier = 'free'）
   ↓
   7日間トライアル開始

2. ユーザーが「Upgrade」クリック
   ↓
   Stripe Checkout Session作成
   ↓
   Stripeの決済ページにリダイレクト
   ↓
   ユーザーがカード情報入力・決済
   ↓
   Stripe Webhook → Next.js API Route
   ↓
   subscriptions テーブル作成/更新
   ↓
   profiles.subscription_tier = 'basic' or 'pro'
   ↓
   ダッシュボードにリダイレクト
```

### Stripe Webhook処理

```typescript
// app/api/webhooks/stripe/route.ts

import { stripe } from '@/lib/stripe';
import { supabase } from '@/lib/supabase';

export async function POST(req: Request) {
  const body = await req.text();
  const sig = req.headers.get('stripe-signature')!;

  const event = stripe.webhooks.constructEvent(
    body,
    sig,
    process.env.STRIPE_WEBHOOK_SECRET!
  );

  switch (event.type) {
    case 'checkout.session.completed':
      const session = event.data.object;
      await handleCheckoutCompleted(session);
      break;

    case 'customer.subscription.updated':
      const subscription = event.data.object;
      await handleSubscriptionUpdated(subscription);
      break;

    case 'customer.subscription.deleted':
      const deleted = event.data.object;
      await handleSubscriptionDeleted(deleted);
      break;
  }

  return new Response(JSON.stringify({ received: true }), {
    status: 200,
  });
}

async function handleCheckoutCompleted(session: any) {
  // subscriptionsテーブルに挿入
  const { error } = await supabase
    .from('subscriptions')
    .insert({
      user_id: session.client_reference_id, // ユーザーID
      stripe_subscription_id: session.subscription,
      status: 'active',
      tier: session.metadata.tier, // 'basic' or 'pro'
      current_period_end: new Date(session.current_period_end * 1000),
    });

  // profilesテーブルも更新
  await supabase
    .from('profiles')
    .update({ subscription_tier: session.metadata.tier })
    .eq('id', session.client_reference_id);
}
```

---

## セキュリティ

### 認証

- Supabase Auth（JWT）
- PKCE（Proof Key for Code Exchange）フロー
- メールリンク認証（パスワードレスオプション）

### データ保護

- PostgreSQL RLS（Row Level Security）
- 環境変数でシークレット管理（Vercel環境変数）
- Stripe PCI DSS準拠（決済情報は保存しない）

### API保護

- Rate Limiting（Vercel Edge Functions）
- CORS設定
- CSRF対策（Next.js組み込み）

---

## モニタリング・ログ

### エラートラッキング

- Sentry（推奨）
- または Vercel Analytics

### メトリクス

- Vercel Analytics（パフォーマンス）
- Supabase Dashboard（DB使用量）
- Stripe Dashboard（MRR、チャーン率）

### ログ

- Vercel Logs（アプリケーションログ）
- Supabase Logs（DB クエリログ）
- GitHub Actions Logs（自動生成ログ）

---

## スケーリング戦略

### フェーズ1: 0-100ユーザー

- Supabase Free tier
- Vercel Pro（既存）
- 自動生成: 週10個

### フェーズ2: 100-500ユーザー

- Supabase Pro ($25/月)
- CDN最適化（Vercel Edge）
- 自動生成: 週20個

### フェーズ3: 500-2000ユーザー

- Supabase Pro（スケールアップ）
- Redis キャッシング導入
- 画像CDN（Cloudinary等）
- 自動生成: 週30個

### フェーズ4: 2000+ユーザー

- Supabase Selfhost検討（Synology NAS）
- マルチリージョン展開
- 専用インフラ検討

---

## 開発環境

### ローカル開発

```bash
# 環境構築
npm install
cp .env.example .env.local

# ローカルサーバー起動
npm run dev

# Supabase ローカル起動（オプション）
npx supabase start

# Playwrightテスト
npx playwright test
```

### 必要な環境変数

```env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=https://xxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJxxx...
SUPABASE_SERVICE_KEY=eyJxxx...

# Stripe
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_xxx
STRIPE_SECRET_KEY=sk_test_xxx
STRIPE_WEBHOOK_SECRET=whsec_xxx

# Claude Code
CLAUDE_API_KEY=sk-ant-xxx

# Replicate
REPLICATE_API_KEY=r8_xxx

# Resend
RESEND_API_KEY=re_xxx
```

---

## 次のステップ

### 技術検証（1週間）

- [ ] Supabase Free tierセットアップ
- [ ] Stripe テストモード設定
- [ ] Claude Code API テスト（デザイン1個生成）
- [ ] Playwright スクリーンショットテスト

### MVP開発（3-4週間）

- [ ] Next.js プロジェクト作成
- [ ] 認証機能実装
- [ ] デザイン一覧ページ
- [ ] ダウンロード機能
- [ ] Stripe課金統合

### 自動化構築（2週間）

- [ ] GitHub Actions設定
- [ ] 自動生成スクリプト
- [ ] メール配信自動化

---

**文書作成者**: Claude Code
**作成日**: 2025年11月15日
**次回更新**: 技術検証完了時
