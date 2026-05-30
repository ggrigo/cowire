---
name: cowire
description: Set up and run Cowire — a filesystem wire between Claude Code and Claude Cowork. Invoking it scaffolds the workspace — it creates two inbox folders (cowire/code, cowire/cowork) and a routing ledger so the two Claude surfaces hand work to each other by capability, where Code does files/git/shell/gh and Cowork does the authenticated browser/email/desktop apps. Invoke when the user wants Code and Cowork to coordinate, to set up cross-surface task handoff, or to drop or sweep an errand between the two surfaces.
---

# Cowire — Code ↔ Cowork errand routing

Claude Code (terminal) and Claude Cowork (browser) don't share state and can't do each other's jobs. Cowire wires them together with two inbox folders and a routing rule, so work crosses the gap without the user retyping context. It is a convention over a shared filesystem — no service, no real-time channel.

## When invoked: scaffold the workspace

First check whether a `cowire/` directory already exists in the project.

**If it does not exist, set it up now** (this is the "install" — actually create the files; don't just describe them):

1. **Choose where the wire lives — this matters most.** It must be a folder reachable from *both* surfaces: the user's synced or shared directory (e.g. a `/Projects` or cloud-synced folder they open in both Claude Code and Cowork), **not** a throwaway working directory. If you can detect their synced folder, use it; otherwise ask. Create a `cowire/` directory inside it.
2. Create the two inbox folders:
   - `cowire/code/` — Claude Code's inbox (Cowork drops errands here)
   - `cowire/cowork/` — Claude Cowork's inbox (Code drops errands here)
3. Write `cowire/cowire-ledger.md` using the **Routing ledger** content at the bottom of this skill. Tune it to the user's actual tools (their real MCPs, CLIs, and authenticated apps) rather than the generic list — ask what they use if it isn't obvious.
4. Write a one-line `cowire/code/README.md` ("Code's inbox — Cowork drops errands here; Code sweeps and acts") and the same for `cowire/cowork/README.md`.
5. Then **finish the setup** (next section). Do not leave the user guessing — give exact, copy-paste steps for *both* surfaces.

**If `cowire/` already exists**, don't re-scaffold — the user is invoking the skill to drop or sweep an errand. Proceed to the relevant section below.

## Finish the setup — make the wire live (be prescriptive, not vague)

After scaffolding, the wire exists but isn't running yet. Two sweeps switch it on — one per surface. **Tell the user clearly and explicitly what to do on each. Name the surface you're on, do the half you can, and hand them the exact command for the other half.** Replace `<cowire>` below with the real path you created (e.g. `/Projects/cowire`).

End your message with a block shaped exactly like this:

> **Cowire is wired up at `<cowire>/`. Two steps switch it on — do both:**
>
> **① In Claude Cowork** — *(if you are running in Cowork, create this now and confirm; otherwise tell the user to run it in Cowork)*
> `/schedule every 15 minutes — sweep <cowire>/cowork/ for *.md with "status: open", do each errand, then set status: done and fill closed:`
> This is Cowork's half. It persists across sessions.
>
> **② In Claude Code (your terminal)** — open this same folder and paste:
> `/loop sweep <cowire>/code/ — for each *.md with "status: open", do the errand, then set status: done and fill closed:`
> Leave that session running; it's Code's half. *(A Code `/loop` is session-bound — restart it each session you want the wire live.)*
>
> **That's it.** To hand work across, drop a markdown errand in the *other* surface's inbox; its sweep picks it up.

Do whichever half belongs to the surface you're currently on (on Cowork, offer to create the `/schedule` task and do it; on Code, you can't start the user's interactive `/loop` for them, so give them the command). Always give the command for the other surface verbatim.

## Dropping an errand

To hand work to the other surface, write one file into *its* inbox (`cowire/code/` or `cowire/cowork/`):

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

## Lifecycle

1. Producer writes a file into the consumer's inbox with `status: open`.
2. The consumer's sweep finds it and either **acts** (`status: done`, fill `closed:`, paste any result links into the body) or **drops** it (`status: dropped` + a one-line reason).
3. **Misrouted?** Bounce it — leave the original open, write a new errand into the *other* inbox explaining what to do instead.
4. Closed entries stay as the record.

## What Cowire is not

- Not real-time (sweeps are minutes, not seconds) — don't use it for urgent work.
- Not a chat — errands are discrete; a reply is a new errand the other way.
- Not a managed service — a pattern over a shared folder. The day Anthropic ships native Code↔Cowork coordination ([claude-code#25791](https://github.com/anthropics/claude-code/issues/25791)), Cowire retires.

---

## Routing ledger

*(Write this to `cowire/cowire-ledger.md` during scaffolding. It decides which inbox an errand belongs in. Tune it to the user's actual tools.)*

**Code-side** (drop in `cowire/code/`) — filesystem read/write, git (commit/branch/push), shell/build/test runs, `gh` CLI (issues/PRs/releases), direct source edits, no-auth HTTP fetches, long-running headless jobs, anything ending in "commit and push."

**Cowork-side** (drop in `cowire/cowork/`) — authenticated browser sessions (logged-in sites), email, cloud files, native desktop apps, cross-app workflows (drag/drop, screenshots), anything you need to *see* to verify.

**Either** — basic web reads, public docs; stay on the surface you're already on (crossing costs a sweep interval).

**Tiebreaker:** touches code, repos, or a terminal → Code. Touches people, browsers, mailboxes, or running apps → Cowork.

**Edge case — needs both:** split into two errands. One surface does its half and drops the result (e.g. a file path) into the other's inbox.
