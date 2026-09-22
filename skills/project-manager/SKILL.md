---
name: project-manager
description: Manage multi-stage projects as a state machine on top of a local folder of plain Markdown files, with a built-in multi-perspective council procedure for founding discussions. Use when the user wants to start, plan, continue, or close a structured project (research, data analysis, software tool, writing, translation/editing) and wants progress tracked in durable files rather than re-explained every session.
---

# Project Manager

A four-phase state machine, operating on the files of a local project
folder on the user's computer. Logging is not a phase; it is an
automatic action inside execution.

By default this works against plain Markdown files in an ordinary
folder — no particular app required. Obsidian-style linking and a
Notion-based reference source are both **optional add-ons** (§2.4, §9,
§10), offered to the user, never assumed. The full-scope founding
discussion (§4.1) uses a built-in multi-perspective council procedure
(§11) — no separate skill to install.

## 0. Invariants

These hold across all phases, without exception.

### Structural

1. The founding document is never edited in later sessions.
2. No task is executed without the user's explicit approval.
3. The project never declares itself complete on its own, even if every
   stage is marked "✅ Done".
4. Layer 2 is never read without one of the reasons listed in §3.
5. Any material change to the plan is logged in the changelog, with a reason.
6. A recurring blocker becomes a decision to be made, not a repeated log entry.
7. Fabricating data is absolutely forbidden, bibliographic data first of
   all. An unverified item is marked as unverified.
8. **A stage is done only together with its deliverable.** If a stage's
   tasks are unfinished, or their output (a measurement, a document, an
   artifact) doesn't exist, the stage is **not closed** and does not get
   "✅ Done" — no matter who asks. No stage is closed "conditionally" or
   "procedurally". An unfinished stage stays open, and its incomplete
   steps are logged under `blocker`.

### Storage-related

9. Writes happen **only** inside the project's own folder (§2). Never
   write into an app-internal folder (e.g. `.obsidian/`) or any generic
   "outputs" folder.
10. The user's existing files are never edited. The skill only touches
    its own files: the project's six files (§2.2), the project's
    deliverable files (§2.5), and, only if the user opted into them, the
    shared terminology file (§2.4) and the projects dashboard (§10). The
    restriction applies to the user's other files, not to the project's
    own deliverable files.
11. Before overwriting the state file, check the `updated` field. If it
    differs from what this session last wrote, the file was edited
    externally (or synced by a cloud drive). **Stop and report**, don't
    overwrite.

### Language

12. All work output is in the language the user works in (set once per
    workspace; do not assume).
13. No mixing of languages within a running text. A foreign-language word
    is acceptable only when it is the sole possible way to express the
    idea.
14. Never translate: proper names, citation keys, algorithm names, file
    and folder names, field identifiers (`success_criteria`,
    `project_type`).
15. A query required by a tool in a different language (e.g. an English
    search query) is an exception and does not count as language mixing.
16. When no solid equivalent exists in the working language, transliterate
    the original according to that language's phonetics rather than
    inventing a new translation. Any translation used must be a term that
    is actually in real use.
17. Foreign-language ↔ working-language equivalences are recorded in the
    shared terminology file, if the user opted into one (§2.4). The same
    term, the same way, across all projects.

### Communication

18. Approval points carry no special name. The proposal is presented,
    then explicit approval is awaited. Do not use vague stand-in words
    for this ("blocker" is reserved for §0 invariant 6 usage). "Review
    point" is reserved for the stage-end check (§5.2).

## 1. Entry and routing

On invocation, locate the project's state file and read **only its
frontmatter** (Layer 0), then route by the `phase` field:

| State | Route to |
| --- | --- |
| no project exists | §1.1 Scope assessment |
| `discovery` | §4 Phase A |
| `planning` | §5 Phase B |
| `execution` | §6 Phase C (completeness check first) |
| `closed` | Read-only. Report the state. Reopening only on explicit instruction |

To find the state file, search the project workspace for files with the
`type: project-state` property (a plain text/frontmatter search works
fine — no special tool required). If more than one project is active,
ask which one is meant, unless the user already specified.

The user does not pick the phase. If their wording conflicts with the
state (e.g. "let's plan" while `phase: execution`), point out the
conflict and ask — don't silently switch phases.

### 1.1 Scope assessment

Assess: how many methodological directions does this touch, are there
material uncertainties, is it a one-off task or multi-stage work?

- **micro** — a one-off or short task. **Do not create a project.** Say
  so directly ("this isn't worth the overhead of a project") and just do
  the task normally.
- **standard** — single-track, multi-stage work. A short founding
  document (7 fields), a unified plan, a state file, a changelog. No
  council procedure.
