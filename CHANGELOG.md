# Changelog

## v2.0.0-rc.6 — 2026-09-15


# Context Circuit 2.0.0-rc.6

A candidate for 2.0.0-rc.5 that removes the progress log from plan records and
finishes the dispatch work rc.5 started. Records written by rc.5 stay readable.
The v2 description in 2.0.0-rc.1 still stands. This template recommends CLI
`2.0.0-rc.6`. Git is required. No Go or Python runtime is.

## Breaking: `record note` is removed

A plan record holds the plan. `record note` appended `## Update — <date>`, and
what an agent put there was the result of running the plan — the same summary
`record complete` writes, parked in the body because completion is a person's
request and had not come yet. Two places recording the same thing disagree the
moment one of them is written first.

The result of running a plan reaches the person, and `record complete` carries it
into the record with `completed_at` when the person asks for it. `record approve`
and `record complete` are unchanged; only the progress append is gone. A person
editing the Markdown directly is untouched: it is a readable file and always was.

A plan already holding an `## Update` section keeps it. Nothing is rewritten.

## A plan is an intention, not a history

Removing the append is half of it. The other half is that running a plan no
longer edits the plan at all. A plan record is written when the work is planned,
and changes again only when a person requests completion.

This closes something the removal alone left open. "Record it where a later
reader needs it" is a judgement any agent can reach about anything, so an
instruction that removed the command while inviting the direct edit would have
produced the same body under a different heading. The rule is now about the act,
not the command: running a plan does not edit the plan. A failing check is
reported to the person and leaves the plan unmarked and unedited, rather than
being written into it and repaired in a loop.

What this buys on resume is that the record and the diff stop competing. The plan
says what was intended, the diff says what happened, and there is no third
account of uncertain age to weigh against either. The instruction says so
directly: a record describes what was intended and never proves what was
implemented.

## What changed

- **A plan that recorded its own shape.** A planner answers under fixed
  headings, and the whole answer was being transcribed into the plan. Verdict
  and plan shape are answers to the coordinator, not plan content: both are spent
  the moment the records are created, and a plan recording that it is a single
  plan states what its own existence already says. Approach, tasks and order,
  risks and checks, evidence, and uncertainties transcribe; those two do not.
- **A coordinator hunting for the tool that spawns an agent.** The Codex guidance
  said to use "the available spawn tool", so a coordinator spent turns filtering
  the tool registry to find out what it was called, twice in one session. The
  two tools are named now. They are also to be called directly: wrapping one in
  `exec_command` makes it yield on `yield_time_ms`, turning a single wait into a
  poll loop whose every iteration is a full round-trip carrying the session.
  Cursor's read-only roles are noted as needing supplied diff text, which was
  only ever said of Claude Code's.
- **A `context/` that went flat, and entries that lost their date.** Gathering
  knowledge produced every note at the top level and, where a note named a
  repository, an entry with no `reviewed` date. Neither was a regression: the
  layout was never specified, and the only hint was an example path in the
  seeded catalog, so one session inferred directories from it and another did
  not. A note now goes in a directory named for its concern and is catalogued
  under a heading for the same one, with only the catalog and the glossary at the
  top level, and the entry's five parts are named again in the instruction rather
  than deferred whole to the catalog. The date is the half that can be checked:
  an entry naming repositories claims to describe their code, so `check` reports
  one that does not say when that claim was last confirmed. An entry naming none
  — the glossary — needs no date. A missing date is worse than untidy, since
  reconciliation at completion has nothing to move.
- **Anchors written as a run of paths, under a heading nobody chose.** A note
  ended with `Repository anchors: a, b, c, d, e.` — a string of five paths, none
  saying which question it answers, which sends a reader to open all five. The
  heading was invented on the spot, because nothing shipped named one, so each
  session picked its own. An anchor belongs in the sentence that explains it;
  where a note covers several surfaces and closes with a block naming where its
  subject lives, that block is headed `Owner:` and carries one bullet per anchor
  saying what that path owns. One heading, so a reader learns the shape once.
  The notes in this repository were rewritten the same way.
