# Operating manual

The public index to how I run my life and projects — structure and pointers only.
The depth lives in a private hub this points into.

## What this is

An AI-first personal system that turns knowledge into action across six repos,
with GitHub `main` as the single source of truth. The repos are four kinds:
markdown knowledge-base articles, book libraries, database-backed apps, and the
portal.

Claude reaches the system two ways. Claude Code — on a Mac or in a cloud session
— clones and reads/writes the repos directly. For live app state, MCP servers on
the always-on Mac Mini front the app databases, so Claude on a phone can read and
write the apps too.

The through-line is two operating systems — Longevity OS and Mind OS — carrying
book action items -> ranked guidance -> live scorecards -> daily protocols across
those repos. Full architecture is in the personal-portal hub, under
`docs/system-architecture`: https://github.com/kennyrnwilson/personal-portal

## The repos

- [knowledge-library](https://github.com/kennyrnwilson/knowledge-library) — the
  Zettelkasten and life-management system; holds the guidance goals
  (maximise-healthspan, master-your-mind, sharpen-focus, lead-people).
- [book-library](https://github.com/kennyrnwilson/book-library) — a folder per book,
  with notes and action items.
- [wellbeing-app](https://github.com/kennyrnwilson/wellbeing-app) — Apple Health data;
  longevity and mind scorecards.
- [swim-app](https://github.com/kennyrnwilson/swim-app) — swim sessions and drills.
- [finances](https://github.com/kennyrnwilson/finances) — self-hosted Actual Budget.
- [personal-portal](https://github.com/kennyrnwilson/personal-portal) — the Astro
  build, the nginx router, and the system docs (the hub).

## The host

The always-on Mac Mini serves the site, runs the MCP servers, and bridges mobile.
GitHub `main` is canonical; every machine fast-forwards from it automatically.

## Conventions

- Link, don't duplicate — system-wide facts live in the hub; repos point at it.
- Documentation lives in each repo's `docs/`; markdown per the
  documentation-conventions skill.
- Writing: ASD-STE100 Simplified Technical English for technical prose.
  Python-first for new tooling.

## A note on access

Most of these repos are private; the links resolve only for my own authenticated
sessions. This page is the public top sheet — secrets, private paths, and machine
endpoints are deliberately not here.