- **full** — multi-domain or methodologically uncertain work. The full
  cycle: 13 fields, the built-in council procedure (§11), review
  points, a lessons-learned file.

This is also where the project's folder is decided (§2.1).

> Present the proposed scope level, folder, and reasoning.
> Do not proceed until the user gives explicit approval.

## 2. Storage

### 2.0 Workspace path

The skill carries no path. The workspace root — an ordinary local
folder on the user's computer, no particular app required — is recorded
in the project state file's `workspace` field and read from there.

- **First time in this environment** — ask for the workspace folder
  path. This can be any folder: a dedicated "Projects" folder, a folder
  inside a larger notes collection, an Obsidian vault, whatever the user
  already uses.
- **On resuming** — verify the path recorded in `workspace` still exists.
  If not (different machine, different OS, folder moved), report it, ask
  for the current path, and update the `workspace` field.
- **Never assume the path**, and never proceed with a broken path.

If there is no file-system access at all (a purely web-based environment
with no local files), report that plainly and stop. This skill does not
work without local files to write to.

### 2.1 Choosing the project folder

**If the project already has a folder in the workspace** (e.g.
`Thesis/my-research-topic/`), the six files are written at the root of
that same folder, alongside its other material. Do not create a new
folder.

**If it doesn't** — ask, offering the workspace's existing top-level
folders (or a new one). Don't assume.

The folder name becomes the project's identifier (the `project` field).

### 2.2 Project files

Naming convention: `<project>-<type>.md`. The prefix keeps files
unambiguous if the user later cross-references them by name (as plain
relative paths, or as wikilinks if they use a linking tool — see §2.4).

| File | Mode | Contents |
| --- | --- | --- |
| `<project>-state.md` | overwritten | frontmatter = the state block (§3) |
| `<project>-founding.md` | immutable | decisions summary + discussion summary |
| `<project>-plan.md` | editable | content plan + format plan + work plan |
| `<project>-log.md` | append-only | stage-completion entries, newest on top |
| `<project>-lessons.md` | append-only | generalized takeaways (full scope only) |
| `<project>-summary.md` | written at closing | final summary document |

**`<project>-council.md`** — written by the built-in council procedure
(§11) at full scope (§4.1). Outside of that procedure, this skill
**reads it, but never writes to it.**

These six are joined by the project's deliverable files (§2.5), which
this skill creates and updates. It touches no other files.

### 2.3 Cross-references between files

By default, reference other project files as plain relative paths or
bare filenames (e.g. "see `my-project-plan.md`"), since that works in
any plain-text folder with no extra tooling.

**Optional — if the user's workspace uses a wikilink-based note tool**
(e.g. Obsidian, or another tool that resolves `[[note name]]` links),
offer to use `[[<project>-plan]]`-style links instead, since they stay
navigable inside that tool. Ask once, per workspace, rather than
guessing from file contents.

### 2.4 Optional: shared terminology file

**Suggested, not required.** If the user is running (or expects to run)
several related projects in the same working language pair, offer a
single shared file at the workspace root, `terminology.md`, so that
foreign-language ↔ working-language equivalences (§0, invariant 16) stay
consistent **across** projects rather than being decided fresh each
time. All participating projects reference it and add to it (§8.4).

If the user declines, or is only running one project, skip this
entirely — record equivalences inline in that project's files instead.

### 2.5 Optional: external reference tools

Some users keep methodological reference material (citations, a
literature index, prior art) in a separate connected tool — Notion, a
reference manager, a shared wiki, or similar. If such a tool is
available and the user wants to draw on it, treat it strictly as a
**read-only reference source**. The project's state, plans, and
changelog are never written there, regardless of what's connected — they
live in the project's own files (§2.2), because those need to be the
cheapest, most reliable, always-available place to write.

If no such tool is connected, this is simply not part of the workflow —
don't ask for one unprompted.

### 2.6 Deliverable files

The items listed in `deliverables` are often separate files (a
measurements table, a methodology write-up, an execution log, a source
dataset, etc). The skill **creates, reads, and updates** these inside
the project folder.

- Same naming convention: `<project>-<type>.md`.
- Frontmatter: `type: project-deliverable`, `project`, `subtype`,
  `updated`.
- Created as skeletons at the end of planning, filled in during
  execution.
- The format plan (§5.2) states which file will hold which content.
- Invariant 7 applies here too: a deliverable file never states a
  measurement that hasn't actually been taken. An unfinished step is
  recorded as unfinished.
- A deliverable file left empty or skeletal is a sign that the
  corresponding stage is not actually done (invariant 8).

## 3. Reading layers

