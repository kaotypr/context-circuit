# Changelog

## v2.5.0 — 2026-10-29


# v2.5.0

This workspace version pins CLI 2.5.0. No record's content changes.

## Upgrade notes

A 2.5.0 CLI refuses changes to a workspace still at schema 2 unless the pinned CLI runs them. Bring in this template first, then let the pinned CLI make the ID split through the launcher; one person on a team makes the split, commits and pushes it, and everyone else pulls before changing anything. (kaotypr/context-circuit-source#177)

Every member installs CLI 2.5.0 before pulling a workspace whose `archive/` was renamed to `_archive/`, because a 2.4.x CLI does not read `_archive/`. One person runs the first changing command, commits and pushes the moved records, and everyone else pulls before changing anything. (kaotypr/context-circuit-source#209)

## Added

Every workspace now carries a launcher: `.context-circuit/cli`, run as `sh .context-circuit/cli COMMAND …`, and `.context-circuit/cli.ps1` for Windows PowerShell or cmd. It runs exactly the version `.context-circuit/CLI_VERSION` pins from the installers' version store, never a CLI found on PATH, and when that version is not installed it refuses and names the install step. It refuses a `--workspace` that names another workspace. It looks in the installers' default directory, or in `CONTEXT_CIRCUIT_BIN_DIR` when that is set. `.context-circuit/.gitattributes` keeps `cli` at LF and `cli.ps1` at CRLF line endings on every checkout. (kaotypr/context-circuit-source#177)

`.claude/settings.json` allows both launcher commands, so Claude Code runs them without a prompt. A workspace that already has the file merges the two entries into it entry by entry, keeping every entry it added, rather than replacing it. (kaotypr/context-circuit-source#178)

`agent setup` turns Claude Code's commit attribution off in the gitignored `.claude/settings.local.json`, and `.gitignore` carries that file. The orient check names the setting, so a machine without it runs `agent setup`, which is how existing workspaces receive it. The agents guide names Cursor's personal and team controls; the written no-attribution rule stays the guard where neither is set. (kaotypr/context-circuit-source#208)

`AGENTS.md`, the record and command references, and the `cc-complete` skill describe grouped records and the archive: a record may sit in one level of group folder with `_archive/` above or below it, `record list` and `status` show your own work first, and `record archive` moves your own finished records under `_archive/`. After completion the agent offers to archive, and declining the offer changes nothing. This workspace version pins CLI 2.5.0, which renames an existing `intent/archive/` or `plans/archive/` to `_archive/` on its first changing command; no record's content changes. (kaotypr/context-circuit-source#209)

## Fixed

The worktree reference also names an `npm link` inside node_modules as an entry reuse rejects, and the `cc-cli` install script now requires the CLI archive's `LICENSE` to be a regular file, as it already did for the binary, `README.md`, and `THIRD_PARTY_NOTICES.txt`. (kaotypr/context-circuit-source#169)

Several shipped skills now say what the CLI and installers actually do. `cc-intent` and `cc-plan` say that `record approve` also returns `commit_recommended`, to pass on with the person's authorization; `cc-complete` reads the completed record from the `completed` key; `cc-stacked` records an integration merge's resolution in the merge commit and the report to the person rather than in a workspace record; and `cc-cli` explains a pin with a reserved label such as `2.5.0-rc.1`, a prerelease of the stable line that is never offered as an update, and that the installers accept only `2.*` versions without a `v` prefix. (kaotypr/context-circuit-source#203)

A wrong fact in mounted, borrowed knowledge is neither copied nor corrected in `context/`, now on each host's smallest model too: `AGENTS.md` and `cc-knowledge` say to tell the person the fix goes upstream through that repository's own review, and not to edit, clone, commit to, or push the borrowed repository, even from a separate checkout, unless the person asks for that as its own task. (kaotypr/context-circuit-source#208)

An intent is presented in the language the person writes in, even when the record is in its author's; `AGENTS.md` and `cc-intent` say so, and the rule now holds on each host's smallest model. (kaotypr/context-circuit-source#208)

A shipped root `.ignore` with `/sources/` keeps `sources/` out of ripgrep-based search, including Claude Code's Grep and Glob, and Cursor's search, so the rule to leave sources unread unless named now holds on each host's smallest model. A file named by the person or the task still opens, and `AGENTS.md` says to leave the folder out of searches that ignore the file. (kaotypr/context-circuit-source#208)

`AGENTS.md` now says that `workspace.yaml`, `members.yaml`, `member.local.yaml`, and `repositories.local.yaml` sit at the workspace root, never under `.context-circuit/`. Agents looked for them there, found nothing, and either searched the workspace or refused to go on for want of a roster. (kaotypr/context-circuit-source#209)

## Changed

The worktree reference now says what copy-on-write reuse does with an entry it cannot reuse: under `--copy-mode auto` or `copy` it is skipped and reported, while `required` fails on it and `off` reuses nothing, and a rerun skips it again until its source changes, while any other failure, such as a full disk, stops preparation and keeps what was finished. A Windows file smaller than one cluster is copied under `auto` and fails preparation under `required`. (kaotypr/context-circuit-source#173)

The README's release badges and links point at the forge the template was published from, GitHub or GitLab. (kaotypr/context-circuit-source#175)

Every instruction, skill, and command reference now calls the launcher. When CLI output warns that the workspace pins another version, or refuses a change for that reason, the agent acts on none of it and runs the command again through the launcher. A CLI from another release line is a different product for the workspace, never a substitute. (kaotypr/context-circuit-source#180)

"The latest release" and "a later tag" mean the latest and later ones on the workspace's own release line: a stable pin updates among stable releases, and a `<label>` pin among that label's releases. Moving to another line is a template change a person names by its exact tag. (kaotypr/context-circuit-source#180)

The template repository's Code of Conduct is now the Contributor Covenant 2.1, with a contact address for reports. (kaotypr/context-circuit-source#188)

The template repository's contributing page now says where the CLI packages are published: on the source repository, with each `cli-v*` release. (kaotypr/context-circuit-source#189)

Where `record approve`, `record complete`, or `record archive` return `commit_recommended`, the agent now asks one question: commit and push the records and ID files it names, or commit only. The person's answer authorizes that commit and a push of the workspace's base branch to its upstream, that once; after commit only, the agent says other clones cannot see the records yet, and with no remote or upstream it commits and says why it could not push. A push refused because the remote moved on is brought up to date with `git pull --rebase`, which replays only the person's unpushed record commits: what Git combines is kept and reported, an ID file conflict keeps the union of both sides' entries, and any other conflict — the same record changed on both sides, a record deleted on one side and changed on the other, or a conflict in a file that is not a record — goes to the person with both versions, the rebase left in progress. The agent never edits a conflicted record itself, not even to keep both sides: which version stands is the person's call. `check` follows; a number reserved twice goes to the person. The agent pushes once more, reports a second refusal or a refusal for any other reason, and never force-pushes. Teammates' ID file conflicts from upgrading at the same time are resolved the same way, by the union and `check`. Delivering code is unchanged: drift there is still explained, not rebased. (kaotypr/context-circuit-source#209)

## Contributors

@kaotypr, @hallode

## v2.4.1 — 2026-10-21


# v2.4.1

This workspace version pins CLI 2.4.1. Nothing in an existing workspace changes,
and a team can move to it member by member.

On a workspace an earlier CLI wrote, the setup step that completes setup now
makes the ID split itself: the `member band`, `member add`, or `member use` that
leaves every member with a band and this machine with a member selected. The
agent tells the person before that step, as it did before the first change in
2.4.0, and the split's notice names every file to commit, the roster included
when the step changed it. Only one person on a team upgrades first; they commit
and push those files, and everyone else pulls before changing anything.

A refused deletion now shows its preview: the records the person asked for, the
records blocking them and how each links, and the IDs to name too. The agent
shows that list and the reason in one answer instead of running the command
again to find out.

The command reference says that `--workspace` and `--json` may follow the
command, and that a flag a command does not take is answered with that command's
usage.

A prepared worktree reports `start`, what it actually started from, beside
`base_branch`, where its work is delivered. When the start was a local branch
behind its fetched remote, `sync_required` says so with the counts and the
remedy, and the worktree skill has the agent pass that on rather than build on a
stale start unremarked.

## v2.4.0 — 2026-10-20


# v2.4.0

This workspace version pins CLI 2.4.0, which every member needs, and one of them
goes first. The first changing command it runs in an existing workspace splits
the shared ID file into one file per member, `.context-circuit/ids/<member>.yaml`,
and moves the workspace from schema 2 to schema 3. Older CLIs refuse a schema 3
workspace with "workspace schema 3 requires a newer CLI; this CLI supports
schema 2", so every member installs 2.4.0 before pulling the split. A 2.3.0
`member list` on a roster with roles fails on the unknown `roles` field instead.
A template update reads, rather than applies, a delta that deletes the shared ID
file or changes the schema version.

The split writes every author's file, so only one person on a team upgrades
first: they make the first change, commit it, and push it, and everyone else
pulls before changing anything. The agent says so before that first change.
Teammates who upgrade at the same time get a Git conflict in
`.context-circuit/ids/*.yaml`, which is theirs to resolve by hand: keep the
union of both sides' entries in each member file, then run `check`, which
reports any number reserved twice.

A shared ID file that comes back after the split, for example merged in from a
teammate still on 2.3.0, is split again by the next changing command, which adds
only new reservations. A number reserved in two member files is a `check`
finding the agent brings to the person rather than fixing. A member ID that is a
Windows device name, such as `con` or `nul`, is refused; one a 2.3.0 roster
already holds is reported by `check` and stops the split until the person
renames it by hand. A newly initialized workspace starts at schema 3 with one ID
file per member.

Approval no longer starts planning on its own. The agent records the approval,
recommends planning, and stops; a request that approves and asks for plans in
the same breath continues straight into planning. Where the installed CLI is
older and still says `planning_required`, `AGENTS.md` wins, and the agent tells
the person their CLI is behind the template.

Approval and completion now say which of their records and ID files are not
committed, as `commit_recommended`. The agent passes that recommendation on to
the person and commits workspace records only when the person authorizes it;
the CLI never commits. Where Git cannot be read, the result says so as
`commit_unchecked`, and the approval or completion still stands. This implements
#144, reported via #141.

Every workspace has members, and every member a band; a person working alone is
a team of one. Initializing asks for the first member and suggests band 1. Band
0 is an ordinary band, numbering from `i001`. Until the roster lists a member,
every member holds a band, and this machine has selected one, the agent declines
changing requests and walks the person through the missing step, then carries on
with what they asked. A member whose band 2.3.0 stored as 0 counts as unbanded
and is asked to pick a band; picking 0 keeps their old numbering. A 2.3.0 roster
hand-edited to give two members `band: 0` is now a roster error that `check`
reports, and the person moves one of them to another band by hand.

Adding a member always gives them a band, and setting band 0 gives band 0 rather
than clearing one; a band is never cleared. Deleting every record with
`record delete --all` is only for a roster of exactly one member, so in a larger
team the agent deletes one member's records at a time, or the records the person
names. A roster two clones broke
— conflict markers in `members.yaml`, two members on one band, or one member
given two bands — is reported by `check` for the person to repair, and the agent
declines changes until they have.

A person can now delete just the records they name, with `record delete --id`:
an intent they changed their mind about, a member's abandoned records when their
band runs out or they leave the team, or a number reserved with no record. The
agent shows the preview, with each record's author and whether it is approved or
completed, and any warning about leftover `cc/pNNNN/*` branches, and confirms
only once the person agrees. A deleted plan leaves its intent's list; any other
link refuses until the person names that record too. A deletion that stops
part-way keeps every number reserved and names the IDs still reserved and the
`record delete --id` call that finishes it. After pulling a deletion on another
machine, the agent runs `check` before creating plans and removes the worktrees
it names for deleted plans, or for an earlier plan that held the same number.

The worktree skill no longer passes `--reuse` by default. Preparing again for a
plan resumes its own worktree, and a worktree or branch left from an earlier plan
that held the same number is refused rather than resumed, so the agent inspects
it before choosing `--reuse`. A worktree prepared before 2.4.0 that holds no
commits is resumed only when it is clean and at the tip of the base branch.

A member may record free-text roles, one line each, such as maintainer or docs. The agent asks
for them when adding a member, and the question may be skipped. Roles are shown
with the roster and authorize nothing.

`worktree prune` clears the associations of worktrees Git no longer lists and
reports them as dropped, and a plan branch checked out elsewhere is now
explained with the safe next steps. `check` now recommends `worktree prune`
rather than `worktree repair` when Git no longer lists a plan's worktree, and
keeps the repair advice for a checkout that only moved; the `record delete`
refusal for a plan's worktree names prune too.

`worktree remove` now also removes the empty `.worktrees/` folders it leaves
behind, so a plan's folder goes with its last worktree; anything a person put
there is left alone (kaotypr/context-circuit-source#161).

When a knowledge note changes, its catalog entry's search terms are rewritten
against what the note now says, not only added to. The workspace catalog,
`context/INDEX.md`, no longer claims to be small enough to read in full: it is
searched for the entries a task needs.

Thanks to @hallode for `worktree prune` (#140).

## v2.3.0 — 2026-09-27


# v2.3.0

The quick-start command now uses the published template's own version tag. The
release assembly substitutes the version while publishing the guide, preventing
future releases from retaining an older tag.

A workspace can now be solo, with no roster at all. In a workspace that lists
members, the agent declines changing requests from a person this machine has not
identified, asks whether they are a listed member or someone new, adds and
selects them, and then carries on with what they asked. Reading and questions
still work without it, and the answer is taken as given.

Records can be deleted on request: one member's intents and plans in a team
workspace, or every record in a solo one, releasing their IDs for reuse. The
agent shows a preview of exactly what goes and deletes only after the person
agrees to that list. This workspace version pins CLI 2.3.0, which provides both.

## v2.2.0 — 2026-09-23


# v2.2.0

Developers can request a grounded plan directly, without creating an intent.
Every new plan uses a folder with `plan.md` as its entry and optional detail
files for contracts, diagrams, schema, UI structure, or other useful design.
The entry links every detail and assigns shared or repository-specific reading.
After the complete plan is presented, a separate execution request starts work.
The workspace pins CLI 2.2.0, which understands these folders and continues to
read legacy single-file plans.

## v2.1.0 — 2026-09-20


# v2.1.0

A workspace can now install its CLI from the registry it was published from,
rather than only from the public repository.

## The cc-cli skill reads the workspace's registry

`workspace.yaml` accepts an optional `cli_registry` naming the `source`,
`repository` and `api` this workspace installs from. The cc-cli skill reads it
before installing and passes those values to the installer; a workspace without
the key installs from the public GitHub release exactly as before.

The skill no longer describes the product repository as the only place a CLI can
come from, and it is explicit that a registry must be read rather than guessed —
a wrong host is not a failed install but a request for a binary sent somewhere
nobody chose.

## Installers accept a registry

The bundled `install.sh` and `install.ps1` accept `--source github|gitlab` with
`--repo` and `--api` (`-Source`, `-Repo`, `-Api`), or the environment variables
`CONTEXT_CIRCUIT_SOURCE`, `CONTEXT_CIRCUIT_REPO` and `CONTEXT_CIRCUIT_API`. A
`gitlab` registry resolves the release through the GitLab API, downloads each
asset from the generic package registry, and requires an access token, because
no anonymous download path exists to fall back to.

Checksum verification, the version check against the downloaded binary, side-by-
side version storage and offline `--archive` installation are unchanged and
apply to every registry.

## v2.0.0 — 2026-09-19


# Context Circuit 2.0.0

Context Circuit gives coding agents shared project context, a grounded plan, and
explicit human control points across one or more Git repositories.

v2 is a rewrite. A Go executable — new in this version, replacing v1's shell
runtime — maintains workspace records, repository bindings, and Git working
copies; the coding agent owns understanding, planning, implementation, and
delivery. The line between them is the product: anything whose answer depends on
reading the actual project belongs to the agent, and anything that must return
the same result every time belongs to the binary, which holds no model
credentials and never calls a model API.

**v2 does not migrate a v1 workspace.** Initialization is for fresh workspaces.
An existing v1 workspace keeps working with v1 and is never rewritten in place.

## Installing

Create a workspace from this repository with
[tembiter](https://github.com/kaotypr/tembiter), then ask the workspace's
`cc-cli` skill to install the executable: it selects the platform, verifies the
download, and installs without administrator access. Native binaries cover
macOS, Linux, and Windows on amd64 and arm64 — Windows does not need WSL. Git is
required; no Go or Python runtime is.

This template expects CLI `2.0.0`, which is that binary's first release: it
starts at 2.0.0 because it was introduced with this template, not because a 1.x
preceded it. The two version lines are independent from here and will diverge. A
workspace pins the version it expects in `.context-circuit/CLI_VERSION`, and
installed versions coexist side by side.

## What a workspace is

A workspace is an ordinary Git repository holding records, knowledge, and
bindings to the repositories the work actually happens in. It receives its own
front page, written at initialization for the project it belongs to and owned by
that project from then on — not a README about Context Circuit, and no license
file, because the template's 0BSD terms ask nothing of the repository they are
copied into.

The agent-facing surface is an always-loaded entry instruction carrying only
what must always be in force, and thirteen skills carrying the procedure behind
each stage, read when that stage comes due. Reference documentation is loaded on
demand rather than held in context.

## How work moves

An implementation change makes its outcome explicit before any detailed code
investigation. The agent retrieves the project's existing knowledge, reads the
request against it, and writes an intent: goal, non-goals, constraints,
observable success criteria, repository scope, and numbered open questions a
person answers by number. It then stops.

Approval is a real conversational decision by a person, recorded with their own
words beside the instant. No command, field, or agent can supply it, and an
intent is not approved because a field says so. Approving authorizes planning
and planning alone.

The agent then inspects real code and writes plans, prepares isolated worktrees,
implements, and — only when asked — reviews, delivers, and completes.
Completion reconciles what the work changed back into the project's durable
knowledge. Commit, push, pull request, merge, deployment, and deletion each
require explicit authorization at the moment they happen.

A person may bypass this. A request to change something directly, because it is
small or because its outcome is already exactly what they said, is carried out
in a bound checkout with no intent and no plan — and the durable knowledge it
alters is reconciled the same way.

## Knowledge

Durable project knowledge lives in `context/` as notes with a shape the
diagnostic enforces: one unwrapped catalog entry per note carrying its link, the
repositories it applies to, the question it answers, its search terms, and the
date it was last confirmed. A note anchors to repository paths collected in one
block at its end, and records who the project serves as well as what it does.

A note's reviewed date is read against the code it anchors to rather than
against the calendar, so knowledge that has gone stale is surfaced rather than
merely undated. `check` reports what is wrong and names what discharges it.

A workspace may also mount a knowledge repository it does not own — an
organization's knowledge center, shared by many workspaces. It is read-only
here, retrieved alongside this workspace's own notes with each match marked for
which side it came from, and never reconciled locally. Sync fetches, then
fast-forwards only when that is safe.

## Designing before building

New in 2.0.0: `cc-system-design` is available to a workspace. Where a change is
large enough that its shape should be settled and read before it is built, the
agent authors that design as source material under `sources/system-design/` —
a three-tier layout with an index, a normative overview a reviewer can stop at,
and detail files split by concern rather than by repository.

A design is passive evidence, not a stage. It carries no status and no gate, and
drafting one approves nothing. An intent may name a design path where its
outcome genuinely rests on one, and most intents name nothing at all.

## Working in parallel

Preparing a worktree clones the bound checkout's ignored `node_modules` and
`.env` files through the filesystem's own cloning where available, with an
independent-copy fallback, so a new worktree rarely needs a reinstall.

`record order` derives dependency waves, each plan's starting reference, the
integration merges a dependent plan needs, and which plans in a wave share a
repository. It recommends overlapping plans or chaining them and reports the
cost of each. Completing a plan releases its dependents; an unfinished one holds
them. Preparing a dependent plan refuses rather than silently branching from a
base that does not contain what it depends on.

Explorer, planner, worker, and an explicitly requested reviewer are subagent
roles with per-host model and effort settings, dispatched through the host's own
tools.

## Repositories and members

Connect an existing checkout, clone a repository, or initialize a new one. The
Git repository carrying the workspace itself can be one of them. A base branch
belongs to the machine that checked it out; the shared remote and default branch
are recorded once for everyone.

Records are written in the language recorded for their author, with the record's
structure staying English whatever its prose is, and a member may record the
register their team writes in. An approval or a completion note keeps exactly
what the person said, never a translation of it. Knowledge stays English.

Allocation bands are optional rather than structural: a solo workspace counts
from `i001` and `p0001`, and a team assigns each member a band so separate
clones allocate without colliding.

## What v1 had and v2 does not

The v1 trust machinery is retired rather than reimplemented. There are no
consequence tiers, candidate digests, path leases, execution or verification
records, automatic repair loops, or external publication. Independent review is
a manually requested read-only inspection that never starts on its own and never
blocks a pull request, delivery, or completion. `check` is a diagnostic, not a
gate.

## Licensing

Everything a workspace receives is 0BSD — terms that ask nothing at all of the
repository the scaffolding is copied into. The CLI is a separate product under
Apache-2.0, whose explicit patent grant is what carries a binary through a legal
review. The 0BSD text is at this repository's root and reaches no workspace.

## Upgrading from a candidate

Records written by `2.0.0-rc.4` and later are read unchanged, and 2.0.0 changes
no record format. A workspace keeps every file it already has; nothing is
removed from a workspace on upgrade.

A workspace created before `2.0.0-rc.14` has this repository's landing page at
its root — a `README.md` about Context Circuit, a `LICENSE`, a `CHANGELOG.md`,
conduct, contributing and security pages, and `assets/`. Those belong to this
repository, not to that project. Delete them and write the project's own front
page; a template update will not write them back, and will not touch a `README`
that is already there.

## Status

Deterministic tests cover file and Git behavior, the documented command surface,
the knowledge notes this repository keeps about its own product, and the
installer natively on Linux, macOS, and Windows. Whether a particular coding
host loads and follows the shared instruction is not established by those tests.
Exercise the flows you rely on.

