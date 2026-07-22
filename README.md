# agent-skills

A collection of agent skills for AI coding assistants.

## Skills

### [solidjs-best-practices](./skills/solidjs-best-practices/)

Best practices for writing and reviewing SolidJS code.

**Covers:**
- Fine-grained reactivity mental model (vs React)
- Signals, Memos, Effects — and their escape hatches
- The destructuring trap (most common React→Solid mistake)
- Control flow: `<Show>`, `<For>`, `<Index>`, `<Switch>/<Match>`, `<Dynamic>`
- `createStore` for nested/complex state
- `createResource` + `Suspense`/`ErrorBoundary` for async data
- Context, composable primitives, performance patterns
- Code review checklist
- SolidStart: routing, server functions, actions, API routes

### [working-backwards](./skills/working-backwards/)

Amazon流 Working Backwards（PR/FAQ）を起点に、アイデア→PRFAQ→仕様書→実装計画までをインタビュー形式で進める開発パイプライン。各フェーズはユーザーの承認ゲートで区切る。

**Covers:**
- Phase 1 PRFAQ: 顧客の5つの質問、1ページPR、外部/内部FAQ、進める/検証/やらない の三択判定
- Phase 2 仕様書: スコープ、ユーザーストーリーと受入条件、必要に応じた周辺ドキュメント（OpenAPI、ADR ほか）
- Phase 3 実装計画: トレーサーバレット起点のチケット分解とブロック関係

## Installation

```sh
npx skills add mktbsh/agent-skills
```

Or install a specific skill:

```sh
npx skills add mktbsh/agent-skills/skills/solidjs-best-practices
```

## License

MIT