- **Layer 0** — the state file's frontmatter. Read **always**, on every
  invocation.
- **Layer 1** — **only the current stage's** section of the plan,
  `success_criteria`, `constraints`, and the deliverable file relevant to
  the current stage. Read during planning and execution.
- **Layer 2** — the changelog, the discussion summary, the full plan, the
  lessons file, the council file. Read **only in two cases**: (a) during
  the closing phase, (b) when the user asks about a specific past detail.

State file format: YAML frontmatter, not a code block — this is plain
text any editor can open, and doubles as structured data for a
query/dashboard tool if the user has one (§10):

```
---
type: project-state
project: my-research-topic
workspace: /path/to/workspace
phase: execution
level: full
stage: 3
stage_total: 6
stage_name: Data collection
substage: 2
substage_total: 4
next: Interview transcription
blocker: none
blocker_from:
updated: 2026-09-16
---

# my-research-topic — Current state

- Last completed task: …
- Next task: …
- Active issues: …
- Last review point and its outcome: …

Related: my-research-topic-plan.md · my-research-topic-log.md
```

Body: 10-15 lines maximum.

## 4. Phase A — Founding

**Purpose.** Decisions get fixed once, so later phases invent nothing on
their own.

### 4.1 Discussion

- **standard** — a direct conversation. Questions are asked in groups,
  naturally, not as a rigid questionnaire.
- **full** — run the built-in council procedure (§11), without
  duplicating its internal steps here. No separate skill or download is
  needed — the procedure is self-contained. The council runs its own log
  in `<project>-council.md` (§2.2), and its conclusion feeds the founding
  document's "Discussion summary" section. This discussion may span
  several sessions, and that's normal — founding is not rushed. If the
  agent-spawning capability §11 depends on (isolated sub-agents / running
  a sub-task with clean context) is unavailable in this environment,
  report that and fall back to a direct conversation conducted from
  several methodological angles in turn within the same context. Do not
  fail silently.

The user participates throughout, not just approving at the end.

### 4.2 Checklist

Every field must be checked; missing ones are asked about explicitly.

**standard (7 fields).** `project_title`, `project_type`, `goal`,
`scope`, `success_criteria`, `deliverables`, `timeline`.

**full (+6 fields).** `domain`, `constraints`, `risks`,
`methodology_notes`, `open_questions`, `panel_composition`.

`panel_composition` is filled from the council procedure's Phase 0
approved composition (§11.2).

`project_type` values: scientific research · data analysis · software
tool · written text · translation/editing · other.

**Principle.** Every field is exhaustive, never assumed. Every
constraint discussed must appear verbatim in the summary. Planning
invents nothing.

### 4.3 Approval

Show the completed checklist in **short form**, not the whole file.
Discuss open questions separately — either resolve them, or knowingly
leave them open, recorded in `open_questions`.

> The founding document is not written until explicit approval.

**Log.** `<project>-founding.md` (§8.1) · state file: `phase: planning`.

## 5. Phase B — Planning

**Purpose.** Turn decisions into a verifiable, executable structure.

**Reads.** Layer 1 — the decisions summary. The discussion summary only
if some field is insufficiently clear.

If the founding document is incomplete (`project_type`, `scope`, or
`success_criteria` missing), stop and go back to §4.

### 5.1 Branching by `project_type`

Affects **only the content plan**. The format and work plan structure is
the same for everyone.

- **scientific research** — literature review, methodology
  justification, data sources, validity, formatting standard
- **data analysis** — source and quality, methods, visualization,
  validation steps
- **software tool** — architecture, testing strategy, packaging
- **written text** — style, structure, target audience, length
- **translation/editing** — source and target format, terminology
  consistency, verification steps
- **other** — a general questionnaire adapted from `scope`/`deliverables`

### 5.2 Three plans

- **Content** — what must be done, per §5.1. Concrete, verifiable items.
- **Format** — what format/structure each `deliverables` item is
  presented in, and which deliverable file (§2.6) it will live in.
- **Work** — stages → substages → tasks. **Every stage explicitly states
  its deliverable** (what measurement, file, or document must exist at
  the end of the stage). At the end of every stage:

```
🔲 Review point. [what specifically is being checked]
- Is the stage's deliverable present?
- Does it match what was planned?
- Is the deliverable acceptable?
- Is a change needed?
```

`success_criteria` is embedded in the work plan as the final acceptance
criteria.

All three are written in one file, in three sections.

> Present the three plans concisely. Do not start execution until
> explicit approval.

**Log.** `<project>-plan.md` · deliverable file skeletons (§2.6) · state
file: `phase: execution`, `stage: 1`, `substage: 1`.

## 6. Phase C — Execution

