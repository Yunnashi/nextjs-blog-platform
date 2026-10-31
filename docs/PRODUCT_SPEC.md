# Product specification

## 1. Product statement

技術記事と case study を、読みやすく、探しやすく、アクセシブルに届ける publishing platform。読者には静かな読書体験を、書き手には安全で迷いの少ない編集体験を提供します。

## 2. Portfolio statement

この作品が証明する中心は frontend engineering です。server-side はNext.jsのServer Components、Server Actions、Route Handlers、Use Case、DALで構成し、公開・検索・認証・コメントを成立させます。

成果の評価軸：

1. UI / UX / accessibility — 30%
2. React / Next.js architecture — 25%
3. Testing / quality assurance — 15%
4. Measurement / performance — 15%
5. PostgreSQL / Prisma / authentication — 15%

## 3. Target users

### Reader

- 技術記事を mobile / desktop で読む
- topic や keyword から記事を見つける
- 後で参照できる分かりやすい URL を共有する
- 必要なら login してコメントする

### Author / site owner

- draft を安全に保存する
- 公開前の見た目と metadata を確認する
- 記事を公開・更新・archive する
- 不適切なコメントを moderation する
- 最低限の運用状態を dashboard で把握する

### Reviewer / recruiter

- 数分で完成度と技術範囲を理解する
- public demo を credential なしで確認する
- README から設計判断、品質、改善結果へ辿る
- repository を起動し、主要テストを再現できる

## 4. Information architecture

```text
Public
├── Home
├── Articles
│   ├── Search results
│   └── Article detail
├── Topics
├── Work / Case studies
├── About
└── Sign in

Authenticated reader
├── Profile
└── Sessions (later phase)

Author
├── Overview
├── Posts
│   ├── Drafts
│   ├── Published
│   ├── New / Edit
│   └── Preview
├── Comments
└── Settings
```

## 5. Page requirements

### Home

- 一つの featured article を大きく表示
- 最新記事 3–6件
- topics への導線
- case study / profile への導線
- 初回表示で content shift を起こさない画像領域
- decorative content と semantic content を分離

### Article list / search

- `q`, `category`, `tag`, `sort`, `cursor` を URL で表現
- Back / Forward で状態が復元される
- 検索中、0件、query error、追加読込失敗を別々に表現
- filter を解除しやすくし、結果件数を読み上げる
- mobile では filter dialog、desktop では sidebar または toolbar

### Article detail

- title、excerpt、author、published / updated date、reading time
- responsive cover image
- semantic heading hierarchy と table of contents
- syntax-highlighted code と copy button
- keyboard accessible share action
- related articles
- comment section
- metadata、canonical URL、Open Graph image、structured data
- raw HTML を直接挿入しない controlled renderer

### Author editor

- title / slug / excerpt / Markdown / cover / category / tags / SEO fields
- field level validation と submit summary
- autosave は phase 2。最初は明示的な Save draft
- unsaved changes の navigation warning
- desktop split preview と mobile preview toggle
- draft / publish action の違いを視覚と文言で明確化
- publish は confirmation dialog を通し、成功後の公開 URL を表示

### Dashboard

- drafts、published posts、pending comments の件数
- recent posts / comments
- analytics product の再実装はしない
- chart を使う場合は同じ情報を text / table でも提供

## 6. Design direction

### Visual principles

- Editorial, calm, precise
- 8px grid を基準にした余白
- content width は本文 65–75 characters 程度
- 大見出しは serif、UI と本文は sans、code は mono
- 色ではなく type size、weight、spacing、border でも状態を伝える
- motion は意味のある transition のみにし、`prefers-reduced-motion` を尊重

### Draft design tokens

| Token | Light | Dark | Purpose |
|---|---|---|---|
| canvas | `#F7F6F2` | `#0D0F12` | page background |
| surface | `#FFFFFF` | `#15181D` | card / dialog |
| ink | `#16181D` | `#F3F1EA` | primary text |
| muted | `#686B73` | `#A5A8B0` | secondary text |
| line | `#DAD8D1` | `#30343B` | border |
| accent | `#6D4AFF` | `#9B86FF` | focus / active / link |
| danger | `#B42318` | `#FF8A80` | destructive / error |

contrast は実装時に実測して調整します。この値を合格値とはみなしません。

### Component inventory

- Button / IconButton / Link
- TextField / Textarea / Select / Combobox
- Badge / Tag / Status
- ArticleCard / FeaturedArticle
- Dialog / DropdownMenu / Toast
- EmptyState / ErrorState / Skeleton
- Pagination / FilterBar
- MarkdownRenderer / CodeBlock / TableOfContents
- AdminDataTable / ConfirmAction

## 7. Core user stories and acceptance criteria

### Browse published posts

**Given** published posts exist  
**When** a reader opens the home or article list  
**Then** only published posts are shown in deterministic order, images reserve their layout space, and each card has a meaningful accessible name.

### Search and filter

**Given** a reader is on `/articles`  
**When** they search and select a tag  
**Then** the URL reflects the state, results update, focus remains predictable, and reload / Back restores the same view.

### Publish an article

**Given** an author has a valid draft  
**When** they confirm Publish  
**Then** status and `publishedAt` change atomically, the public cache is invalidated, and the public URL shows the article.

### Prevent unauthorized editing

**Given** a reader or another author invokes an update action  
**When** the Use Case checks role and ownership  
**Then** it returns a forbidden result and does not mutate the post.

### Comment safely

**Given** an authenticated reader views a published post  
**When** they submit valid content  
**Then** the Server Action associates the current session user rather than trusting a client-supplied user id, stores plain content, and reflects moderation status.

## 8. Non-functional requirements

### Accessibility

- WCAG 2.2 AA を目標
- 全操作を keyboard で完了可能
- visible focus、skip link、landmark、heading order
- validation error は field と summary の両方から辿れる
- dialog open / close 時の focus management
- theme、motion、zoom 200% で内容を失わない

### Performance

- public route の Client JavaScript を最小化
- cover image は適切な size / format / priority
- font subset と fallback で layout shift を抑える
- search は server-side pagination
- 10,000件 seed でServer Component responseとdatabase query planを計測
- measurement は条件と raw result を `docs/measurements/` に保存

### Security and privacy

- password は強い password hash を使用し、平文や log に残さない
- session cookie は HttpOnly / Secure / SameSite を環境に応じて設定
- authorization はUI非表示ではなくServer Action / Route Handler / Use Caseで強制
- client-supplied `userId` を ownership の根拠にしない
- rate limit: login、comment、password reset
- Markdown renderer の link / image protocol を allowlist
- secret と個人情報を structured log に含めない

### Reliability

- Server Action resultとRoute Handler error responseを統一
- request / correlation idでserver logを追跡
- health Route Handlerとdatabase readiness
- migration は review → staging apply → production apply
- backup / restore 手順は deployment 決定後に文書化

## 9. MVP boundary

MVP は次の縦切りが成立した状態です。

- Author login
- Draft CRUD
- Preview / publish
- Public home / list / detail
- Category / tag filter と basic search
- Responsive UI、dark mode、主要 loading / error states
- 主要Use Case / DAL integration testとpublish E2E
- README、architecture diagram、ER diagram、2件以上の ADR

コメント、like、dashboard 集計、advanced search は MVP 後です。