- **An ID ledger that indented by key length.** Growing an empty `intents: []`
  into a list produced entries indented to wherever the `[]` had sat — nine
  spaces after `intents:`, a different depth after a different key. It parsed and
  round-tripped, so nothing reported it, but the diffs were noise and the file is
  one people are told not to edit by hand, which makes an odd shape exactly the
  wrong thing to show them. A grown container is aligned with its key.

## Upgrading

Records are readable across this boundary and nothing is converted. A script or
habit calling `record note` has no replacement command, because the result it was
recording is what a run reports to you and what `record complete` writes when you
ask for it. Run `agent setup` after installing: the planner and worker
instructions are unchanged since rc.5, so a workspace already on rc.5 needs it
only if it skipped that step.

An `ids.yaml` written by an earlier candidate keeps its existing indentation
until its next entry, which rewrites the container aligned. No command reads the
indentation, so there is nothing to correct by hand.

Initialization preserves existing files, so a workspace created earlier keeps the
`context/INDEX.md` it has and does not receive the layout and anchor conventions
added here. The `check` addition applies to it regardless, and will report any
catalog entry that names repositories without a date. Copy the new sections from
a freshly initialized workspace if you want them.

## Candidate status

Deterministic tests cover the removal of `record note` from the command surface,
documentation parity against the commands the CLI accepts, and the brief work
carried from rc.5: composition and ordering, the quoted record, the planner's
refusal of a numbered plan and of an unapproved intent, and the `--intent` and
`--plan` pairing. The catalog's reviewed-date rule is covered across entries that
name one repository, several, and none, and the fenced example that catalogs
nothing. The ledger's indentation is covered by the record fixtures that create
several IDs.

Whether a coding host passes the returned prompt unmodified, calls the named
spawn tool directly, transcribes only the headings it is told to, leaves a plan
unedited while it runs, or files a note under a directory named for its concern,
is not established by any test here: those are instruction changes and are unproven in
that sense. Exercise the flows you rely on before depending on this release.

## v2.0.0-rc.5 — 2026-09-15


# Context Circuit 2.0.0-rc.5

A candidate for 2.0.0-rc.4 that changes how a subagent is briefed and what the
coordinator does with the result. The record format is unchanged and nothing
needs converting. The v2 description in 2.0.0-rc.1 still stands. This template
recommends CLI `2.0.0-rc.5`. Git is required. No Go or Python runtime is.

## What changed

- **A brief the coordinator felt free to rewrite.** `agent dispatch` composed a
  complete prompt and the skill asked for it to be launched, which read as
  permission to launch something equivalent. A coordinator that paraphrased it
  lost the part it could not see mattering: the quoted plan. The worker then
  spent its opening minutes sweeping the repository for the plan file, and the
  reading the delegation existed to avoid happened twice. Passing `prompt`
  unmodified is now the rule, with the reason attached — appending is allowed,
  retyping is not, and a missing detail goes in `--task` and dispatches again.
- **A planner handed the answer it was dispatched to give.** Plan shape is what
  a planner returns, yet it could be dispatched with `--plan p0001`, which
  settles the split it was asked to propose and has no single ID to pass when
  the intent turns out to hold several plans. A planner now takes `--intent`,
  refuses `--plan`, and refuses an intent that is not approved, since planning
  one plans an outcome nobody agreed to.
- **A brief that opened with instructions and ended with the address.** The
  prompt began with role instructions and glued on the working directory and the
  record. It now reads in the order an agent needs: working directory, ownership,
  the record, the task, and last what to return. The intent or plan is quoted in
  full and fenced, so its own headings stop reading as sections of the brief that
  contains it, and it names its file path. An agent sent to find a file reads the
  repository on the way.
- **A plan template with a section for progress.** A plan record holds the plan.
  `## Progress and result` arrived empty in every new plan and invited a running
  log beside the approach it was supposed to describe. Newly created plans carry
  approach, tasks and order, and risks and checks. Progress is recorded when a
  later reader needs something they would not otherwise have; completion carries
  its own summary.