**Purpose.** Work is continuable from any session, without re-explaining
context.

**Reads.** Layer 0 + Layer 1. Not the changelog.

### 6.1 Completeness check

**Mandatory, at the start of every invocation, before proposing
anything.** Compare the state file's `stage`/`substage`/`next` fields
against the plan's actual structure, and verify four things:

1. Does `stage_total` match the actual number of stages in the plan?
2. Does the task named in `next` actually exist in the plan, inside the
   stated stage/substage?
3. Do all stages before `stage` carry a "✅ Done" mark?
4. For every stage marked "✅ Done", does its deliverable actually exist
   in the corresponding deliverable file (invariant 8)?

If any check fails, **stop, name the specific inconsistency, and ask.**
Do not proceed on an assumption.

### 6.2 The per-task cycle

1. Briefly state: which stage/substage the project is in, what the last
   completed step was, what is proposed as next.
2. Wait for approval. No autonomous execution.
3. Execute, per the content and format plans.
4. **Log.** Write the output into the relevant deliverable file (§2.6).
   Overwrite the state file (first checking invariant 11 in §0). Update
   `updated`.
5. If `blocker` gets filled in, record the `blocker_from` date. If the
   same blocker survives into a 2nd session, **escalate it as a decision
   to be made**, not log it again.

### 6.3 Stage completion

On reaching a "🔲 Review point":

1. **Deliverable check.** Is the stage's stated deliverable present
   (§5.2)? If not — the stage is **not closed**. Report which steps
   remain unfinished, record them under `blocker` with `blocker_from`,
   and propose either completing them or changing the plan (§0,
   invariant 5). A user instruction of "just mark it done" does not
   override this check — in that case, say directly that the stage is
   not being marked done without its deliverable.
2. **Deviation check.** Compare progress against `success_criteria`. Any
   deviation? Report it.
3. Wait for the user's separate approval.
4. **Log, in this order:**
   - **Prepend** a new entry to the top of the changelog (§8.2). Don't
     read or rewrite earlier entries.
   - If the entry contains a conclusion, add a short line to the lessons
     file (full scope only).
   - In the plan, add **"✅ Done"** next to the stage heading.
   - Overwrite the state file for the next stage.

### 6.4 Constraint

Execution **never** proposes closing the project and never treats it as
complete, even if every stage carries "✅ Done". Closing happens
**exclusively** on the user's explicit instruction.

## 7. Phase D — Closing

**Invoked only on the user's explicit instruction.** This is the one
point where reading all of Layer 2 is deliberate and justified.

1. **Precondition.** Do all stages carry "✅ Done", does each have its
   deliverable present, and does a corresponding changelog entry exist?
   If not — list the incomplete stages and their missing deliverables,
   then ask: continue the work, or close the project as **incomplete**?
   An incomplete stage still does not get "✅ Done" at closing (invariant
   8); it is recorded in the summary's "Incomplete stages" section.
2. **Read** the founding document, the full plan, the full changelog, the
   lessons file, the deliverable files, and the state file (as a
   cross-check).
3. **Summary document** `<project>-summary.md` (§8.3).
4. **Lessons** — generalized, stripped of project-specific names and
   details, so they're applicable to projects in other domains.
5. In the state file, change only `phase: closed` and `updated`. Leave
   the rest frozen as an archive. In the council file (if any): `status:
   closed`.

## 8. Templates

### 8.1 Founding document

```
---
type: project-founding
project: <project>
level: standard|full
created: YYYY-MM-DD
---

# <project> — Founding document

## Decisions summary
- project_title: …
- project_type: …
- goal: …
- scope: included — … ; excluded — …
- success_criteria: …
- deliverables: …
- timeline: …
(also, at full scope)
- domain / constraints / risks / methodology_notes /
  open_questions / panel_composition: …
- confirmed_by_user: yes, YYYY-MM-DD

## Discussion summary
[main arguments, alternatives considered, why the final decisions were
made — at full scope, based on the council's conclusion]

Related: <project>-plan.md · <project>-council.md · terminology.md (if used)
```

### 8.2 Changelog entry

```
## YYYY-MM-DD — Stage X done: [stage name]
- Work completed: …
- Stage deliverable: [what was created and in which file]
- Material used: [references to source files/notes relied on]
- Issues encountered: …
- Solutions applied: …
- Methodology: the approach actually used; if different from planned,
  the difference and why
- Deviation from success_criteria: none | …
- Conclusion: …
```

### 8.3 Summary document

