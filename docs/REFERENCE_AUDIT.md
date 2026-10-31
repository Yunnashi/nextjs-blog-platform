# Reference MERN blog audit

この文書は、参考にしたMERN Blogの画面、機能、実装上の課題と、Next.js版へ引き継ぐ判断を単独で確認できるようにまとめたものです。

## 1. Reference value

参考版は次の完成した体験を持ちます。

- Home、About、Projects、Search、Article detail
- email / Google authentication
- profile update、avatar upload、account delete
- article create / edit / delete
- comment create / edit / delete / Like
- admin overview、posts、users、comments
- responsive layout、dark mode

新規版では、この画面範囲と主要導線をproduct referenceとして使用します。

## 2. Mapping to the final architecture

| Current MERN blog | New Next.js blog |
|---|---|
| React Router pages | App Router pages / nested layouts |
| client `useEffect` fetching | public Server Components / query Use Cases |
| Express route / controller | Server Action or Route Handler → Use Case |
| MongoDB / Mongoose | PostgreSQL / Prisma |
| string relation | foreign key / join table / constraints |
| `isAdmin` boolean | `READER / AUTHOR / ADMIN` + ownership policy |
| Redux Persist user | server session / HttpOnly cookie |
| Redux theme | theme cookie + small client provider |
| local admin state | scoped Redux `editorSlice` / `uiSlice` |
| offset + Show More | deterministic cursor pagination |
| Mongo regex | measured PostgreSQL search |
| Quill HTML | Markdown + controlled renderer |
| client route guard | server layout check + per-Use-Case authorization |
| ad-hoc fetch errors | typed Action result + error boundary |

## 3. Strengths to preserve

- public / authenticated / adminの画面範囲が分かりやすい
- search stateをURLへ載せる考え方
- article detailからcategory / recent articleへの回遊
- comment ownershipとadmin moderationの区別
- upload progressとdestructive confirmation
- dark modeとresponsive layout
- ownerが記事とcommunityを運用できるdashboard

## 4. Implementation concerns to redesign

### Client-only public data

HomeやArticleがmount後fetchのため、public content表示がJavaScriptとnetwork round tripに依存します。新規版ではServer Componentsで初期HTMLを生成します。

### Session duplication

JWT cookieに加えてuser snapshotをRedux Persistへ保持しています。新規版はserver sessionを正本とし、credentialやsession正本をReduxへ保存しません。

### Database integrity

post / comment / user relationがstringで、foreign keyやcomposite uniquenessがDBにより保証されません。PostgreSQL constraintを最終防衛にします。

### Authorization input

client-supplied `userId`を送るmutationがあります。新規版はserver session principalとDB recordだけでownershipを判定します。

### Content rendering

Quill HTMLを直接描画する設計はsanitization policyの維持が必要です。新規版はMarkdownとallowlisted rendererを採用します。

### State and data fetching

server stateを各componentが個別fetchしています。新規版では公開readをserver query、mutationをAction、管理画面の作業状態だけをReduxに分けます。

### Verification

automated test、accessibility report、performance baselineがありません。新規版ではvertical sliceから成果物に含めます。

## 5. Portfolio narrative

```text
Problem
→ Completed MERN product reference
→ Constraints and quality goals
→ Next.js full-stack redesign
→ Server/client state boundaries
→ Relational integrity
→ Measurement and verification
→ Result and trade-offs
```

英語での要約例：

> I used the completed MERN version as a product reference, then rebuilt it as a server-first Next.js application with explicit use-case and data-access boundaries, relational integrity, scoped Redux state, and measurable quality.