- **Integration that repeated the work it delegated.** The coordinator was told
  to inspect real diffs rather than workers' claims and to run the repositories'
  ordinary checks — after a worker had already run them. That is the expensive
  half done twice, and it is why a delegated wave could cost more than doing the
  work directly. A worker now reports the files it changed, the exact checks it
  ran, and their real outcome, and the coordinator integrates from that report.
  The trade is named rather than hidden: the coordinator trusts a report it did
  not reproduce, so the worker is told that a check it did not run is one nobody
  ran and that a failure is reported plainly. The diff is read where integration
  needs it — a merge to resolve, or a report naming a conflict, a failure, or an
  assumption — not as a routine audit. Wanting a second pair of eyes is
  independent review, which is read-only, separately dispatched, and requested by
  the user.

## Upgrading

Records are readable across this boundary and nothing is converted. A plan
written earlier keeps its `## Progress and result` heading; only newly created
plans lack one.

Run `agent setup` after installing. The planner and worker instructions changed,
and native role definitions embed them, so a host still holding the previous
definitions briefs its agents with the previous text. A definition you customized
is preserved and reported rather than overwritten, as before.

A caller that dispatched a planner with `--plan` is refused and told to pass
`--intent`. Nothing else in the command surface changed.

## Candidate status

Deterministic tests cover the brief's composition and its ordering, the quoted
record and the headings it carries, the planner's refusal of a numbered plan and
of an unapproved intent, the pairing that keeps `--intent` to planners and
`--plan` to the rest, and the absence of a progress section from a new plan.
Whether a coding host passes the returned prompt unmodified, or integrates from a
worker's report instead of re-reading the diff, is not established by any test
here: those are instruction changes, and they are unproven in that sense.
Exercise the flows you rely on before depending on this release.

## v2.0.0-rc.4 — 2026-09-15


# Context Circuit 2.0.0-rc.4

A candidate for 2.0.0-rc.3 that changes the record format and does not convert
what earlier candidates wrote. The v2 description in 2.0.0-rc.1 still stands.
This template recommends CLI `2.0.0-rc.4`. Git is required. No Go or Python
runtime is.

## Breaking: records from earlier candidates are not readable

`completed` is now `completed_at`, approval moved out of the body into an
`approved_at` instant, and every record carries `created_at`. Decoding is strict,
so a plan completed under rc.1 through rc.3 fails to load rather than loading
wrongly, and `record order` — the command that reads completion — fails with it.
Nothing is converted.

The refusal names the candidate that wrote the record and what replaced the
field, because an unknown-key error reads as a corrupt file rather than a version
boundary. A workspace holding earlier records either starts fresh or has its
frontmatter rewritten by hand; there is no migration and none is planned. This is
the cost of settling the format before 1.0 rather than carrying a compatibility
shim into it.

## What changed

- **An intent grounded in nothing in particular.** The instruction said to use
  relevant existing knowledge, in a clause, while the return half of the same
  circuit had a command, a procedure, and a diagnostic. Writing an intent now
  begins by retrieving what bears on the request and reading the request's
  apparent meaning against what the project already records. What that reading
  contradicts, or cannot settle, is a question rather than a gap filled with a
  quiet assumption — which is what made a contradiction surface during planning,
  after the approval it invalidates had already been given.
- **Unsettled decisions that never reached the person.** An intent carries
  `## Open questions`: a numbered list, the question in bold, an italic `_Answer:_`
  line beneath once settled. The number is the handle a person answers by, so it
  holds still — an answered question keeps its number and is never deleted, and a
  later one takes the next unused number. A question arising during planning that
  bears on the outcome returns to the intent; a plan never silently settles one.
- **Gates that recorded a word instead of an event.** `Approval: Pending` in the
  body was a state the system wrote about itself, and a completion date could
  disagree with the prose beside it. Both gates are now instants in frontmatter,
  written by the same operation that appends the human-readable section, so the
  two cannot diverge and a second decision on the same day stays distinct.
  Absence is the only empty state.
- **A shared tiering nobody could answer locally.** A roster agrees on which role
  earns the strongest settings, but not on what each member can run: one uses a
  different host, another pays for a different model. v1 had a machine-local
  override at the workspace root and v2 shipped without one.
  `.context-circuit/role-tiering.local.yaml` overrides a single host and role
  rather than replacing the file, so an overridden role is the only one that
  stops tracking the team's later changes. Its host must already appear in the
  shared file, both face the same validation, and `agent settings` names which
  pairs this machine overrode.

