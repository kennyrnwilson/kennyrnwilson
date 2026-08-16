← [Back to profile](../README.md)

# AI setup

How I actually work with AI: the tools, how they are configured, and the mechanisms that make them useful.


## The tools

| Tool | What I use it for | Documented |
| --- | --- | --- |
| [Claude Code](./claude-code/README.md) | The main driver. Reads and writes the repos directly, on a Mac or in a cloud session. | ✅ |
| OpenAI Codex | Second opinion, and a check that my setup is not locked to one vendor. | soon |

## The life management system

I am building out an system that will enable me to manage my life. Every AI tool much have at its fingertips high level information on how this system works in order to 

1) Help build the system 
2) Interact with it

## The system being described

### Source Repositories
Git Hub is the single source of truth for the life management system repositories. All AI tools can be easily permissioned for GitHub and can hence clone and read/write the necessary repositories. 


| Repository | What it holds |
| --- | --- |
| [knowledge-library](https://github.com/kennyrnwilson/knowledge-library) | The Zettelkasten and life-management notes, including the guidance goals. |
| [book-library](https://github.com/kennyrnwilson/book-library) | A folder per book, with notes, summaries, and action items. |
| [wellbeing-app](https://github.com/kennyrnwilson/wellbeing-app) | Apple Health data; the longevity and mind scorecards. |
| [swim-app](https://github.com/kennyrnwilson/swim-app) | Swim sessions and drills. |
| [finances](https://github.com/kennyrnwilson/finances) | Self-hosted Actual Budget. |
| [personal-portal](https://github.com/kennyrnwilson/personal-portal) | The site build, the nginx routing, and the system documentation. |

Most of these are private, so the links resolve only for my own authenticated
sessions. The full map of how they fit together is the
[system architecture hub](https://github.com/kennyrnwilson/personal-portal/blob/main/docs/system-architecture/index.md)
in `personal-portal`, with
[a page per app](https://github.com/kennyrnwilson/personal-portal/blob/main/docs/system-architecture/apps/index.md)
underneath it.

![Six repositories — knowledge-library, book-library, wellbeing-app, swim-app,
finances, personal-portal — around GitHub main as the single source of truth, with
an AI coding assistant cloning, reading, and writing them.](./images/system-map.png)

### State

The canonical system state exists is one of two places

* Directly in GitHub repositories e.g. markdown articles in knowledge-base and book and their summaries in book-library
* In a set of databases running on a Mac Mini whose configuration lives in GitHub repositories such as swim-app, wellbeing-app, finances

![Two columns. On the left, versioned in GitHub with full history: knowledge-library
markdown notes, book-library notes and PDFs, personal-portal docs and site
configuration. On the right, live on the Mac Mini and gitignored: the wellbeing,
swim, and finances databases, whose only durability is a weekly five-tier snapshot
to iCloud.](./images/where-state-lives.png)

How state actually moves — an edit on a phone reaching the site, a health import, a
backup run — is walked through end to end in
[data flows](https://github.com/kennyrnwilson/personal-portal/blob/main/docs/system-architecture/data-flows.md),
and the snapshot rotation is
[the iCloud backup pattern](https://github.com/kennyrnwilson/personal-portal/blob/main/docs/system-architecture/patterns/data-backup-icloud.md).

### Access

Most repository content does not hold state. It holds the mechanisms that expose the
state — to me, and to my tools.

![On the far left my devices — MacBook Air, Mac Mini, iPhone and iPad. They feed an AI
session, drawn as a split brain and circuit head, which fans out into four MCP
servers. Those reach the data: knowledge-library and book-library markdown, and the
wellbeing, swim and finances databases. In front of the data sit the static site
pipeline over the two markdown sources and the wellbeing, swim and finances apps over
their databases. On the far right, nginx, fed by the pipeline and the wellbeing and
swim apps, with its traffic running along the bottom of the picture to a browser and
back to the same devices. The MCP servers reach the markdown and the swim database
directly and the wellbeing app through its API, none of them passing through nginx.
Finances has no MCP server.](./images/how-state-is-exposed.png)

There are three kinds:

- A **static site pipeline** in `personal-portal` compiles markdown from
  `knowledge-library`, `book-library`, and its own `docs/` into HTML.
- A **web application over each database** — `wellbeing-app` and `swim-app`, each
  with its own API, and `finances` running Actual Budget.
- **Four MCP servers**, one each for `knowledge-library`, `book-library`,
  `wellbeing-app`, and `swim-app`. `finances` has none.

The MCP servers do not all attach at the same depth. The two library servers read
markdown off disk. The wellbeing server goes through that app's API, so it inherits
whatever the API already enforces. The swim server is the exception: it opens the
SQLite file directly, with no API in front of it.

nginx fronts the web traffic. The MCP route does not go through it. Everything runs
on the Mac Mini, and Tailscale puts the whole system on a private network across my
own Apple devices.

That private network is also the limit of the MCP door. The servers are reachable
only from machines on my Tailscale network, which rules out cloud sessions — they run
in a container that is not on it. So a cloud session gets the repositories from GitHub
and the operating manual from its plugin, but no live application state. Reading a
scorecard or writing a swim session needs a session on one of my own devices.

A phone counts, but indirectly. The servers are local child processes, not network
services, so nothing connects to them over the network at all. A session on a phone
reaches them by being relayed to the desktop app on the Mac Mini, which starts them
there. The Mini is always in the path.

The detail behind each mechanism:
[MCP server topology](https://github.com/kennyrnwilson/personal-portal/blob/main/docs/system-architecture/mcp-topology.md)
for what each server exposes and how it reaches its data,
[the build pipeline](https://github.com/kennyrnwilson/personal-portal/blob/main/docs/system-architecture/build-pipeline.md)
for how markdown becomes the site, and
[one MCP server per repo](https://github.com/kennyrnwilson/personal-portal/blob/main/docs/system-architecture/patterns/mcp-server-per-repo.md)
for why they are split that way.

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

The routing table, the TLS termination, and every service the Mini runs are in
[the Mac Mini service topology](https://github.com/kennyrnwilson/personal-portal/blob/main/docs/system-architecture/mac-mini-services.md);
the machines themselves are in
[devices](https://github.com/kennyrnwilson/personal-portal/blob/main/docs/system-architecture/devices.md).

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
