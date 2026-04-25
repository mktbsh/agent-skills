---
name: cli-design-guideline
description: Apply the "Command Line Interface Guidelines" (cli-guidelines.org) philosophy and concrete best practices when designing, building, refactoring, or reviewing any CLI tool — including decisions about subcommands, flags/arguments, help text, error messages, exit codes, stdout/stderr usage, colors/TTY detection, interactivity, config files, environment variables, and command naming. Use this skill proactively whenever the user is working on anything command-line-shaped (a new CLI, a new subcommand, argument parsing, `--help` output, a shell script meant for humans, an SDK CLI wrapper, Cobra/Click/clap/Typer/oclif code, etc.), even if they don't explicitly ask for "CLI design guidance." Also use it when reviewing someone else's CLI UX, writing CLI-related specs or RFCs, or porting a GUI/API workflow to the terminal.
---

# CLI Design Guideline Skill

The purpose of this skill is to make any CLI you design or touch feel considered, humane, and consistent with 40+ years of UNIX convention — without being dogmatic about it. It is grounded in [cli-guidelines.org](https://clig.dev/) (Prasad, Firshman, Tashian, Parish).

The full guideline (~900 lines) is bundled at `references/cli-guideline.md`. Treat that as the source of truth; the summary below is a working index so you can be useful immediately without reading the whole thing.

## How to use this skill

1. **Identify which phase the user is in.** Designing a brand-new tool? Adding one subcommand? Fixing error messages? The guideline has distinct sections for each — don't dump everything at once.
2. **Apply the philosophy first, then the concrete rules.** The 8 pillars (below) explain *why*; the cheat sheet and `references/cli-guideline.md` contain concrete rules. When a rule conflicts with the user's situation, fall back to the philosophy and reason from there.
3. **Decide how deep to read before answering.** The rule of thumb: if your answer would benefit from a concrete example or template from the guideline, read the section. When in doubt between tiers, read the reference — it's better to over-read than to miss a relevant nuance.
   - **Quick check** — a single-flag or single-convention question where the cheat sheet has a direct answer (e.g., "should I use `-v` or `--verbose`?", "what should `--help` be short for?"). Answer directly with a one-line rationale; no need to read the reference.
   - **Specific design advice** — any question that needs examples, templates, or multi-sentence rationale (e.g., "design my `--help` output", "review my error messages", "what exit codes should I use?"). Read the relevant section(s) of `references/cli-guideline.md` using the index below. Cite or adapt concrete examples from the guideline — you can rewrite them for the user's context rather than quoting verbatim, but ground your advice in what the guideline actually says. A "review this CLI" request is typically specific-advice for each issue found, but may span multiple sections.
   - **Multi-section design** — designing a tool or subcommand from scratch, where you need to cover flags, help, output, and more. Read sections in this order — **The Basics → Arguments and flags → Help → Output** — then add other sections as needed. This is a *reading* order to build understanding progressively; structure your *response* however best serves the user.
4. **Don't lecture.** Weave the principles into your design suggestions. If the user asks "should I add `--verbose` or `-v`?" don't recite the whole philosophy — just answer, with a one-line rationale.
5. **Push back gently when a proposal violates a principle.** Explain the tradeoff (e.g., "A positional arg here breaks the consistency rule — users expect `-f FILE`. Worth it?") and let the user decide. This is compatible with "don't lecture" — one sentence of rationale is a push-back, not a lecture.

## Philosophy (the 8 pillars)

These are the load-bearing ideas. Internalize them; they resolve most design disputes.

1. **Human-first design** — CLIs are UIs for humans first, pipelines second. Don't carry forward 1970s terseness out of tradition.
2. **Simple parts that work together** — Your tool *will* be piped, scripted, and composed. Design for it: line-based stdout, structured output on request, clean exit codes, well-behaved signals.
3. **Consistency across programs** — Follow existing conventions (`-h/--help`, `-v/--verbose`, `-o/--output`, exit 0 on success, etc.) unless you have a strong reason not to. Consistency is worth more than local cleverness.
4. **Saying (just) enough** — Neither silent nor firehose. Default output should tell the user what happened; `--verbose` reveals detail; `--quiet` for scripts.
5. **Ease of discovery** — Good `--help`, helpful error messages, suggestions for typos, discoverable subcommands. Users should be able to learn the tool *through* the tool.
6. **Conversation as the norm** — Modern CLIs are interactive dialogues (progress, prompts, confirmations, color). Use that, but always give a way to disable it (`--no-input`, `NO_COLOR`, non-TTY auto-detection).
7. **Robustness** — Be responsive (feedback within ~100ms), do the heavy work quickly, handle Ctrl-C cleanly, don't corrupt state on interrupt, be idempotent where possible.
8. **Empathy** — The user is probably tired, on deadline, and has 40 other tabs open. Make the common case frictionless; make recovery from mistakes easy.

Plus: **Chaos** — people will use your tool in ways you didn't imagine. Plan for it.

## Index to the full guideline (`references/cli-guideline.md`)

Read the section that matches what you're working on. Line ranges are pinned to the bundled `references/cli-guideline.md`.

| Working on… | Section (line range) |
|---|---|
| Foundations — argparse lib, stdout/stderr, exit codes | **The Basics** (175–204) |
| `-h` / `--help`, usage text, examples | **Help** (205–376) |
| README, man pages, `--version` | **Documentation** (377–406) |
| What to print, formatting, color, TTY detection, JSON output, progress, pagers | **Output** (407–535) |
| Error messages, suggestions, debug output | **Errors** (536–551) |
| Flag naming, short vs long, `--` separator, positional vs flag | **Arguments and flags** (552–636) |
| Prompts, confirmation, destructive actions, `--no-input` | **Interactivity** (637–646) |
| `git`-style multi-tool design, command hierarchy | **Subcommands** (647–662) |
| Idempotency, crashes, timeouts, retries, locking | **Robustness** (663–706) |
| Keeping the CLI stable across versions, deprecation | **Future-proofing** (707–738) |
| Ctrl-C, SIGTERM, SIGPIPE, SIGINT semantics | **Signals and control characters** (739–754) |
| Config file format, location (XDG), precedence order | **Configuration** (755–802) |
| Env vars, `NO_COLOR`, `DEBUG`, `MYAPP_*` conventions | **Environment variables** (803–849) |
| Choosing a command name, subcommand naming | **Naming** (850–867) |
| Shipping, single binary vs package manager, updates | **Distribution** (868–875) |
| Telemetry (if you must) | **Analytics** (876–897) |

If the user's question spans multiple sections (common for "design a new CLI from scratch"), start with **The Basics**, then **Arguments and flags**, then **Help**, then **Output**.

## Quick-reference cheat sheet

Use these as defaults; deviate only with reason.

- Use a mature argparse library (Cobra/Click/clap/Typer/oclif/…); don't hand-roll.
- Exit `0` success, non-zero failure; map distinct failure modes to distinct codes.
- Primary output → **stdout**. Logs, progress, errors → **stderr**.
- `-h` and `--help` both work, on the root *and* every subcommand.
- Running with no args on a command that needs them → print concise help, exit non-zero.
- Detect TTY: disable color/progress/prompts when not a TTY, or when `NO_COLOR` is set.
- Offer `--json` (or equivalent) for machine-readable output once a human format exists.
- Short flags `-x` for common options, long flags `--extended` for everything. Don't invent weird long-flag punctuation.
- Confirm destructive actions; provide `--force` / `-f` to skip.
- Config precedence: flags > env vars > project config > user config > defaults.
- Name env vars `MYAPP_FOO`. Respect standards: `NO_COLOR`, `DEBUG`, `XDG_CONFIG_HOME`.
- Error messages: what happened, why, what to try next. No stack traces by default (show with `--debug`).
- Keep startup fast; show feedback within ~100ms or use a progress indicator.
- Design for pipes: assume stdout might be read by a program; keep the format stable.

## When the guideline and the user disagree

The guideline's authors are explicit that these are conventions, not laws. If the user has a deliberate reason to break one (e.g., a tool only ever consumed by another machine, an intentionally opinionated `doctor`-style subcommand, a domain with strong existing conventions of its own like `kubectl`), follow the user. Note the tradeoff once, then move on.

## What this skill does *not* cover

- Full-screen TUI apps (ncurses, Textual, Bubble Tea) — the guideline explicitly scopes these out. Use general TUI/UX knowledge there.
- Language-specific idioms inside the implementation (error handling in Go, async in Python, etc.) — apply normal language best practices alongside this skill.
- Shell scripting style per se (shellcheck, quoting rules). Those are adjacent but separate.

