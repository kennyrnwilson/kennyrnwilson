← [Back to profile](../README.md)

# AI setup

How I actually work with AI: the tools, how they are configured, and the
mechanisms that make them useful across a whole system rather than one chat at a
time.

This is **reference** documentation — what the setup is today. For how it was
built, and the wrong turns along the way, see [articles](../articles/README.md).

## The tools

| Tool | What I use it for | Documented |
| --- | --- | --- |
| [Claude Code](./claude-code/README.md) | The main driver. Reads and writes the repos directly, on a Mac or in a cloud session. | ✅ |
| OpenAI Codex | Second opinion, and a check that my setup is not locked to one vendor. | soon |

## The principle underneath

Everything here follows from one idea: **an AI session should start knowing how my
system is put together.**

Not because the model needs teaching every time, but because the alternative is me
teaching it — the same preamble, ten times a day. Where the docs live, which repo
owns what, what `main` means, which two operating systems run through everything.

That turns into three rules.

- **One copy, or none.** Two copies of a fact means one is wrong and you cannot
  tell which.
- **Load it automatically.** Anything that needs pasting gets pasted on good days
  only.
- **Nothing in the startup path without a time limit.** Learned the hard way —
  see [Giving Claude a Memory](../articles/giving-claude-a-memory.md).

## The system being described

![Six repositories — knowledge-library, book-library, wellbeing-app, swim-app,
finances, personal-portal — around GitHub main as the single source of truth, with
an AI coding assistant cloning, reading, and writing them.](./images/system-map.png)

The repositories hold markdown and application data. GitHub `main` is canonical, and
every machine fast-forwards from it. The AI coding assistant works on the
repositories directly — it clones them, reads them, and commits back.

## The host

One always-on Mac Mini runs the rest of it, and it has two doors onto the same data.

![One Mac Mini with two independent routes to the same data. A browser goes through
nginx, which routes by path to the static portal site and to the wellbeing and swim
apps. An AI session bypasses nginx entirely and reaches the databases and
repositories through MCP servers.](./images/the-host.png)

**The browser door** is nginx. One entry point that routes by path: the portal — the
static site built from the notes and books — plus the wellbeing and swim apps, each
a web front end over its own API.

**The AI door** is the MCP servers. They skip the web layer completely and reach the
databases and repositories directly, which is why an AI session on a phone can read
and write live state rather than just read pages.

Both doors end at the same data. That is the point of the arrangement.

## What every tool is told

The standing context is the life management system itself — the thing all the repos
and apps exist to serve. Any AI tool that does not know this shape gives generic
advice about a system it cannot see.

![A five-stage chain: books, action items, guidance goals, scorecards, daily
protocols, with a feedback loop from protocols back to scorecards. Two bands,
Longevity OS and Mind OS, run beneath the whole chain.](./images/life-management-system.png)

It is an AI-first personal system that turns knowledge into action. Books are read
into a folder each, with notes and **action items**. Those items feed four **guidance
goals** — maximise healthspan, master your mind, sharpen focus, lead people. The apps
turn live data into **scorecards** against those goals, and the scorecards drive
**daily protocols**. Two operating systems, **Longevity OS** and **Mind OS**, run
through every stage.

Alongside the chain, a tool needs the ground rules: GitHub `main` is canonical, docs
live in each repo's `docs/`, link rather than duplicate, and prose is written in
Simplified Technical English.

That is roughly fifty lines of markdown — a short public index called the operating
manual. How those fifty lines reach every session without being pasted is the
`operating-context` plugin, described in
[shared context](./claude-code/shared-context.md).
