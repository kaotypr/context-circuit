# Changelog

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

