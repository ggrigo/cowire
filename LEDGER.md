# Routing ledger — Code ↔ Cowork

Which kind of work belongs in which inbox. Drop an errand by this; if something looks misrouted when you pick it up, bounce it back the other way.

This is about **workflow fit, not raw capability**. Both surfaces can technically do many of the same things — the ledger says where each kind of work *lands cleanly*, given how each surface is actually wired and authenticated. Tune it to your own setup.

## Code-side (drop in `code/`)

Use Code when the errand needs:

| Capability | Why Code |
|---|---|
| Filesystem read/write outside app sandboxes | Direct shell access; no MCP shim in the way. |
| Git operations (commit, branch, push, log, diff) | `git` and `gh` are native to the terminal. |
| Shell commands, build runners, test harnesses | Real shell, background jobs, streamed output. |
| `gh` CLI for GitHub (issues, PRs, releases, gists) | Authenticated via `gh auth`; richer than clicking through the web UI. |
| Direct edits to source files | Line-addressable edit tools. |
| One-shot HTTP fetches with no auth | Cheap and direct. |
| Long-running headless jobs (CI watches, log tails) | Background process + monitoring. |
| Anything whose next step is "commit and push" | The commit happens here anyway — don't add a hop. |

## Cowork-side (drop in `cowork/`)

Use Cowork when the errand needs:

| Capability | Why Cowork |
|---|---|
| Browser automation with DOM access | The browser extension is loaded and authenticated to your sessions. |
| Authenticated browser sessions (web mail, GitHub web, any logged-in site) | Your cookies live in your browser, not in a terminal. |
| Email (search, label, draft, send) | Wired to your mail account's OAuth. |
| Drive / cloud files | Same — the OAuth lives on this side. |
| Driving native desktop apps | Computer-use; the terminal can't touch native apps. |
| Cross-app workflows (drag/drop, screenshots, copy-paste between apps) | Needs computer-use. |
| Anything you need to *see* to verify (rendering, layout) | This is the surface with a screen in front of you. |

## Either — let context decide

| Capability | Heuristic |
|---|---|
| Web fetch / search | Either can do basic reads. Prefer the surface that already holds the context. |
| Reading public docs, PDFs, feeds | Wherever you already are. |
| Light scheduling | Pick by where the *output* will be used. |

**Default when ambiguous:** stay on the surface you're already on. Crossing costs a sweep interval, not zero.

**Tiebreaker when you must cross:** touches code, repos, or a terminal → Code. Touches people, browsers, mailboxes, or running apps → Cowork.

## Edge cases

- **Needs both** (e.g. "render the page, screenshot it, commit the screenshot"): split into two errands. Cowork renders + screenshots and drops the file path into `code/`; Code commits and pushes. The handoff is the path in the closed errand.
- **Could be either, unsure which is cleaner:** drop it where you currently are. If the receiving surface decides it's misrouted, it bounces — writes a new errand into the other inbox with a one-line "redirected: <reason>".

## How the ledger grows

Append-only for additions; strike-through-and-replace for corrections (keep the strike visible). When a new tool or capability comes online: try it from the surface where it first appears; if it works only there, say so explicitly — that's the strongest kind of routing rule. The ledger is meant to be rewritten when a real errand exposes a wrong rule.
