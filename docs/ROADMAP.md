# 4週間の学習・実装ロードマップ

## 目的

この計画は、参考講座 [Full Stack MERN Blog with Admin Dashboard](https://www.udemy.com/course/full-stack-mern-blog-with-admin-dashboard/learn/lecture/41453708) の機能追加順を参考にしつつ、実装を次の構成へ置き換えて1か月で完成させるためのものです。

```text
Next.js App Router
├── 公開画面: Server Components
├── 管理画面: Client Components + Redux Toolkit
├── Server Actions / Route Handlers
├── Service / Use Case
├── Data Access Layer
├── Prisma
└── PostgreSQL
```

講座はMERN構成のため、Express、MongoDB、Mongoose、React Routerの実装方法は採用しません。参考にするのは、`画面の土台 → DB → 認証 → Redux → 管理画面 → 記事CRUD → 公開画面 → 検索 → デプロイ` という学習・開発順序です。Next.js固有の実装は、講座ではなく公式ドキュメントを正とします。

## 前提時間と完了戦略

- 期間: 4週間
- 稼働: 週6日 × 5時間 = 週30時間、合計120時間
- 1日の目安: 学習60分、実装3時間、テスト・計測・記録60分
- 7日目: 休息、遅れの吸収、週次レビューのいずれか
- 学習は実装直前に必要な範囲だけ行い、同じ日にコードと説明資料へ反映する
- 第2週末の縦切り完成を最優先とし、全画面を薄く作ることを優先しない

1か月で「学習」と「ポートフォリオ品質」を両立させるには、週30時間程度が必要です。週15〜20時間しか確保できない場合は、後述の「縮小ルール」を適用します。

## 時間配分

| 領域 | 比率 | 4週間 | 完成時に説明できること |
|---|---:|---:|---|
| UI実装・UX・アクセシビリティ | 30% | 36h | design token、responsive、keyboard、focus、状態設計 |
| React / Next.js設計 | 25% | 30h | rendering、RSC境界、mutation、cache、URL state |
| テスト・品質保証 | 15% | 18h | test pyramid、user-visible behavior、CI、E2E |
| パフォーマンス | 15% | 18h | Core Web Vitals、bundle、render、query、before/after |
| PostgreSQL・Prisma・認証 | 15% | 18h | relation、index、migration、transaction、認証・認可 |
| **合計** | **100%** | **120h** | |

### 週ごとの配分

| Week | UI / UX / a11y | Next.js | Test | Performance | Backend | Total |
|---|---:|---:|---:|---:|---:|---:|
| 1 | 8h | 10h | 3h | 2h | 7h | 30h |
| 2 | 10h | 8h | 5h | 2h | 5h | 30h |
| 3 | 11h | 6h | 5h | 4h | 4h | 30h |
| 4 | 7h | 6h | 5h | 10h | 2h | 30h |
| **合計** | **36h** | **30h** | **18h** | **18h** | **18h** | **120h** |

## 講座セクションと本プロジェクトの対応

2026-10-30に確認した講座のセクション順を、次のように読み替えます。

| 講座の順序 | 本プロジェクトで学ぶ・作るもの | 扱う週 |
|---|---|---:|
| 1. Introduction / Installation | 要件、App Router、TypeScript、Tailwind、品質基準 | 1 |
| 2. Pages / Routes / Header | route group、layout、Server / Client Components、header | 1 |
| 3. Server / Database | Next.js server-side、DAL、Prisma、PostgreSQL | 1 |
| 4. Sign up | Server Action、RHF、Zod、password、User | 1 |
| 5. Footer | public layout、semantic HTML、responsive UI | 1 |
| 6. Sign in | session、protected route、認証と認可 | 1 |
| 7. Redux Toolkit / Persist | admin限定store、`editorSlice`、`uiSlice` | 1〜2 |
| 8. OAuth / Header / Dark mode | theme、header state。OAuthは余裕がある場合のみ | 1 |
| 9. Dashboard | admin shell、navigation、overview | 2 |
| 10. Create / Update post | Use Case、transaction、Server Action、editor | 2 |
| 11. Dashboard users | user listとrole管理。遅延時はpost-MVP | 3 |
| 12. Post page | article RSC、metadata、comment / reaction | 2〜3 |
| 13. Comments / Overview | moderation、集計、admin dashboard | 3 |
| 14. Home | editorial home、featured / recent posts | 3 |
| 15. Search / About / Projects | URL state、pagination、index、portfolio pages | 3 |
| 16. Deployment | CI、migration、production audit、公開 | 4 |

## Week 1 — 基盤、レンダリング、DB、認証、Redux

**週のゴール:** public / adminの骨格、PostgreSQLへの接続、sign up / sign in、admin限定Redux storeまでを動かす。

### Day 1 — 要件固定とNext.js全体像

**学習**

- [Next.js Learn: Dashboard App](https://nextjs.org/learn/dashboard-app) のOverviewとGetting Started
- [Layouts and Pages](https://nextjs.org/docs/app/getting-started/layouts-and-pages)
- [Server and Client Components](https://nextjs.org/docs/app/getting-started/server-and-client-components)
- [Fetching Data](https://nextjs.org/docs/app/getting-started/fetching-data)
- renderingを「SSR / CSRの二択」ではなく、RSC、Client Component、prerender、dynamic renderingの責務として整理する

**実装**

- exact versionとlockfileを固定し、App Router / TypeScript / Tailwindを初期化
- `(public)`、`(auth)`、`admin`のroute groupとlayoutを作成
- Server / Client境界、状態の置き場所、data flowを図にする
- ADR: 公開画面をserver-first、管理画面をclient-richにする理由

**完了条件:** route mapとrendering方針を、自分の言葉でREADMEから説明できる。

### Day 2 — Header、Footer、画面状態、アクセシビリティ

**学習**

- [web.dev Learn Accessibility](https://web.dev/learn/accessibility/) のHTML / content structure / keyboard focus
- [WAI-ARIA APG](https://www.w3.org/WAI/ARIA/apg/) は必要なpatternだけ参照

**実装**

- color、type、spacing、radius、shadowのdesign token
- header、footer、skip link、navigation、focus style
- loading / empty / error / successの共通表示方針
- mobile / tablet / desktopの最小wireframe

**完了条件:** keyboardのみでnavigationでき、200% zoomでも主要layoutが破綻しない。

### Day 3 — PostgreSQL、Prisma、DAL

**学習**

- [PostgreSQL Tutorial](https://www.postgresql.org/docs/current/tutorial.html) のtable、join、aggregate、foreign key、transaction
- [Prisma ORM: PostgreSQL](https://www.prisma.io/docs/orm/overview/databases/postgresql)
- [Prisma Migrations](https://www.prisma.io/docs/orm/migrations/how-migrations-work)

**実装**

- `compose.yml`でversion固定したPostgreSQL、health check、persistent volumeを定義
- development / integration test databaseを分離し、`.env.example`へ接続形式を記載
- `User`、`Post`、`Category`、`Tag`とrelation / unique constraint / indexを設計
- 初回migration、seed、Prisma client lifecycle
- `server-only` DALと、UIへ返すsafe DTO
- ER図と「なぜこのindexが必要か」の仮説を記録

**完了条件:** migrationからDBを再現でき、Server ComponentがDAL経由でseed dataを表示する。

### Day 4 — Sign up / Sign in、Validation、認可

**学習**

- [Next.js Authentication Guide](https://nextjs.org/docs/app/guides/authentication)
- [Next.js Data Security](https://nextjs.org/docs/app/guides/data-security)
- [Zod Basics](https://zod.dev/basics) と [React Hook Form Get Started](https://react-hook-form.com/get-started)

**実装**

- sign up / sign in formをRHFで実装し、Server ActionでZod再検証
- maintained auth libraryでsessionを構成
- admin layoutで認証、Use Caseでrole / ownershipを再検証
- field error、form error、pending、成功後focus / navigationを設計

**完了条件:** 未認証アクセスと権限外mutationをserver側で拒否するintegration testが通る。

### Day 5 — Redux Toolkitをゼロから学ぶ

**学習**

- [Redux Essentials](https://redux.js.org/tutorials/essentials/part-1-overview-concepts) のPart 1〜3
- [Redux Toolkit Setup with Next.js](https://redux.js.org/usage/nextjs)

**実装**

- Providerを`/admin`配下だけに配置し、requestごとにstoreを生成
- `editorSlice`: draft、dirty、save status、preview state
- `uiSlice`: sidebar、dialog、toast、table selection
- reducer / selector testを作成
- session、public data、search filterをReduxへ入れない理由をADRに残す

**完了条件:** Redux DevToolsでactionとstate transitionを説明でき、RSCがstoreを読まない。

### Day 6 — Theme、CI、週次統合

- light / dark / system themeとcontrast、reduced motionを確認
- lint、typecheck、Vitest、buildをGitHub Actionsで実行
- public → auth → adminのsmoke test
- Week 1で生じた設計判断をADRへ反映
- Google OAuthはcore flow完成後に時間があれば追加

**Week 1 Exit Criteria**

- clean checkoutからsetup、migration、seed、起動ができる
- sign in → protected admin pageが動く
- publicはServer Component、interactive adminは必要箇所だけClient Component
- admin storeに`editorSlice`と`uiSlice`があり、reducer testが通る
- CIでlint / typecheck / test / buildが通る

## Week 2 — 管理画面、記事CRUD、公開記事の縦切り

**週のゴール:** `sign in → draft作成 → preview → publish → public article` をproduction品質で完成する。

### Day 1 — Dashboard shell

- admin sidebar、breadcrumb、responsive navigation、overview card
- `uiSlice`でsidebar / dialogを管理
- focus trap、Escape、focus return、screen reader labelを確認
- Client Componentの境界を、操作が必要なleafへ寄せる

### Day 2 — Post Use Caseとcreate draft

- `CreateDraft`、`GetPostForEdit`、`ListAdminPosts`を実装
- slug / status / author / timestampsのconstraintとindex
- Server Actionはvalidation・session取得・Use Case呼び出し・結果整形だけにする
- success / validation / conflict / unexpected errorを型で区別

### Day 3 — Editorとpreview

- RHF + Zod + `editorSlice`でtitle、summary、body、cover、category、tags、SEO fields
- accessible label、description、error summary、pending state
- unsaved changes warningとpreview route
- editor component testとreducer test

### Day 4 — Update / Publish / Cache

**学習:** [Mutating Data](https://nextjs.org/docs/app/getting-started/mutating-data)、[Caching](https://nextjs.org/docs/app/getting-started/caching)

- update / publishをtransactionで実装
- ownership / role / current statusをUse Caseで再検証
- publish後に必要なpath / tagだけrevalidate
- duplicate submit、stale update、transaction rollbackをtest

### Day 5 — Public article page

- article detailをServer Componentで実装
- semantic article、heading hierarchy、TOC、code、responsive image
- metadata、canonical、Open Graph、not-found
- related postsをDALで取得し、N+1を作らない

### Day 6 — 縦切りのテストとUX polish

**学習**

- [Testing Library Guiding Principles](https://testing-library.com/docs/guiding-principles/)
- [Playwright Best Practices](https://playwright.dev/docs/best-practices)

**検証**

- component: editor validation、dialog、navigation
- integration: create / update / publish、authorization、rollback
- E2E: sign in → create → preview → publish → public view
- keyboard、focus、loading / empty / error / success、mobile viewport

**Week 2 Exit Criteria**

- 最初の縦切りがE2Eで通る
- private fieldがRSC payload、Redux、logへ出ない
- server validationとauthorizationをUIから迂回できない
- publish transaction失敗時に中途半端な状態が残らない
- 記事画面はJavaScriptを最小化し、keyboardで読める

## Week 3 — Home、検索、コメント、管理機能、portfolio pages

**週のゴール:** 読者体験と運用体験をつなぎ、主要機能をfeature completeにする。

### Day 1 — Homeと記事一覧

- featured、recent、category / tag導線をeditorialなlayoutで実装
- article card、skeleton、empty / error state
- `next/image`、font、responsive、dark mode、200% zoomを確認
- public dataはServer Componentから直接DAL / Use Caseを呼ぶ

### Day 2 — Search、Filter、Pagination

**学習:** [PostgreSQL Indexes](https://www.postgresql.org/docs/current/indexes.html)、必要なら [`pg_trgm`](https://www.postgresql.org/docs/current/pgtrgm.html)

- query、category、tag、sort、cursorをURL stateにする
- share、reload、Back / Forwardで状態を保持
- Zodでsearch paramsを正規化
- deterministic cursor paginationを実装
- 10,000件seedで`EXPLAIN ANALYZE`し、index前後を記録

### Day 3 — Comment / Reaction

- comment / reactionのmodel、relation、constraint、index
- create / delete / reactのUse Caseとauthorization
- interactive islandだけClient Component化
- optimistic UIを使う操作と使わない操作の理由を記録
- screen reader announcement、失敗時rollback、focus recovery

### Day 4 — Users / Comments / Overview

- admin user list、role変更、comment moderation、overview aggregation
- admin tableのresponsive、sort / filter、bulk selection
- cross-user mutationとrole escalationのsecurity test
- 遅れている場合、user管理UIは削り、DB / Use Case設計だけ残す

### Day 5 — About / WorkとUI仕上げ

- About、Work、project case studyへの導線
- typography、spacing、motion、empty / error copyを統一
- mobile / tablet / desktop、light / dark、reduced motion
- keyboard、focus order、contrast、touch target、200% zoom

### Day 6 — Feature complete test

- Vitest + Testing Libraryでform、filter、dialog、editor、Reduxを検証
- PostgreSQLを使うintegration testでconstraint / transaction / authorizationを検証
- Playwrightで公開閲覧、検索、投稿、コメントのcritical flowを3〜5本に限定
- Chrome / Firefox / WebKitでsmoke test

**Week 3 Exit Criteria**

- MVP機能を追加し終え、Week 4では新機能を作らない
- URL stateがshare / reload / browser navigationに耐える
- 10,000件seedでquery planとpaginationのcorrectnessを確認
- critical E2Eが独立して安定実行できる
- UI inventoryの主要状態とviewportを確認済み

## Week 4 — 品質強化、計測、デプロイ、作品化

**週のゴール:** 「動くブログ」から「設計と品質を証拠で説明できるポートフォリオ」へ仕上げる。

### Day 1 — Feature freezeと品質リスク整理

- 新機能を止め、risk matrixでtest gapを整理
- flaky / implementation-detail testを修正
- CIをlint / typecheck / unit / integration / E2E / buildへ整理
- loading / empty / error / successと権限別flowを再確認

### Day 2 — Accessibility audit

**学習:** [web.dev Learn Accessibility](https://web.dev/learn/accessibility/) のform、image、color、motion、test sections

- axeのcritical violationを0にする
- keyboardのみでpublic / auth / admin / editor / dialogを操作
- VoiceOverなどで主要flowをmanual確認
- 200% zoom、contrast、reduced motion、error announcement
- 自動testで保証できる範囲とmanual確認が必要な範囲をレポート化

### Day 3 — Frontend performance lab

**学習**

- [web.dev Learn Performance](https://web.dev/learn/performance/)
- [Next.js Production Checklist](https://nextjs.org/docs/app/guides/production-checklist)

**計測**

- Lighthouse / Core Web Vitals、bundle analyzer、React Profilerでbaselineを保存
- RSC / Client境界、image、font、third-party、hydration、renderを一項目ずつ改善
- measurement条件を固定し、before / after / trade-offを記録
- 目標: LCP p75 ≤ 2.5s、INP p75 ≤ 200ms、CLS p75 ≤ 0.1

### Day 4 — DB / Server performance lab

- 10,000 posts / commentsの再現可能seed
- slow query logと`EXPLAIN (ANALYZE, BUFFERS)`で重いqueryを特定
- N+1、over-fetch、count / aggregation、pagination、indexを確認
- cache / revalidationのcorrectnessとstale dataの許容範囲を記録
- 最低2件のbottleneckを数値つきbefore / afterにする

### Day 5 — Deploymentとproduction readiness

**学習:** [GitHub Actions: Building and testing Node.js](https://docs.github.com/en/actions/tutorials/build-and-test-code/nodejs)、[Next.js Production Checklist](https://nextjs.org/docs/app/guides/production-checklist)

- preview / production environmentとsecretを分離
- migration deployとforward-fix手順
- cookie、CSRF、security headers、rate limit、error responseを確認
- structured log、request id、error monitoring、health Route Handler
- deploy後のsmoke E2Eとrollback / recoveryメモ

### Day 6 — Portfolio packaging

- READMEを `Problem → Constraints → Decisions → Implementation → Measurement → Result → Trade-offs` で構成
- architecture図、ER図、data flow、test strategy、ADR 4〜7件
- UI screenshotと60〜90秒のdemo video
- performance before / after、accessibility test log、test matrix
- clean checkoutで第三者が再現できることを最終確認

**Week 4 Exit Criteria**

- 公開URLでcritical E2Eが通る
- critical axe violation 0 + manual accessibility logがある
- performance改善を条件と数値つきで2件以上説明できる
- schema / relation / index / transaction / authz判断を説明できる
- READMEからdemo、architecture、ADR、test、a11y、performance evidenceへ辿れる

## MVPと縮小ルール

遅れた場合は品質を削らず、機能を次の順で削ります。

### 必ず完成させるCore MVP

1. Home / article list / article detail
2. Sign inとadmin protection
3. Draft create / edit / preview / publish
4. Search / filter / pagination
5. responsive、dark mode、keyboard、主要error state
6. unit / integration / critical E2E、CI
7. performance / accessibilityの計測記録
8. production deploymentとREADME

### 最初に削るもの

1. Google OAuth
2. autosave、rich textの高度な機能
3. admin user management UI
4. reaction
5. commentとmoderation
6. advanced search、複雑なanalytics

週15〜20時間の場合は、最初から3〜5をpost-MVPとします。Reduxの学習は残し、editor / UI stateの小さな実例とtestまでに絞ります。

## 学習成果の確認質問

各週末に、コードを見ずに次を説明します。答えられない項目だけ翌週の最初に復習します。

### Week 1

- なぜこのcomponentはServer / Clientなのか
- request、render、mutation、revalidationの流れはどうなるか
- Reduxへ入れる状態と入れない状態の基準は何か
- authenticationとauthorizationはどこで行うか

### Week 2

- Server Action、Route Handler、Use Case、DALをどう使い分けるか
- publish transactionが守るinvariantは何か
- client bundleへ含めないdata / codeは何か
- testをunit / component / integration / E2Eへどう配分したか

### Week 3

- filterをReduxではなくURLへ置いた理由は何か
- cursor paginationの安定性を何で保証するか
- optimistic UIの適用条件とrollback方法は何か
- relation / indexがquery patternとどう対応するか

### Week 4

- どの計測からbottleneckを特定し、何を変えたか
- Lighthouseの点数ではなく、ユーザー体験をどう改善したか
- 自動a11y testだけでは不足する理由は何か
- 現時点のtrade-offと、あと2週間あれば何を改善するか

## 全機能共通のDefinition of Done

- acceptance criteriaを満たす
- loading / empty / error / successを確認
- responsive / keyboard / focus / 200% zoomを確認
- inputをserverで再検証
- Action / HandlerだけでなくUse Caseで認可
- appropriate test、またはtestしない理由がある
- private dataをclient / Redux / logへ出さない
- cache / revalidation / Redux stateの整合性を確認
- docs、schema、migrationが同期

## 最終品質目標

| Area | Initial target |
|---|---:|
| LCP | p75 ≤ 2.5s |
| INP | p75 ≤ 200ms |
| CLS | p75 ≤ 0.1 |
| Accessibility | critical axe violation 0 + keyboard / screen reader manual check |
| Article query | p95 ≤ 300ms with documented seed and environment |
| CI | lint / typecheck / unit / integration / E2E / build required |

数値はpreview / production相当環境で測定し、device、network、dataset、tool versionを必ず記録します。