- **A mirror URL that could not be resolved.** `cli_registry` accepted any value
  beginning `https://`, so a workspace could record a host with no project path
  and the failure surfaced later, on another machine, as an install error. It is
  validated where it is recorded now: an https URL naming both a host and a
  project. A workspace holding a host-only registry is refused until the value is
  corrected or removed, and because the check sits in the workspace configuration
  every command reports it rather than only the installer.

## Upgrading

Records are not carried across. Install the CLI, then start a fresh workspace or
rewrite existing record frontmatter by hand. A workspace that recorded a
`cli_registry` without a project path corrects or removes it before any command
runs. Role definitions written by an
earlier version are replaced by `agent setup`. A machine that used the v1
root-level `role-tiering.local.yaml` re-applies those settings with
`agent configure --local`; nothing reads the old path.

## Candidate status

Deterministic tests cover file and Git behavior, the documented command surface,
the record format including the refusal at the candidate boundary, the local
tiering override across several hosts, the accepted and refused `cli_registry`
shapes, and the installer on Linux, macOS, and Windows. Whether a coding host reads the entry instruction and grounds an intent
or carries open questions as it describes is not established by any test here:
those instruction changes are unproven in that sense. Exercise the flows you rely
on before depending on this release.

## v2.0.0-rc.3 — 2026-09-14


# Context Circuit 2.0.0-rc.3

A correction candidate for 2.0.0-rc.2. The v2 description in 2.0.0-rc.1 still
stands. This candidate makes subagent delegation actually reachable and stops the
command surface from misdirecting the agent reading it. This template recommends
CLI `2.0.0-rc.3`. Git is required. No Go or Python runtime is.

## What is fixed

- **Roles that nothing installed.** `agent setup` writes the native role
  definitions a host needs, but no instruction ever told a workspace to run it,
  and it appeared only inside the dispatch skill, which is read after deciding to
  dispatch. Initialization now writes definitions for every supported host, and
  `agent setup` with no `--host` covers them all rather than the one that happened
  to run it. A workspace is opened in several hosts — an intent written in one, a
  plan grounded in another, execution in a third — so installing for one leaves
  the next with nothing to invoke.
- **Dispatches the host could not launch.** Role files are host-local and
  gitignored, so a specification could name an agent type the host never
  registered while looking entirely well formed, and the failure surfaced as a
  host error or as no launch at all. A dispatch specification now reports the
  definition it expects, whether that file is present, and the setup run that
  writes it. It reports rather than refuses, so the prompt remains usable by a
  live spawn tool where native roles are unavailable. A clone carries no
  definitions, because they stay ignored; this is what makes that visible.
- **Planner and explorer shipped uncalled.** Both roles had definitions, settings,
  and working dispatch specifications, and no line of the entry instruction ever
  reached for either. Planning now names when to delegate, as a judgment about
  size rather than a step: a planner earns its cost when the reading would be
  substantially larger than the plan it produces, and a session that already holds
  the code investigates itself. The planner answers under fixed headings so its
  result transcribes into the plan record, and `not-feasible` is a complete answer
  that writes no plan.
- **Help that answered a different question.** `help member` discarded its
  argument, printed the entire surface, and exited zero. It now returns that
  command's signatures and what the command decides, refuses, or leaves to the
  caller; an unmatched name fails rather than printing everything. A group
  followed by an option reports the option it could not take instead of blaming
  the group, and the documented promise that every command takes `--help` now
  states where that holds.

## Upgrading

Existing workspaces keep their records. Role definitions written by an earlier
version are replaced by `agent setup`, which needs no argument now. A workspace
initialized before this candidate has definitions for one host at most; running
setup once covers the rest.

## Candidate status

