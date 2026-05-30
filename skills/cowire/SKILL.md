---
name: cowire
description: Set up and run Cowire — a filesystem wire between Claude Code and Claude Cowork. Creates two inbox folders (code/, cowork/) plus a routing ledger so the two Claude surfaces hand work to each other by capability: Code does files/git/shell/gh, Cowork does the authenticated browser/email/desktop apps. Invoke when the user wants Code and Cowork to coordinate, to set up cross-surface task handoff, or to drop or sweep an errand between the two surfaces.
---

# Cowire — Code ↔ Cowork errand routing

Claude Code (terminal) and Claude Cowork (browser) don't share state and can't do each other's jobs. Cowire wires them together with two inbox folders and a routing rule, so work crosses the gap without the user retyping context. It's a convention over a shared filesystem — no service, no real-time channel.

## Setup (first run)

1. Pick the shared folder both surfaces can reach (a synced/shared directory).
2. Create the two inboxes: `code/` and `cowork/`. Each is the inbox of whoever *picks work up*, not who drops it.
3. Drop in the routing ledger (`LEDGER.md` from this repo) and tune it to the user's tools.
4. Wire the sweeps:
   - **Code side:** a `/loop` that lists `code/`, acts on `status: open` errands, marks them `done`.
   - **Cowork side:** a `/schedule` task that sweeps `cowork/` (it runs locally in that folder, so it can read and write the files).

## Dropping an errand

To hand work to the other surface, write one file into *its* inbox:

- Filename: `YYYY-MM-DD-HHMM-slug.md`
- Frontmatter:
  ```
  ---
  from: code | cowork
  to: code | cowork
  status: open
  opened: <timestamp>
  closed:
  ---
  ```
- Body: plain English, written for a reader with **no prior context** — include everything the receiving surface needs to act without seeing the conversation that produced the request.

## Which surface gets what

Consult `LEDGER.md`. Short version:

- **Code** — filesystem, git, shell, `gh`, source edits, build/test runs, anything ending in "commit and push."
- **Cowork** — authenticated browser sessions, email, cloud files, native desktop apps, anything you need to *see* to verify.
- **Ambiguous** — stay on the surface you're already on; crossing costs a sweep interval.

## Lifecycle

1. Producer writes a file into the consumer's inbox with `status: open`.
2. The consumer's sweep finds it and either **acts** (`status: done`, fill `closed:`, paste any result links into the body) or **drops** it (`status: dropped` + a one-line reason).
3. **Misrouted?** Bounce it — leave the original open, write a new errand into the *other* inbox explaining what to do instead.
4. Closed entries stay as the record.

## What Cowire is not

- Not real-time (sweeps are minutes, not seconds) — don't use it for urgent work.
- Not a chat — errands are discrete; a reply is a new errand the other way.
- Not a managed service — it's a pattern over a shared folder. The day Anthropic ships native Code↔Cowork coordination ([claude-code#25791](https://github.com/anthropics/claude-code/issues/25791)), Cowire retires.
