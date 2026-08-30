# Changelog

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