Deterministic tests cover file and Git behavior, the documented command surface,
the installer on Linux, macOS, and Windows, and both directions of help-to-command
parity. The token installer path was exercised against the real private release on
macOS; its PowerShell equivalent is not verified. Whether a coding host reads the
entry instruction and delegates as it describes is not established by any test
here: the instruction changes in this candidate are unproven in that sense.
Exercise the flows you rely on before depending on this release.

## v2.0.0-rc.2 — 2026-09-14


# Context Circuit 2.0.0-rc.2

A correction candidate for 2.0.0-rc.1. The v2 description in that release still
stands; this one fixes two defects that made the first candidate difficult to
adopt. This template recommends CLI `2.0.0-rc.2`. Git is required. No Go or
Python runtime is.

## What is fixed

- **Installation from a private source repository.** The source repository is
  private, so its release assets are not publicly downloadable and the plain
  download path answers 404. The installers now accept a GitHub token as
  `--token` / `-Token`, or in `CONTEXT_CIRCUIT_TOKEN`, `GH_TOKEN`, or
  `GITHUB_TOKEN`, and resolve each asset through the API. Without a token they
  use the public download path unchanged, so publishing the repository later
  needs no further edit. Offline installation from a trusted release still needs
  no token.
- **Readable workspace records.** A seed writes an empty container as `{}`, and
  the record editor treated that as a style every later entry had to match. The
  first repository was written in flow style and each one after it extended the
  same line, so `workspace.yaml` and `repositories.local.yaml` degraded into
  one-liners. Empty containers now grow in block style. An existing workspace
  already written in flow style keeps that style; rewriting those two files by
  hand is enough, and later entries follow the file.

## Candidate status

Deterministic tests cover file and Git behavior, the documented command surface,
and the installer on Linux, macOS, and Windows. The token path is covered
against a fixture that refuses unauthenticated requests, and was exercised
against the real private release on macOS. The PowerShell token path is not
verified: it uses its own HTTP client and no test drives it. Whether a coding
host loads and follows the shared instruction is not established by these tests.
Exercise the flows you rely on before depending on this release.

## v2.0.0-rc.1 — 2026-09-14


# Context Circuit 2.0.0-rc.1

Context Circuit v2 is a rewrite. A Go executable maintains workspace records,
repository bindings, and Git working copies; the coding agent owns understanding,
planning, implementation, and delivery. This candidate is published for
evaluation before 2.0.0.

**v2 does not migrate a v1 workspace.** Initialization is for fresh workspaces.
An existing v1 workspace keeps working with v1 and is never rewritten in place.

## Installing

The executable is a separate product with its own releases. Ask the workspace's
`cc-cli` skill to install it; the skill selects the current platform, verifies
the download, and installs without administrator access. This template
recommends CLI `2.0.0-rc.1`. Git is required. No Go or Python runtime is.

## What is new

- **A Go executable.** Native binaries for macOS, Linux, and Windows on amd64
  and arm64, replacing the shell runtime. Windows no longer needs WSL.
- **Copy-on-write worktrees.** Preparing a worktree clones the bound checkout's
  ignored `node_modules` and `.env` files using the filesystem's own cloning
  where available, with an independent-copy fallback. Dependencies are skipped
  when package inputs differ, so a new worktree rarely needs a reinstall.
- **Subagent roles.** Explorer, planner, worker, and an explicitly requested
  reviewer, with per-host model and effort settings. The `cc-dispatch` skill
  resolves each task and launches it through the host's own tools.
- **Stacked plan execution.** `record order` derives dependency waves, each
  plan's starting reference, the integration merges a dependent plan needs, and
  which plans in a wave share a repository. It recommends overlapping plans or
  chaining them, and reports the cost of each. Completing a plan releases its
  dependents; an unfinished one holds them.
- **Repository operations.** Connect an existing checkout, clone a repository,
  or initialize a new one. The workspace root itself can be a repository.

## What is gone

The v1 trust machinery is retired rather than reimplemented. There are no
consequence tiers, candidate digests, path leases, execution or verification
records, automatic repair loops, or external publication. Independent review is
a manually requested read-only inspection that never starts on its own and never
blocks a pull request, delivery, or completion. `check` is a diagnostic, not a
gate.

