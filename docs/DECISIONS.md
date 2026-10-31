# Architecture decision drafts

実装開始時に各項目を`docs/adr/NNNN-*.md`へ分け、Status / Context / Decision / Consequences / Alternativesを記録します。

## ADR-001: Use Next.js as the full-stack application boundary

**Status:** Accepted

**Decision:** 独立したNestJS / Express backendを置かず、Next.js App Router、Server Actions、Route Handlers、Prisma、PostgreSQLで構築する。

**Why:** ブログ規模では別APIが必須ではなく、Server Components、cache、metadata、ActionsなどNext.jsのserver-side機能を深く学ぶ目的に合う。

**Consequences:** frameworkとの結合は強くなる。business ruleとdata accessをUse Case / DALに分け、App Router fileへ散らさない。

**Rejected:** Next.js + NestJS。外部clientや組織分離の要件がなく、deploy、auth、cache invalidation、contract管理のコストが目的を上回る。

## ADR-002: Use a Data Access Layer

**Status:** Accepted

**Decision:** Server Component、Action、Route HandlerからPrismaを直接呼ばず、Use Case → DAL → Prismaの依存方向にする。

**Why:** authorization、query selection、private field filtering、Prisma error mappingを一箇所で確認できる。

**Consequences:** 小規模なCRUDにも追加layerが生じる。単なるpass-through abstractionは作らず、security / query policyを持つ単位だけをDAL化する。

## ADR-003: Scope Redux Toolkit to the admin subtree

**Status:** Accepted

**Decision:** Redux Providerは`/admin`へ限定し、`editorSlice`と`uiSlice`を持つ。公開記事、session、URL filterはReduxへ入れない。

**Why:** editor workflowと横断UI stateでReduxを実践しつつ、public Server Componentsの利点とclient bundleを守る。

**Consequences:** server snapshotからstoreを初期化し、route / draft変更時のresetが必要。Storeはserver global singletonにしない。

## ADR-004: Prefer Server Actions for internal mutations

**Status:** Accepted

**Decision:** Next.js UIからのみ呼ぶmutationはServer Actionsを使う。HTTP semanticsが必要なupload、webhook、health、将来の外部APIだけRoute Handlersを使う。

**Why:** 内部mutationのためだけにREST endpointとclient fetch layerを作らず、validation、redirect、revalidationを同じserver boundaryで扱える。

**Consequences:** Actionは独自API contractではない。同じUse Caseを外部公開する必要が生じた場合はRoute Handlerを追加する。

## ADR-005: PostgreSQL and Prisma with reviewed migrations

**Status:** Accepted

**Decision:** relation / constraintはPostgreSQLをsource of truthとし、Prismaをapplication data accessとmigration workflowに使用する。ローカルのPostgreSQLはDocker Composeで起動し、本番はmanaged PostgreSQLを使用する。

**Why:** 参考版でstringだったrelationをforeign keyとcomposite constraintで保証し、index / transactionを説明できる。

**Consequences:** `compose.yml`でPostgreSQL version、health check、volumeを管理する。Next.jsはローカルではホスト上で起動する。generated migrationをreviewし、extensionや特殊indexにはcustom SQL migrationを使用する。

## ADR-006: Store article content as Markdown

**Status:** Proposed

**Decision:** article bodyはMarkdown sourceとして保存し、許可したelement / protocolだけをcontrolled rendererで描画する。

**Why:** developer articleのcode blockと相性がよく、任意HTMLの保存・描画を避けられる。

**Consequences:** WYSIWYGほど直感的ではない。実際のauthor needが確認された場合のみvisual editorを再評価する。

## ADR-007: Use server-managed sessions

**Status:** Proposed

**Decision:** maintained auth libraryを優先し、HttpOnly cookieとserver sessionでauthentication / session managementを行う。

**Why:** browser JavaScriptやReduxへcredentialを保存せず、Server Component / Action / Route Handlerで同じsessionを検証できる。

**Consequences:** session storage、expiry、revoke、CSRF方針をintegration testする。libraryとPrisma versionの互換性をscaffold時に確認する。

## ADR-008: URL is the source of truth for article discovery

**Status:** Accepted

**Decision:** `q`, `category`, `tag`, `sort`, `cursor`はURL search paramsで表現し、Reduxに複製しない。

**Why:** share、reload、Back / Forward、Server Component queryと相性がよい。

**Consequences:** query normalizationとinvalid param fallbackが必要。入力中queryはlocal state、確定条件はURLに分ける。

## ADR-009: Add PostgreSQL search optimization only after measurement

**Status:** Proposed

**Decision:** MVP searchを最小実装し、日本語・英語を含む10,000件seedでquery planとlatencyを測る。必要なら`pg_trgm` / GIN indexをSQL migrationで追加する。

**Why:** 検索基盤を先に複雑化せず、改善をbefore / afterで示せる。

**Consequences:** 初期searchがlarge datasetで遅い可能性がある。性能budgetを超えた時点でmeasurementとADRを更新する。
