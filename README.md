# Cowire

**Cowire = Cowork + wire** — a thin wire between Claude's two work surfaces.

Claude Code runs in your terminal: it touches files, git, the shell. Claude Cowork runs in your browser: it drives your logged-in tabs, your email, your desktop apps. Neither can do the other's job, and they don't share memory. So when a task needs both — "check the repo, then post the result to a site I'm logged into" — *you* become the messenger, carrying context across by hand and retyping it.

Cowire is that wire. Two inbox folders, one per surface, plus a one-page rule for which surface handles what. Each surface drops an **errand** in the other's inbox; the other picks it up, does the part it's good at, and writes the result back. Work crosses the gap without you in the middle.

It's deliberately small: two folders and one markdown file per errand. It's also deliberately honest — this is duct tape. The day Anthropic ships real cross-surface coordination, Cowire retires. Until then it works today, on nothing but a shared folder.

## The problem

You're running both surfaces. A job comes up that crosses them:

- Code can read your repo and run `git`, but it can't click a button in a site you're logged into.
- Cowork can drive your authenticated browser and inbox, but it can't run your test suite or push a commit.

Today the bridge between them is *you* — you read Code's output, switch to Cowork, retype the context, get a result, switch back, paste it in. The two surfaces never talk; you are the integration.

## How it works

- **Two inboxes** — `code/` and `cowork/`. Each folder is the inbox of whichever surface *picks work up*, not who drops it.
- **One errand = one file** — `YYYY-MM-DD-HHMM-slug.md` with a little frontmatter (`from`, `to`, `status`, `opened`, `closed`) and a plain-English body written for a reader with no prior context.
- **A routing ledger** ([`LEDGER.md`](LEDGER.md)) — says which kind of work lands cleanly on which surface. Short version: files / git / shell / `gh` → Code; browser / email / desktop apps → Cowork.
- **Each surface sweeps its own inbox** on a schedule, acts on open errands, and marks them `done` (or `dropped`, with a reason). Misrouted? It *bounces* — writes a new errand back the other way.

A worked example is in [`examples/`](examples/).

## Install

```
/plugin marketplace add ggrigo/cowire
/plugin install cowire@cowire
```

Then run `/cowire` — it sets up the two inboxes, drops in the ledger, and wires the sweeps for you.

**Counted alternative:** `npx skills add ggrigo/cowire` installs via [skills.sh](https://www.skills.sh) (which lets us see real install numbers). The native `/plugin` path above is fully supported; this one just routes through a channel that counts installs.

## Or set it up by hand

1. Make the two folders: `mkdir -p code cowork`.
2. Copy [`LEDGER.md`](LEDGER.md) in and tune it to your own tools.
3. On the **Code** side, run a `/loop` that sweeps `code/` and acts on open errands.
4. On the **Cowork** side, set a `/schedule` task that sweeps `cowork/` (it runs locally in that folder, so it can read and write the files).
5. To hand work across, drop a file in the *other* surface's folder. That's the whole protocol.

## What Cowire is not

- **Not real-time.** Sweeps run on a schedule (minutes, not seconds). Urgent work doesn't belong here.
- **Not a chat.** Errands are discrete units of work, not a back-and-forth. If a reply is needed, the receiver opens a new errand the other way.
- **Not a managed service.** Whether you install the plugin or copy the folders by hand, it's a convention over a shared filesystem — nothing runs in the cloud.

## Built to be thrown away

Cowire exists because nothing native does this yet. Anthropic's Claude Code "Channels" pushes outside events *into* a Code session, but it doesn't route work to Cowork. The only thing connecting the two surfaces today is a shared filesystem — which is exactly what Cowire sits on.

There's an open request asking Anthropic to build this natively ([claude-code#25791](https://github.com/anthropics/claude-code/issues/25791)). If they ship it, Cowire is done — and that's a good day. The value was never the folders; it was noticing the gap and patching it before the platform got there. Disposable on purpose.

## License

MIT.