```
# <project> — Project summary

## Overview
Goal, scope, timeline, final deliverables

## Progress
Brief narrative summary, from founding to the last stage
(based on changelog entries, summarized, not copied)

## Incomplete stages
List: which stages weren't finished and what deliverable is missing
(if all are done: "none")

## Issues encountered and solutions

## Methodological deviations from the plan
List, with reasons

## Outcome against success_criteria
Each item separately: met or not, to what extent

## Dates and total duration
```

### 8.4 Terminology file (optional, workspace root, shared — see §2.4)

```
# Terminology

| Source term | Working-language term | Form | Project |
| --- | --- | --- | --- |
| overfitting | … | translation | … |
| scoring | … | transliteration | … |
```

Form values: translation · transliteration · not translated.

### 8.5 Deliverable file

```
---
type: project-deliverable
project: <project>
subtype: <baseline | methodology | measurements | execution-log | …>
updated: YYYY-MM-DD
---

# <project> — <Name>

[skeleton written at the end of planning; content filled during
execution; an unfinished step is stated as such]

Related: <project>-plan.md
```

## 9. Optional: linking to source material

Plain relative-path references (§2.3) work everywhere and are the
default. If the workspace lives inside a wikilink-based note tool (e.g.
Obsidian), offer to use that tool's linking instead — it's a genuine
upgrade there, since project documents then live in the same graph as
the underlying material and become clickable/traversable:

- **Work-plan tasks link** to the relevant source notes, e.g.
  `[[<topic>-research-questions]]`, `[[<topic>-synthesis]]`,
  `[[<topic>-inclusion-exclusion]]`.
- **Changelog entries link** to the specific sources a stage relied on,
  e.g. `[[Smith20]]`, `[[Lee16]]`.
- **With a literature-review skill.** If the project is a scientific
  literature review, that skill's steps become work-plan stages, not a
  parallel process. This skill drives the overall flow; the other one
  drives the methodology.
- **The built-in council procedure (§11)** runs the founding discussion
  (§4.1) and its own log; this skill runs the project state. No
  duplication.

Links (of either kind) are written at the moment material is actually
used, not retroactively. Never link to a file that doesn't exist.

## 10. Optional: projects dashboard

**Suggested, not required — ask before setting this up.** If the user's
workspace has a query/dashboard capability (e.g. the Dataview plugin in
Obsidian), the state files' frontmatter is directly queryable, and a
live dashboard can be offered. When creating the first project, offer
to also create `Projects.md` at the workspace root (if it doesn't exist
yet). After that it updates itself automatically, since it's a query,
not a list that needs maintaining.

````
# Projects

## Active

```dataview
TABLE WITHOUT ID
  file.link AS "Project",
  phase AS "Phase",
  stage + "/" + stage_total AS "#",
  stage_name AS "Current stage",
  next AS "Next step",
  updated AS "Updated"
FROM ""
WHERE type = "project-state" AND phase != "closed"
SORT updated DESC
```

## Blockers

```dataview
TABLE WITHOUT ID
  file.link AS "Project",
  blocker AS "Blocker",
  blocker_from AS "Since"
FROM ""
WHERE type = "project-state" AND blocker AND blocker != "none" AND phase != "closed"
SORT blocker_from ASC
```

## Closed

```dataview
TABLE WITHOUT ID
  file.link AS "Project",
  level AS "Scope",
  updated AS "Closed"
FROM ""
WHERE type = "project-state" AND phase = "closed"
SORT updated DESC
```
````

Without such a tool, this section is simply skipped: routing (§1) falls
back to a plain search for `type: project-state` across the workspace,
which works everywhere and needs no setup.

**Consequence for frontmatter.** Field names and types are meant to be
queryable even when no query tool is present yet, so treat them as an
interface, not just a note to self. Keep `stage` and `stage_total` as
**numbers**, not a `3/6` string. Keep `updated` and `blocker_from` as
`YYYY-MM-DD`, so sorting (manual or tool-assisted) works.

## 11. Built-in council procedure

Used at full scope for the founding discussion (§4.1). This procedure is
part of this skill — it is not a separate skill to install, and nothing
else in this document depends on one being available. It only needs the
ability to run isolated sub-tasks with a clean context (e.g. sub-agents),
ideally some of them on a different underlying model than the main
session; §11.0 covers what to do when that capability, or model choice,
isn't available.

A structured discussion, where positions are formed in isolated contexts
and, where possible, by different models, before any seat reads another's
output — then they are clashed against each other, evaluated, and
checked.

**An honest limitation.** If every seat runs on the same underlying model
family, isolation removes anchoring but not shared priors; different
models dilute common assumptions but don't eliminate them. A blind spot
shared by the whole council does not fully disappear from the inside —
the one genuinely independent node against that is the user, and the
procedure is built around bringing a correlated assumption to them
explicitly, rather than letting it hide.

