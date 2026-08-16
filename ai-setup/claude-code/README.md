← [Back to AI setup](../README.md)

# Claude Code

The main driver of my setup. Claude Code works on the repositories directly — it
clones, reads, and writes them — rather than being handed pasted snippets of them.

## Where it runs

| Surface | What I use it for |
| --- | --- |
| Terminal CLI | Most work. Full access to the repositories on a Mac. |
| Cloud sessions | Work away from a Mac, and anything long-running. |
| Desktop app | Same setup as the CLI; shares one configuration directory. |
| Browser chat | Reading and asking, rather than building. |

The first three share one configuration directory, so a plugin installed once is
available to all of them. That single fact is what makes
[shared context](./shared-context.md) practical.

## How it is configured

![Four configuration layers ordered by what they cost at session start: settings
and plugins are always loaded; skills cost one line each until used; MCP servers
cost nothing until called.](../images/config-layers.png)

Four layers, and the important thing about each is **what it costs before anyone
needs it**.

**Settings** name which plugins are enabled and what commands may run without asking.

**Plugins** are installed from marketplace repositories on GitHub. Mine come from a
[public marketplace](https://github.com/kennyrnwilson/kennys-ai-integrations) I
maintain, plus Anthropic's official one. A plugin can carry hooks, skills, agents,
and commands.

**Hooks** run on session events. A `SessionStart` hook is how the operating manual
loads itself — see [shared context](./shared-context.md).

**Skills** are instructions for a particular kind of task, loaded only when relevant.
They cost one line each — a name and a description — until something needs them. That
makes them cheap to have many of.

**MCP servers** front live application data. They cost nothing until called, provided
their schemas are deferred behind a tool search. Mine run on an always-on Mac Mini and
expose the wellbeing, swim, knowledge, and book libraries, so a session on a phone can
read and write real state.

## My own plugins

Published in [kennys-ai-integrations](https://github.com/kennyrnwilson/kennys-ai-integrations):

| Plugin | What it does |
| --- | --- |
| `operating-context` | Loads the operating manual into every session. |
| `documentation-conventions` | Markdown and Mermaid conventions across my repositories. |
| `dev-conventions` | Coding and project-scaffolding conventions. |
| `image-gen` | Generates images and infographics for documents. |

## The trust boundary worth knowing about

Committing plugin configuration into a repository does **not** install the plugin for
anyone who clones it. That looks like an oversight until you see what it prevents: a
plugin can ship a hook that runs arbitrary commands, so if a repository's committed
configuration could auto-install one, every repository you cloned would be a
code-execution vector.

An externally-sourced plugin therefore does not install until a human installs it. I
learned this by pushing configuration to six repositories and watching it do nothing.

The sanctioned non-interactive path is the environment setup script, which is exactly
how the cloud surface is covered in [shared context](./shared-context.md).

## Further reading

- [Shared context in every session](./shared-context.md) — the operating manual and
  how it reaches each surface.
- [Articles](../../articles/README.md) — the same work as a narrative, and as a
  step-by-step guide.
