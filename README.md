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

Amazon流 Working Backwards（PR/FAQ）プロセス。プロダクト・機能のアイデアを、実装前に架空のプレスリリースとFAQで顧客起点から検証する。

**Covers:**
- 顧客の5つの質問（事実/仮説のラベリング）
- 1ページのプレスリリース構成と規範
- 外部/内部FAQ（最も答えにくい質問から書く）
- 進める / 検証してから / やらない の三択判定

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