**Never claim that isolation or model variation guarantees independent
opinions.** They reduce correlation, nothing more.

### 11.0 Invariants

Hold across all sub-phases, without exception. These are specific to the
council procedure; the parent skill's own invariants (§0) — no
fabricated data, explicit approval before acting, no self-declared
completion, language rules — apply here too and are not repeated below
except where the council procedure adds something.

**Core**

1. **Fabricating data is absolutely forbidden.** Every number, percentage,
   date, and citation is labeled: `from source` · `assumption` · `needs
   verification`.
2. **No filler.** Every line must survive one question: what is lost if
   this line is deleted? If nothing — delete it. No seat says something
   merely because it's expected to speak, chair included.
3. **Silence rule.** An invoked seat may return "nothing material to
   add" and not appear in the output. A check that was actually
   performed is always counted, even when its result doesn't surface in
   the output.
4. **The critic is never dropped.** The decision that "this discussion
   doesn't need a critic" is made by exactly the model whose
   agreableness bias the critic exists to counterweight.
5. **The main session withholds its own opinion until Phase 3 is done.**
6. **Phase 5 and Phase 6 are never skipped** (Phase 6 excepted only in
   quick mode).
7. Every excluded seat is named in the Phase 0 proposal, with a reason.
8. **An unknown is never filled in by assumption.** A missing input stays
   unknown and is passed to the seats as unknown.

**Naming**

9. **Every seat has a name — a role or a qualification.** Numbering
   ("seat 2") or a generic label ("Subject-matter 1") is never used in
   the proposal, in a seat's brief, in the output, or in the log file.
10. **The source of a missing input, an objection, or a question is
    named, not counted.** "The Critic and the Implementer" is correct;
    "two seats" is wrong.
11. Approval points carry no special name; the proposal is presented and
    approval is awaited. Do not use a vague stand-in word for this.

### 11.1 Modes

| Mode | What it includes | When |
| --- | --- | --- |
| quick | one pass, 3 lenses, no sub-agents, no Phase 6 | a one-off, narrow question |
| standard | isolated seats + Phase 6, no search | a contested or multi-domain question |
| deep | full elicitation + approved search + a log file | founding a project, a discussion worth spending time on |

### 11.2 Phase 0 — Assessment and proposal

Assess: how much does the answer depend on the user's private context,
and how contested or high-stakes is the question?

Present in **one short block**:

- the proposed mode and why
- the seat composition, one line each, **named and with its model
  stated**
- the default-excluded seats, with a reason

The elicitation phase's length is **not estimated in advance** and is
not capped.

**Naming seats.** Every seat is presented by a professional name, role,
or qualification: "Critic", "Methodologist", "Second-language
acquisition specialist". The "Subject-matter" row in §11.5's table is a
status, not a name.

> One approval, not three separate questions. Do not proceed before it.

#### 11.2.1 Model assignment

When spawning a sub-agent, the model is stated explicitly. This is the
one free lever that genuinely introduces different weights into the
discussion.

The assignment is decided by **the nature of the material under
discussion**, not a fixed table, and is included visibly in the Phase 0
proposal.

**Rules.**

- With three or more seats, **at least two different models** must be
  present. Running everyone on the same model means giving up the one
  free lever available.
- The seat whose work depends most on depth of engagement with the
  material gets the strongest available model.
- The seat whose work is a structural frame (e.g. the outside view) can
  run on a different model for the sake of decorrelation.
- **The Phase 6 checker is never run on the chair's model.**
- **The model must be capable enough for the working language.**
  Lightweight models produce unreliable analytical output in some
  languages — garbled phrasing, computational errors, and unsupported
  figures have been observed. Don't use a model below that bar for a
  seat whose output is in such a language; pair models from clearly
  different capability tiers instead.
- If a seat's output is linguistically or computationally substandard,
  the chair does not fix it — the seat is re-run on a different model,
  and the change is logged in the log file.
- The assignment is recorded in the log file (§11.10). On resuming, the
  same seat runs on the same model, otherwise old and new positions
  become incomparable.

### 11.3 Phase 1 — Elicitation

No position is voiced until the picture is complete. Sub-agents cannot
ask follow-up questions, so all elicitation finishes before Phase 3.

**Checklist.**

1. **Why** — what problem does this solve, what changes if it isn't solved
2. Goal and success criterion
3. Constraints: time, resources, data, skill
4. What's already been tried, and with what result
5. What's fixed and what's negotiable
6. Who is the decision-maker, who is affected
7. Deadline