Plans are workspace-global and a plan may span several repositories. Member
allocation bands are optional rather than structural: a solo workspace counts
from `i001` and `p0001`, and a team assigns each member a band so separate clones
allocate without colliding. Intent approval is still a real human decision, and
commit, push, pull request, merge, deployment, and deletion still require
explicit authorization.

## Candidate status

Deterministic tests cover file and Git behavior, the documented command surface,
and the installer on Linux, macOS, and Windows. Whether a coding host loads and
follows the shared instruction is not established by those tests. Exercise the
flows you rely on before depending on this release.

## v0.1.0 — 2026-09-08


Ships the v1.0.0 runtime layer on top of `0.0.1-alpha.4` (v0.7.0). This is a
pre-1.0.0 **minor** bump: the assembled artifact is backward-incompatible
(nested product home, plan schema 3, no per-plan approval, intent required).
It is the first release of the two-gate lifecycle: the human decides what
"correct" means (intent) and when it ships (delivery); everything between
those gates is mechanical, consequence-tiered, and self-invalidating.

Compared with v0.7, the assembled artifact moves the product home, adds two
skills and a planner child, and drops per-plan approval. The earlier
spec-adversary and automated scope-envelope design is **not** in this cut —
Gate 1 is followed by a post-approval planner and a feasibility check; scope
safety stays at delivery.

- **Intent front door (`cc-intent`).** A writing change starts as a first-class
  `intent/<id>/`: human-facing `INTENT.md` plus machine `contract.yaml` (goal,
  non-goals, constraints, outcome-level acceptance criteria, optional coarse
  scope, provisional tier). The coordinator drafts it from the plain ask without
  reading the codebase. Approval is Gate 1 — it freezes `contract_digest` and
  confirms the ask was understood. Adds `INV-INTENT-01` and the
  `intent-contract` schema. Intent ids are `i<NNN>-slug` (ceiling `i999`),
  allocated inside the current member's band.

- **Post-approval planner (`cc-trace`) and feasibility.** On approval, one
  read-only **planner** child per repository reads the real code and writes that
  repository's plan when the look is feasible. The coordinator then runs a
  feasibility check: feasible → publish with no second gate and without rewriting
  the child's prose; not feasible → stop and return the blocker; a required
  change beyond a bound scope is surfaced as a question. There is **no**
  automated scope gate. Adds `INV-INTENT-02`, the `planner` role, and the
  `trace-manifest` schema.

- **Plan contract v3.** Every Standard/Critical plan names its parent intent and
  exactly one repository. Plan-level approval is gone: `plan.yaml` status is
  `draft` until explicit mark-done (`done`). Task paths are advisory grounding,
  not an exhaustive write allowlist. Legacy plan schemas are not executable by
  the v1 runtime; in-flight v0.7 plans need a human-bound intent if they must
  keep running. Stacked plans still exist, materialized atomically inside the
  member's plan band.

- **Candidate identity and staleness.** Independent-check results and human
  acceptance bind to a **candidate** (digest over per-repository commits,
  selected bases, and the frozen contract). A new commit or criteria change
  yields a new candidate and voids prior evidence. Several stacked plans that
  converge to one pull request are one candidate → one verification → one
  acceptance. Adds `INV-CANDIDATE-01` and the `candidate` /
  `human-acceptance` schemas.

- **Tiered assurance; `cc-pair` is Explore.** One consequence ladder (Explore /
  Standard / Critical), declared on the intent and failing upward.
  **Explore** is human-supervised `cc-pair` — one worker, no planner, no
  verifier, never labeled verified — and is promotable in place by attaching an
  intent and raising the tier. **Standard and Critical** require an independent
  read-only verifier bound to the current candidate. Adds `INV-ASSURE-01`;
  `INV-PAIR-01` is retained as the Explore mechanics, not a separate mode.

- **Completion and Product Knowledge.** A plan becomes done only when a human
  asks to mark it done. Mark-done has no unreadiness check: verification,
  candidate acceptance, and delivery never complete a plan. Explore is planless
  until promotion. When the plan affected Product Knowledge, that same
  mark-done reconciles live `context/` in place — no proposal staging path, no
  extra knowledge-acceptance gate, and no next-plan grounding block. Live
  context holds durable knowledge only (`INV-KNOWLEDGE-03`): it never names a
  particular plan, intent file, or sources file. Delivery remains a separate
  explicit action (Gate 2).

