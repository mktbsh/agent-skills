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