**Procedure.** Round 1: one complete package, grouped by seat and asked
by name. Later rounds: only the gaps. The exit criterion is
completeness, not a round count — stop when the checklist is filled or
the user says "that's enough".

**The number of questions is driven only by necessity.** No question is
asked to hit a quota, and there's no upper bound — if there are many
material questions, all of them get asked.

**The unknowns list.** At the end of Phase 1, compile an explicit list of
what remains unknown. It goes into every seat's brief.

### 11.4 Search — deep mode only

First present what will be searched for and why, then wait for approval.

Search isn't only about speed. When a seat's position rests on a source
it found rather than its own training, its errors become **source**
errors, which correlate less across seats than a shared model prior.
Search is thus also a decorrelation tool.

### 11.5 Phase 2 — Composition

| Seat | Status |
| --- | --- |
| Critic | always, cannot be excluded |
| Outside observer | default, excludable with justification |
| Methodologist | default, excludable with justification |
| Implementer | conditional — when there's something to implement |
| Risk and ethics | conditional — when there are risks or an ethical question |
| Subject-matter | 1–3, depending on the question |
| Chair | always |
| Checker | standard and deep mode |

Ceiling: 5 seats in standard mode, 7 in deep mode (chair and checker not
counted). When the ceiling is hit, subject-matter seats are trimmed
first, never the default seats.

The "Subject-matter" row in the table above is a **status, not a name**.
Every subject-matter seat gets its actual qualification as its name:
"Second-language acquisition specialist", "Learning-focused cognitive
psychologist", "Structural biologist".

#### Briefs

- **Critic** — builds the strongest case against the obvious answer.
  **Load-bearing claims only** — the ones whose being wrong would flip
  the conclusion. Critiquing phrasing, word choice, or minor detail is
  disallowed. Every objection must state what changes if the objection
  is correct.
- **Outside observer** — the "outside view" (Kahneman and Lovallo).
  Deliberately ignores the case's inside details and judges by the
  reference class's base rate: what usually happens in cases like this,
  what makes this case special, and is that claim to specialness
  justified. Difference from the Critic: the Critic attacks a specific
  argument, while the Outside observer doesn't argue with the argument —
  it assumes the argument is correct and asks how many cases like this
  actually reach their goal. It is the one seat that counterweights
  planning-fallacy optimism from inside the case. When there's no base
  rate to draw on, that is stated explicitly rather than inventing a
  number.
- **Methodologist** — evaluates not the conclusion but how it was
  reached: is the evidence sufficient, does the conclusion follow from
  the premises, what design would test it.
- **Implementer** — owns the "how". What steps does this actually take,
  which step is uncertain, what resource or skill is missing, which
  first step is most likely to fail.
- **Risk and ethics** — what could go wrong, who is affected.
- **Subject-matter seats** — each applies its own domain's concrete
  standards and is introduced by its qualification's name.

### 11.6 Phase 3 — Isolated positions

Every seat runs as a **separate sub-agent, with a clean context**, in
parallel, on its assigned model (§11.2.1).

**The brief contains:** the question, the user's answers, **the unknowns
list**, that seat's own role brief, and the language rules that apply to
this project (per §0 of the parent skill). **It does not receive any
other seat's text.**

The unknowns list is accompanied by a specific instruction: "these are
unknown — do not fill them in by assumption; state what would break your
position if it turned out otherwise."

**Required return structure.**

```
Position. [3-6 sentences]
Underlying assumptions. [what this position stands on]
What input did I lack. [specific; empty if nothing]
What would change this position. [a specific datum or argument]
Confidence. high | medium | low — why
What I don't know. [stated explicitly]
Sources. [search mode only]
```

#### 11.6.1 Stop rule

If **two or more seats** name the same missing input, the checklist was
incomplete right at that point. **Stop before Phase 4**, present the
gap, and ask. When presenting it, name the missing input's source by the
seats' **names**, never a count. After the gap is filled, only the seats
for which the new data is material are re-run.

This is the mechanism that turns an incomplete checklist into a
detectable event instead of a silent distortion.

### 11.7 Phase 4 — Cross-examination

Run by the chair, in the main context.

1. **Real disagreements** — where seats actually contradict each other,
   and about what. No artificial disagreement is manufactured.
2. **Shared assumptions** — assumptions made by **every** seat are pulled
   out and flagged as a **shared council blind spot — unverified**.
3. **Unanimity signal** — if every seat agrees, that can mean either the
   right answer or a shared blind spot. The procedure can't tell them
   apart, so **unanimity is flagged as a signal, not as grounds for
   confidence**.
4. **Oversight checklist.** Is the checklist filled? Is every number and
   citation labeled? Are excluded seats named with a reason? Was no
   phase skipped?
