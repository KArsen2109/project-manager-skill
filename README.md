# project-manager

A [Claude](https://claude.com) skill that manages multi-stage projects as
a state machine on top of a local folder of plain Markdown files. No
particular note-taking app is required — an ordinary folder on your
computer is the primary target. Obsidian-style wikilinks, a
Dataview-style dashboard, and a read-only reference source such as
Notion are all supported as **optional add-ons** you can opt into, never
assumed. A **built-in multi-perspective council procedure** handles the
founding discussion for larger projects — no separate skill to install.

It is meant for work that spans many sessions — research projects, data
analysis, software builds, long-form writing, translation/editing —
where you want progress, decisions, and deliverables tracked in durable
files instead of re-explained to Claude every time you open a chat.

## Why this exists

Long-running work usually breaks one of Claude's sessions apart from the
next: context resets, decisions get re-litigated, and it's easy to lose
track of what was actually finished versus just discussed. This skill
fixes that by keeping the *project itself* — not the chat history — as
the source of truth. Every session, Claude reads a small state file,
knows exactly where the project stands, and picks up from there. Nothing
is marked done without the file that proves it's done.

## Features

- **Four-phase state machine** — founding → planning → execution →
  closing. Claude cannot skip ahead, cannot silently switch phases, and
  cannot close a project on its own.
- **Deliverable-backed completion.** A stage is only "done" if the file
  it was supposed to produce actually exists and holds real content. No
  conditional or "trust me" completions.
- **Built-in council procedure** for founding discussions on complex
  projects: a panel of named, isolated perspectives (Critic, Outside
  Observer, Methodologist, Implementer, Risk & Ethics, subject-matter
  specialists) argue the question independently, get cross-examined by a
  chair, and are checked by a separate verifier — all without needing a
  second skill installed.
- **Three scope levels** so you're never over- or under-managed:
  - **micro** — a one-off task; the skill declines to create project
    overhead at all.
  - **standard** — a single-track project with a short founding
    document and a unified plan.
  - **full** — multi-domain or methodologically uncertain work, with
    the full council procedure, review points, and a lessons-learned
    file.
- **Plain-text, queryable state.** Every project file uses YAML
  frontmatter, so it's readable in any editor, diffable in git, and
  optionally queryable with a tool like Obsidian's Dataview plugin.
- **Never touches your other files.** The skill only writes to the
  files it owns for a given project (state, founding document, plan,
  changelog, lessons, summary, council log, deliverables) and, only if
  you opt in, a shared terminology file and a projects dashboard.
- **Language-consistency rules built in**, including a shared
  terminology list so translated or transliterated terms stay the same
  across every project in a workspace.

## Benefits

- **Nothing gets lost between sessions.** Come back after a week and
  Claude immediately knows what stage you're on, what's next, and what's
  blocked — no re-explaining.
- **No inflated status reports.** Because completion is tied to an
  actual deliverable file, you never get an optimistic "done!" for work
  that wasn't really finished.
- **Decisions are made once, not re-argued.** The founding document is
  immutable after approval, so scope and success criteria can't quietly
  drift over the course of a long project.
- **A second opinion without a second tool.** The built-in council
  procedure gives you structured, multi-angle scrutiny on high-stakes
  decisions (which model to use, which methodology to pick, whether a
  plan is sound) without installing or configuring anything extra.
- **Portable and inspectable.** Everything is plain Markdown — no
  database, no proprietary format, no vendor lock-in. You can read,
  edit, or move any project file yourself at any time.

## What you'll need

- A local folder to act as the project workspace, and file-system
  access from the session running Claude (Claude Code, the Claude Agent
  SDK, or an equivalent local setup). This skill does not work in a
  browser-only environment with no local files — that's the entire
  requirement, a plain folder of `.md` files is enough.
- *Optional:* a wikilink-based note tool (e.g. Obsidian) if you'd
  rather cross-reference project files with `[[links]]` than plain
  filenames.
- *Optional:* a query/dashboard plugin (e.g. Dataview) for the live
  projects dashboard described in the skill. Without it, that piece is
  simply skipped.
- *Optional:* a connected read-only reference tool (e.g. Notion, a
  reference manager) if you want Claude to pull methodological
  background from somewhere other than plain files. It is never written
  to, only read.
- *Optional, for the best council-procedure results:* a Claude setup
  that can spawn isolated sub-agents, ideally on more than one
  underlying model. Without that, the skill falls back to a plain
  sequential discussion from several angles in the same session and
  says so — it never fails silently.
- *Optional:* a literature-review skill if you do scientific reviews —
  the council procedure's steps then slot into it rather than
  duplicating it.

## Installation

1. Download or clone this repository.
2. Copy the `skills/project-manager/` folder into wherever your Claude
   setup loads skills from. For Claude Code / the Claude Agent SDK, that
   is typically:
   - `~/.claude/skills/project-manager/` (available in every project), or
   - `.claude/skills/project-manager/` inside a specific project
     (available only there).
3. Restart your Claude session so the new skill is picked up.

No dependencies to install, no build step — it's a single Markdown
instructions file the agent reads.

## Usage

Just talk to Claude about your project in plain language:

- *"Let's start a new project for cleaning up this survey dataset."*
- *"Let's continue the thesis-outline project."*
- *"Close out the migration project — we're done."*

Under the hood, the skill:

1. **On first use in a workspace** — asks for the folder path and your
   working language, and remembers both going forward.
2. **Assesses scope** — decides whether this is `micro` (just do it),
   `standard`, or `full`, and gets your sign-off before creating
   anything.
3. **Runs a founding discussion** — a direct conversation at `standard`
   scope, or the built-in council procedure at `full` scope — and writes
   an immutable founding document once you approve it.
4. **Plans the work** into stages, each with a named, checkable
   deliverable and an explicit review point.
5. **Executes one approved task at a time**, logging real output into
   the right deliverable file and updating the project's state after
   every step. Nothing runs without your go-ahead.
6. **Closes only when you say so**, producing a final summary and
   generalized lessons-learned — never on its own initiative, even if
   every stage looks finished.

See [`examples/`](./examples) for what the generated files actually
look like for a sample project.

### What gets created

Inside your project's folder, the skill maintains:

| File | Purpose |
| --- | --- |
| `<project>-state.md` | current phase, stage, and next step (read every session) |
| `<project>-founding.md` | the immutable record of what was decided and why |
| `<project>-plan.md` | the content, format, and work plan, stage by stage |
| `<project>-log.md` | a changelog of what was completed at each stage |
| `<project>-lessons.md` | generalized takeaways (full scope only) |
| `<project>-summary.md` | the final write-up, produced at closing |
| `<project>-council.md` | the founding discussion's record (full scope only) |
| deliverable files | whatever your project actually produces — a dataset, a report, a draft, measurements, etc. |

## Design notes

- **Everything is a file, not a database.** State lives in frontmatter
  fields (`phase`, `stage`, `stage_total`, `blocker`, …) so it survives
  session restarts, is human-readable, and is greppable/queryable.
- **A stage's "done" mark and its deliverable are the same claim.** The
  skill will not mark a stage done on request alone if the deliverable
  file is empty or missing — see invariant 8 in `SKILL.md`.
- **Layered reading.** To stay cheap and avoid stale context, the skill
  reads only the current stage's slice of the plan during execution, and
  only reads the full history (changelog, discussion notes) at closing
  time or when you ask about a specific past detail.
- **No silent phase-hopping.** If you say "let's plan" while the project
  state says `execution`, the skill flags the mismatch and asks, rather
  than guessing what you meant.

## Full documentation

The complete, authoritative behavior spec lives in
[`skills/project-manager/SKILL.md`](./skills/project-manager/SKILL.md) —
it's written as the actual instructions Claude follows, so it doubles as
a very literal reference for every rule summarized above, including the
full council-procedure walkthrough.

## License

MIT — see [`LICENSE`](./LICENSE).

## Contributing

Issues and pull requests welcome. If you adapt this for a note-taking
tool other than Obsidian, or add a language-agnostic variant, a PR
documenting that adaptation is especially useful.
