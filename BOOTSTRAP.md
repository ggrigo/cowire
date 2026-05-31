# Cowire — one-paste bootstrap (no plugin, no session restart)

Fetch this file and run it top to bottom. It sets up **Cowire** — a filesystem wire between Claude Code (terminal) and Claude Cowork (browser) — without installing the plugin or restarting your session. No prior context needed.

```
# fetch it
https://raw.githubusercontent.com/ggrigo/cowire/main/BOOTSTRAP.md
```

If you're an agent: read this file and execute the steps below directly. If you're a human: paste this file to your agent and say "run the Cowire bootstrap."

---

## What this creates

A `cowire/` workspace with two inbox folders and a routing ledger, so Claude Code and Claude Cowork can hand work to each other by capability — Code does files/git/shell/`gh`, Cowork does the authenticated browser/email/desktop apps.

## Steps — do these now

1. **Choose where the wire lives.** It must be a folder reachable from *both* surfaces — the user's synced/shared directory (a `/Projects` or cloud-synced folder they open in both Code and Cowork), **not** a throwaway working dir. If unsure, ask. Create a `cowire/` directory inside it.
2. **Create the two inboxes:** `cowire/code/` and `cowire/cowork/`. Each is the inbox of whoever *picks work up*, not who drops it.
3. **Write `cowire/cowire-ledger.md`** with the routing policy in the appendix below. Tune it to the user's actual tools.
4. **Write one-line inbox READMEs:** `cowire/code/README.md` = "Code's inbox — Cowork drops errands here; Code sweeps and acts." Same shape for `cowire/cowork/README.md`.
5. **Report what you created, then give the two sweep commands** (next section).

## Finish — make the wire live (do both; be explicit)

> **Cowire is wired up at `<cowire>/`. Two steps switch it on:**
>
> **① In Claude Cowork** — *(if you're in Cowork, create this now)*
> `/schedule every 15 minutes — sweep <cowire>/cowork/ for *.md with "status: open", do each errand, then set status: done and fill closed:`
>
> **② In Claude Code (terminal)** — open this same folder and paste:
> `/loop sweep <cowire>/code/ — for each *.md with "status: open", do the errand, then set status: done and fill closed:`
> Leave that session running; it's Code's half. (A Code `/loop` is session-bound — restart it each session.)
>
> **That's it.** To hand work across, drop a markdown errand in the *other* surface's inbox.

## Errand format

One file per errand in the target inbox. Filename `YYYY-MM-DD-HHMM-slug.md`. Frontmatter:

```
---
from: code | cowork
to: code | cowork
status: open
opened: <timestamp>
closed:
---
```

Body: plain English, written for a reader with **no prior context**. The consumer's sweep acts on it, sets `status: done` (or `dropped` + reason), and writes results back.

---

## Appendix — routing ledger (write this to `cowire/cowire-ledger.md`)

**Code-side** (`cowire/code/`) — filesystem read/write, git (commit/branch/push), shell/build/test runs, `gh` CLI (issues/PRs/releases), direct source edits, no-auth HTTP fetches, long-running headless jobs, anything ending in "commit and push."

**Cowork-side** (`cowire/cowork/`) — authenticated browser sessions (logged-in sites), email, cloud files, native desktop apps, cross-app workflows (drag/drop, screenshots), anything you need to *see* to verify.

**Either** — basic web reads, public docs; stay on the surface you're already on (crossing costs a sweep interval).

**Tiebreaker:** touches code, repos, or a terminal → Code. Touches people, browsers, mailboxes, or running apps → Cowork.

**Needs both:** split into two errands. One surface does its half and drops the result (e.g. a file path) into the other's inbox.