5. **Triad completeness** — do why, what, and how all have an answer? A
   missing one is logged as open.

### 11.8 Phase 5 — Chair's summary

The chair evaluates the discussion, not the subject. Averaging is not
synthesis.

Structure — items 1 and 2 are mandatory, the rest are written only when
there's something to fill in (invariants 2 and 3):

1. **Why** — relevance, meaning, significance
2. **What** — what will actually be done
3. Strengths and weaknesses, by seat
4. Gaps found
5. Synthesis — the chair's own position
6. Points of disagreement
7. Open questions

#### 11.8.1 A targeted question to the user

When Phase 4 finds a shared assumption or unanimity, the chair does
**not** ask the user for confirmation — it asks a specific,
subject-matter question: "every seat assumes X. In your domain, is X
actually true?"

Reason: in this setup, the user is the **one genuinely uncorrelated
node**. Drawing on their knowledge as a check is worth more than one
more seat.

### 11.9 Phase 6 — Verification

Active in standard and deep mode. A separate sub-agent, clean context,
**running on a model different from the chair's**.

Sees only the chair's summary and the seats' raw outputs. Answers four
checkable questions:

1. Is every claim in the summary traceable to some seat or source?
2. Was a disagreement left out of the summary?
3. Does the conclusion follow from what was presented?
4. Is there a line whose deletion loses nothing (invariant 2)?

These are close to mechanical questions — which is exactly why a
same-family model is useful here: **verifying is easier than
producing.**

The chair fixes what's found **once.** No cycle is opened — there is no
second verification pass.

### 11.10 Extended discussion and logging

**Logging is only for project-founding discussions.** A non-project
council run writes nothing to the workspace.

#### 11.10.1 Path

The procedure carries no path of its own. The workspace root is taken
from the project state file's `workspace` field, exactly as in the rest
of this skill (§2.0). The path is never assumed. Without file-system
access, report that and continue without logging.

#### 11.10.2 The file

Written into the project's existing folder: `<project>-council.md`.
Its final conclusion feeds `<project>-founding.md`. No new folder is
created, and the user's existing files are never edited.

```
---
type: project-council
project: <project>
workspace: <path>
mode: deep
status: open | closed
updated: YYYY-MM-DD
---

# <project> — Council discussion

## Composition and models
[seat (named) — model — reason for inclusion or exclusion]

## Checklist status
[the seven items — filled or open]

## Unknowns
[what's still not known]

## Positions
[by seat, latest version]

## Shared assumptions
[the council's blind spots; the user's answer, if given]

## Agreed decisions

## Open questions

## Session log
[newest on top. date, what was added, what changed]

Related: <project>-founding.md (a shared terminology.md, if used)
```

#### 11.10.3 Resuming

Read the file, briefly present the state, then continue from where it
left off.

**Not all seats are re-run** — only those whose input data changed. A
seat with unchanged input has its position taken from the file. This is
the procedure's single biggest saving.

Model assignment does not change on resuming — otherwise old and new
positions become incomparable. If a model change is genuinely needed,
that seat's position is re-run in full.

### 11.11 Output format

Lists and clear labels, not loose prose. Every seat by its name —
qualification or role — never by number or count. "The Critic and the
Implementer noted X" is correct; "two seats noted X" is wrong.

```
Proposal. [mode · composition and models · exclusions, with reasons]

Elicitation.
**[Seat name].** question(s)

Positions.
**[Seat name].** position · what would change it · confidence

Cross-examination.
[disagreements · shared assumptions · unanimity signal]

Chair's summary.
- Why: …
- What: …
- Strengths and weaknesses: …
- Gaps: …
- Synthesis: …
- Points of disagreement: …
- Open questions: …

Verification. [only if it found something]
```

## Configuration

Before first use in a new workspace, agree on with the user, once, and
keep consistent afterward:

- The workspace's working language for all project output (§0, Language).
- The workspace root path (§2.0) — never assumed, always confirmed.
- Whether they want plain relative-path references or wikilinks (§2.3) —
  default to plain unless they say otherwise or their workspace is
  clearly a wikilink-based tool.
- Whether they want a shared terminology file (§2.4) — offer once,
  default to no if they're running a single project.
- Whether an external reference tool is connected and should be treated
  as read-only source material (§2.5) — offer once; skip if none exists.
- Whether a query/dashboard plugin is installed for §10 — if not, say so
  and skip the dashboard, don't insist on it.
- Whether a literature-review skill is installed (§9) for scientific
  reviews — optional, the skill falls back to plain discussion if it
  isn't and reports the gap; it does not fail silently. (The council
  procedure itself, §11, is built in and needs no separate install.)
