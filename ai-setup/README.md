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

A short public index to all of this — the operating manual — is the subject of
[shared context](./claude-code/shared-context.md).