- **Nested product home.** Shipped wrapper, role files, and product docs live
  under `.context-circuit/{wrapper,agents,docs}`. The workspace root stays the
  project's: thin `AGENTS.md` / `WORKFLOW.md` / `CLAUDE.md` / `CURSOR.md` and
  `.agents/`. An upgrade moves former top-level `wrapper/`, `agents/`, and
  `docs/` into that nested home and does not move workspace-owned files.

- **Host-native routes.** Committed `.claude/`, `.codex/`, and `.cursor/` trees
  are thin routes into `.context-circuit/agents` and owning invariants (Claude
  skill links to `.agents/skills/cc-*`; Codex has no `.codex/rules/`). They do
  not copy role bodies. Root `CURSOR.md` is new. Personal host state stays
  personal; an upgrade adds missing product stubs and does not overwrite
  overrides. Maintainer-only Claude extras (`cc-human-simulator`,
  `cc-test-case`) do not ship.

- **Member roster and band allocation.** Committed `members.yaml` maps each
  member to non-overlapping intent and plan number bands. Each machine names
  one roster member in gitignored `member.local.yaml` (same host-local
  convention as repository bindings). Allocation never asks for a block number.
  Adds `INV-MEMBER-01`.

- **Repository terminology.** The canonical binding and execution-record field
  is `base_branch`. Schema-1 `anchor_branch` bindings remain readable and migrate
  to schema 2 with `repository-binding-migrate`. New writes use `base_branch`.

- **Blank seed and landing page.** The uninitialized template gains `intent/`
  and `members.yaml`, drops `context/proposals/` and `context/sources.yaml`, and
  ships a first-run README (initialize, connect, work live or approve an
  intent, then ship as a separate ask).

Internal `runtime_version` is `1.0.0`. Upgrade still replaces only
template-owned files and preserves workspace identity, accepted context,
sources, plans and their status, local bindings, connected repositories,
runtime evidence, and dirty worktrees. The published template version is
stamped `0.1.0`.

## v0.0.1-alpha.4 — 2026-08-30


Ships the v0.7.0 layer on top of `0.0.1-alpha.3` (v0.6.1). Four bounded scopes,
adding at most one new capability and no core plan-contract bump:

- **Direct collaboration (`cc-pair`).** A standalone interactive collaboration
  mode for working on a bound repository with the agent, with three actors (user,
  coordinator, worker) and **no verifier, no lease, no execution records** — the
  human is the live oracle. It is **orthogonal to the plan lifecycle** (not a plan,
  not an execution): entered anytime a repo is bound, by intent / `/cc-pair`, or
  offered after a plan/stack execution completes. Each session runs in its own
  `cc-pair/<session>` branch and worktree from a base commit, never on the active
  or a plan's branch in place, tracked only by a light `pairing-session` pointer.
  Output is human-supervised and **never "verified"**; it never auto-completes and
  never delivers. Adds one skill (`cc-pair`), one boundary invariant
  (`INV-PAIR-01`), and one additive `pairing-session` schema — and **no
  plan-contract change**. Supersedes the earlier `ui-refinement` framing.

- **Execution latency.** Two inference-layer levers that make actions finish
  sooner without changing what they mean: overlapping provably-independent
  run-stack plans (safety from existing `INV-CONCURRENCY-01/02`) and per-role model
  tiering the host adapter applies on the child spawn (`INV-HOST-01`,
  `INV-RUNTIME-01`). The deterministic runtime stays thin. The only new record is
  additive per-attempt `(model, effort)` evidence. **No new skill and no new
  invariant.**

- **Runtime opacity.** Hardens the **invoke-not-read** boundary: a role that
  invokes `wrapper/runtime/engine.sh` **must not read** its implementation (nor any
  runtime implementation file). Strengthens the `INV-RUNTIME-01` corollary from
  "need not read" to "must not read," states it once in `wrapper/adapters/AGENTS.md`
  with the invoking skills referencing that single owner, and closes any skill
  information gap that tempts the read. **No new invariant** — a wording and
  single-ownership cleanup.

