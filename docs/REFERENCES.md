# Official references

2026-10-30時点の設計資料です。実装時は使用versionの一次資料を再確認します。

## Next.js

- [Next.js Learn: Dashboard App](https://nextjs.org/learn/dashboard-app) — App Routerを使うfull-stack tutorial
- [Layouts and Pages](https://nextjs.org/docs/app/getting-started/layouts-and-pages) — file-system routing、page、shared layout
- [Server and Client Components](https://nextjs.org/docs/app/getting-started/server-and-client-components) — public server-first / admin client boundary
- [Fetching Data](https://nextjs.org/docs/app/getting-started/fetching-data) — Server Component、ORM / DB access、streaming
- [Mutating Data](https://nextjs.org/docs/app/getting-started/mutating-data) — Server Actions / Server Functionsとmutationのsecurity boundary
- [Route Handlers](https://nextjs.org/docs/app/getting-started/route-handlers) — HTTP endpointが必要な処理
- [Data Security](https://nextjs.org/docs/app/guides/data-security) — DAL、DTO、RSC payload、server-only data
- [Authentication](https://nextjs.org/docs/app/guides/authentication) — authentication / session / authorizationの分離
- [Caching](https://nextjs.org/docs/app/getting-started/caching) — cacheとrevalidation model
- [Production Checklist](https://nextjs.org/docs/app/guides/production-checklist) — release review

## Redux

- [Redux Essentials](https://redux.js.org/tutorials/essentials/part-1-overview-concepts) — Redux未経験者向けのconcept / Toolkit tutorial
- [Redux Toolkit Setup with Next.js](https://redux.js.org/usage/nextjs) — per-request store、RSC非依存、mutable global client stateへの限定
- [Redux Toolkit](https://redux-toolkit.js.org/) — slice、selector、middleware、testing

## Prisma and PostgreSQL

- [PostgreSQL Tutorial](https://www.postgresql.org/docs/current/tutorial.html) — table、join、aggregate、foreign key、transaction
- [PostgreSQL Indexes](https://www.postgresql.org/docs/current/indexes.html) — index type、multicolumn、partial、index-only scan
- [Prisma ORM](https://www.prisma.io/docs/orm) — scaffold時点のstable workflow
- [Prisma migrations](https://www.prisma.io/docs/orm/migrations/how-migrations-work) — migration plan / review / apply
- [Prisma transactions](https://www.prisma.io/docs/orm/fundamentals/transactions) — publication transaction
- [PostgreSQL full-text indexes](https://www.postgresql.org/docs/current/textsearch-indexes.html) — GIN / GiST
- [`pg_trgm`](https://www.postgresql.org/docs/current/pgtrgm.html) — similarity / partial search

## React and web quality

- [Creating a React App](https://react.dev/learn/creating-a-react-app) — framework-firstの位置づけ
- [React Hook Form: Get Started](https://react-hook-form.com/get-started) — accessible form state / validation integration
- [Zod Basics](https://zod.dev/basics) — server-side validationと型推論
- [Vitest Guide](https://vitest.dev/guide/) — unit / integration test runner
- [Testing Library Guiding Principles](https://testing-library.com/docs/guiding-principles/) — user-visible behavior中心のtest
- [Playwright Best Practices](https://playwright.dev/docs/best-practices) — isolatedなcritical E2E
- [Learn Accessibility](https://web.dev/learn/accessibility/) — semantic HTML、focus、form、contrast、manual test
- [Learn Performance](https://web.dev/learn/performance/) — critical path、resource、rendering、measurement
- [WCAG 2.2](https://www.w3.org/TR/WCAG22/) — AA acceptance criteria
- [WAI-ARIA Authoring Practices](https://www.w3.org/WAI/ARIA/apg/) — dialog、menu、combobox patterns
- [Web Vitals](https://web.dev/articles/vitals) — LCP / INP / CLS

## Delivery

- [GitHub Actions: Building and testing Node.js](https://docs.github.com/en/actions/tutorials/build-and-test-code/nodejs) — Node.js CI workflow
- [Udemy reference course](https://www.udemy.com/course/full-stack-mern-blog-with-admin-dashboard/) — feature implementation orderの参照元。MERN固有実装は移植しない

## Version policy

- Node.jsはNext.jsとPrismaが共通してsupportするactive LTSを選ぶ
- `latest`のまま運用せず、scaffold時にexact versionとlockfileをcommit
- major upgradeはfeature実装と同じpull requestへ混ぜない
- READMEのcommandはclean checkoutで確認したものだけを載せる
- cache / auth / migrationに影響するversion changeはADRを更新する