- **Publication intent.** A structured, user-owned local home for the provider
  field values a publication pushes (dates, time estimate) plus a
  consult-before-publish preview. Adds an `intent/` layer (canonical
  `estimate_minutes`, `"2h 30m"` human I/O), a last-published `fields:` snapshot on
  the publication record, and a `cc-publish` preview that diffs and edits before
  pushing. Clarifies `INV-EXTERNAL-02` wording to permit a display-only drift read
  (not an inbound flow). **No new skill and no new invariant**; a mode of the
  existing `cc-publish`.

Internal `runtime_version` moves to 0.7.0. All additions remain bounded and grant
no new authority to the core plan → approve → execute → verify → deliver workflow.

## v0.0.1-alpha.3 — 2026-08-29


Ships the v0.6.1 refinement layer on top of `0.0.1-alpha.2` (v0.6). Five bounded
changes, no new authority and no core contract bump:

- **External-service references.** A first-class, plural `context/references/`
  Product Knowledge category for reference knowledge about external services the
  workspace consumes but does not own, one sub-directory (`README.md`) per
  service, mirroring `domains/`. The owned-vs-external rule is stated once in the
  conventions and the route is registered in the layout index and blank seed. A
  wrapper/gateway repository's own knowledge stays in `domains/`; only knowledge
  about the external service itself lives here.

- **Worker brief relocated.** The promoted, shipped runtime brief template moves
  out of the workspace root to `wrapper/runtime/`, beside the `engine.sh` code
  that consumes it, so the released template root stays clean of internal runtime
  machinery. The engine prefers the new location and falls back to
  `wrapper/adapters/` for source checkouts; grounding behavior is unchanged.

- **Unified `worker` role name.** The implementing execution role has one name —
  `worker` — everywhere, retiring `writer` as a synonym. The brief follows the
  role in concept, file (`writer-brief.md` → `worker-brief.md`), engine function,
  and command. A rename only: rule IDs, semantics, authority, and contracts are
  unchanged.

- **System-design grouping dimension.** The middle segment of the system-design
  source path is generalized from a strict version to a grouping dimension
  (`<product-or-project>/<grouping>/<scope>/`), defaulting to a version and
  allowing a named grouping as an explicit author choice. Version groupings use
  3-number `v`-prefixed semver going forward (`v0.6.1`); legacy `v0.5`/`v0.6`
  folders stay. This refines the `cc-system-design` skill's layout convention
  only.

- **Versioned, clean dist build.** The dev `build-dist.sh` wrapper names its
  artifact for the template release version it carries (`template_version`,
  matching the published archive name) instead of a drifted literal, and each run
  replaces the dist output so re-runs neither fail the no-overwrite guard nor
  leave stale trees. The strict publication path is untouched.

Internal `runtime_version` moves to 0.6.1. All additions remain bounded and grant
no new authority to the core plan → approve → execute → verify → deliver
workflow.

## v0.0.1-alpha.2 — 2026-08-27


Ships the v0.6 product layer on top of the initial `0.0.1-alpha.1` release. Adds
run-stack execution (`cc-run-stack`) for dependency-ordered multi-plan runs,
optional inter-plan `plan_dependencies` with plan schema 2 (`runtime_version`
0.6.0, plan `accepted_schema_versions` `[1, 2]`), path leases for
concurrency-safe overlapping writes, required repository grounding recorded as
execution evidence, and the opt-in external publication surface (`cc-publish`)
with the `plan` and `thread` kinds. Also ships the `cc-system-design` authoring
skill, visible archive directories, and refreshed guides and terminology. All
additions are bounded and grant no new authority to the core plan → approve →
execute → verify → deliver workflow.

## v0.0.1-alpha.1 — 2026-08-25


First published release of the Context Circuit universal project workspace
template. Establishes the binding to `context-circuit-template` and ships the
initial product layer: the runtime, contracts and schemas, host adapters, role
guidance, skills, guides, and the blank uninitialized workspace seed.

