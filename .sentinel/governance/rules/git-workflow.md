# Git Workflow Standard

## Release tagging standing rule (ratified 2026-08-25)

**A version bump is not shipped until it's tagged.** Bumping `pyproject.toml` (or the
equivalent per-repo version file) and merging the PR is necessary but not sufficient — GitHub's
repo page, `gh release list`, and anyone cloning the repo cold only reflect the current release
from a real git tag / GitHub Release. A merged PR with no tag is invisible to all of that,
which is exactly the gap JP caught on 2026-08-25: four version bumps (`sentinel-memory-os`
0.5.8→0.5.11) shipped across four merged PRs with zero tags created, so `gh release list`
still reported `v0.5.7` as latest days after four newer versions had landed on `main`.

Applies to every repo with a version-bump convention (check `docs/development/release.md` or
equivalent for the repo's own documented release steps — this rule restates step "tagged and
publication evidence recorded" as a hard requirement, not an optional last step):
- After merging a PR that bumps the version, **tag `main` at that commit and create a GitHub
  Release in the same session**, before considering the work done. Don't defer it to "the next
  release will catch it up" — each shipped version gets its own tag.
- `git tag -a v<version> -m "..." <commit>` then `git push origin v<version>`, then
  `gh release create v<version> --notes-file <path>` (or `--notes` inline for something short).
  Release notes should summarize what changed — pull from the same changelog entry the version
  bump already wrote, don't write a second, different summary from memory.
- If several version bumps land back-to-back in one session before anyone checks
  `gh release list`, it's fine to catch up with one release marking the cumulative current
  state (say so in the notes) rather than fabricating separate historical tags after the fact —
  but from that point forward, tag on every bump, not retroactively.
- Verify with `gh release list` (latest matches the version just bumped) before reporting a
  release/version-bump task complete.

## Clean-state standing rule (ratified 2026-08-25)

**No work is ever left uncommitted, unshipped, or lying around.** Every git repo an agent
touches — except the Obsidian vault (it is iCloud-synced and self-controlled, not a
PR-workflow repo; forcing this rule onto it risks sync conflicts, not code) — is governed
by this rule, now and for every future repo unless explicitly exempted the way the vault is:

- **Uncommitted work does not survive a session.** Before a session ends or hands off, its
  changes are committed on a branch — never left dangling in a working tree. If work is
  genuinely unfinished, commit what exists with a clear message; do not leave silent diffs.
- **Every change to a repo goes through a PR, gets merged (or explicitly closed with a
  stated reason), and is deployed/tested where applicable.** No direct pushes to `main`
  outside the existing push-authority rules below, and no branch sits open indefinitely
  as a substitute for finishing the work.
- **When work lands, its branch is deleted — remote and local — and any worktree used for
  it is removed** (`git worktree remove`, never a bare `rm -rf`). A merged PR with its
  source branch still present is an incomplete close-out, not a finished one.
- **Session/task end-state is always clean:** `git status` clean, no stray local branches
  beyond the one currently in flight, no local worktrees for finished work, no open PRs
  left without either a merge or a stated reason for staying open (e.g. genuinely blocked
  and escalated — never "awaiting JP's merge approval" for in-SOW work; see the auto-merge
  ruling under Push / PR). A repo ruleset or CODEOWNERS rule that requires JP's review
  (sentinel since 2026-10-19, CHG-2026-10-18-005) is a gate JP chose, not an escalation: such a
  PR is reported as "awaiting the ruleset's code-owner review" and merged as soon as it lands
  (see the auto-merge ruling's limits). This is not a one-time cleanup — it is the default
  condition a repo should be in after every session that touches it.
- **This does not relax §5 (money/legal/member-rights) or the push-authority rules below.**
  A PR still needs verified-green gates before merge; "keep it clean" is never grounds to
  merge something that hasn't actually passed.

Applies retroactively when an agent discovers an existing repo in a dirty state (stray
branches, orphaned worktrees, unmerged-but-abandoned PRs) — treat that as debt to clear,
not as history to leave alone. See the branch-sweep pattern below for how to do this
safely without losing work that was implemented but never shipped.

## Parallel work — worktree isolation (canonical, ratified 2026-07-30)

Every parallel CODE session gets its own git worktree — a separate physical directory
sharing one repo history. Sessions cannot see each other's uncommitted work; the
shared-working-tree collision class is eliminated structurally, not by discipline.

- **The standard is the outcome, and there is only one (JP, 2026-10-07):** "no work is ever
  lost, or left un committed. thats the standard so if there is a better system which ensures
  that end result and its adheared to universally thats whats acceptable. what I dont care for
  is having to seperate standards, this isnt acceptable." Where a worktree lives is not the
  rule. Every worktree gets the same controls, with no exceptions and no second standard,
  wherever it lives and whoever created it (a person, an agent, the Claude Desktop app, or a
  Claude Code subagent):
  1. it is claimed in the worktree-lock registry as soon as its path is known (lock section
     below). The session that creates it claims it. A Claude Code subagent's isolated worktree is
     claimed by the subagent as its first action; failing that, the spawning session claims it
     when the subagent returns. Either way it counts as part of the spawning session's work;
  2. it is found and checked by the daily lock report and the open-work report;
  3. it is covered by the worktree and closeout guards;
  4. its work is committed and lands on the repo's default branch: through a PR where the repo
     has a remote, and by the local-commit path in `canonical-paths.md` (JP, 2026-09-07) where it
     has none (Distyll, vigilum);
  5. once landed, it is removed along with its branch.

  **Where worktrees are created.** A worktree made by hand, by an agent or by a script defaults
  to `~/dev/code/qb/worktrees/<branch-slug>/`. The Claude Desktop app and Claude Code subagents
  create theirs under `<repo>/.claude/worktrees/<name>/`. The one registry of worktree roots is
  [`worktree-roots.txt`](https://github.com/FinTechGlobalSolutions/sentinel/blob/main/governance/rules/worktree-roots.txt), which lists every root named here; this file keeps
  no second list. Creating a worktree under a root that is not in that registry breaks control 2.
  Either create it under a registered root, or register the new root in `worktree-roots.txt`
  (a sentinel PR) before or together with its first use.

  **How control 2 finds worktrees (since #118, [issue](https://github.com/FinTechGlobalSolutions/sentinel/issues/118)).**
  Both reports ask git, not a directory list: `git worktree list --porcelain` for every repo
  `bin/gov-repos.sh` names (the set govsync governs) and every repo in `open-work-roots.txt`
  (`bin/worktree_discover.py`). A worktree created in a fresh directory is covered with no roots
  file edited, and each one is classified: unclaimed, dirty, unpushed or unmerged, prunable.
  `worktree-roots.txt` is now only the net for directories git does NOT know (a subagent's
  `agent-*` leftover, a worktree whose metadata was pruned): the lock report calls those
  `ORPHAN-DIR`, "possible orphaned work, not a worktree: inspect and salvage", and gives them no
  claim-and-remove remedy. A checkout of a repo git knows, at a path git does not list, is
  `LISTED-ELSEWHERE`: a moved worktree (repair it from there; its old path's `PRUNABLE` says "moved",
  never "prune") or a copy (run no git in it; salvage). Directories are compared by device and inode,
  so a path that differs only in case is the same directory. Still open:
  - The closeout guard is no general backstop. It checks only the checkout the closing session
    is standing in, and only for repos with a remote. It does not see a subagent's worktree, any
    other worktree, or Distyll and vigilum. The discovery is now a reusable helper
    (`worktree_discover.py claims <session-id>` lists the worktrees a session holds locks on);
    wiring it into `bin/hook-closeout-guard.py` is follow-up.
  - A claim made without `--session-id` takes `CLAUDE_CODE_SESSION_ID` from the environment; a
    shell with neither still records an ownerless lock.

  Tooling must resolve a destructive command's target from its explicit operands, so a worktree
  nested inside its primary checkout is never mistaken for that checkout. A command that sweeps
  a whole primary checkout (`git clean -ffdx`, or deleting the checkout) also reaches every
  worktree nested under its `.claude/worktrees/`, so never run one in a checkout that holds
  worktrees. This replaces the 2026-07-30 wording "sibling to the main checkout, never nested
  inside it". JP ruled on 2026-10-07 that the location was never the rule: the outcome is. Ceres
  decision d9012959-b9f2-468e-b250-ad1ef314b683 (a Ceres record, with no web link).
- **Session-identifying naming convention (canonical, ratified 2026-08-16):**
  `<slug>` starts with `<AGENT-CODE><MMDDYY>-<short-description>`. The corresponding
  worktree branch name should be `wt/<slug>`. This is how a second session — human or
  agent — looking at `git worktree list` or the branch list can tell WHO is doing WHAT
  and WHEN without opening a single file. Format:
  - `AGENT-CODE` — a short, stable code per tool/agent identity. Registered so far:
    `CC` = Claude Code. Extend the table below rather than inventing a fresh scheme
    per session — `CX` (Codex/ChatGPT), `CP` (GitHub Copilot agent), `GM` (Gemini),
    `HM` (JP working directly, no agent) are reserved slots; add the real code here
    the first time one of those tools actually needs a worktree, not speculatively.
  - `MMDDYY` — the date the worktree was created, so a stale-looking worktree's age is
    visible at a glance without `git log`.
  - `<short-description>` — kebab-case, matches the existing slug convention
    (`quaestor-01-webhooks`, `restituo-04-onboard-c42`, etc.) — campaign/slice-scoped
    naming stays exactly as it was; the agent-code+date prefix is additive, not a
    replacement for meaningful names.
  - Example: `wt/CC081626-quaestor-01-webhooks` (Claude Code, 2026-08-16, QUAESTOR
    Wave 0 webhook work). A worktree/branch with no recognizable prefix predates this
    convention — do not treat its absence as a violation, just as a signal to migrate
    the *next* worktree touching that area to the new form.
  - This is a naming convention, not a lock — it doesn't by itself prevent two
    sessions from touching the same files. It exists so the collision, if one happens
    anyway, is diagnosable in seconds instead of requiring reflog archaeology (see the
    2026-08-16 QUAESTOR/RESTITUO incident this rule and the pre-commit guard above
    were both written in response to).
- **Create:** `git worktree add ~/dev/code/qb/worktrees/<slug> <branch>`, then
  `eval "$(fnm env)" && fnm use 24` inside the new worktree before any install/build/test/
  push. The Node pin is load-bearing (Node 26 breaks `better-sqlite3`) and does not
  travel with `git worktree add` — a worktree created without this step silently runs
  gate checks (typecheck, pre-push) on whatever Node the shell happens to have, which can
  pass green on the wrong runtime and mask a pin-specific failure until CI. A worktree the
  Claude Desktop app created for a session (`<repo>/.claude/worktrees/<name>/`) skips the
  `git worktree add` step, but nothing else: the session claims its lock as its first action
  and then follows every other step here. The same holds for a Claude Code subagent's isolated
  worktree: the subagent claims it as its first action, or failing that the spawning session
  claims it when the subagent returns (control 1 above).
- One worktree per active session. A session works ONLY in its own worktree. A subagent's isolated
  worktree does not count toward its parent session's one-worktree limit, though its work is still
  the parent's to land (control 1 above).
- **Remove when merged:** `git worktree remove <path>` — never `rm -rf` a worktree
  directory (leaves stale metadata in `.git/worktrees/`).
- **The shared main checkout is PULL-ONLY. HARD RULE, now MECHANICALLY ENFORCED
  (2026-08-16).** No session — including a Strategist/Orchestrator doing integration —
  commits, stages, or builds in the shared main checkout (`~/dev/code/qb/quorumbooks`)
  under any circumstance. It exists solely to `git pull`/`fetch`, run `git worktree
  add/remove`, and read state. Integration merges that used to happen here now happen
  in a dedicated worktree and land via a GitHub PR — GitHub itself requires a PR on
  `main` via an org-level ruleset (confirmed live 2026-08-16, not just a local-hook
  convention), so there is no longer any legitimate reason for a direct commit in the
  primary checkout, not even for the person merging.
  **Enforced by `scripts/git-hooks/pre-commit`** (tracked, installed via
  `scripts/install-hooks.sh`, shared across every worktree from the one common hooks
  directory): it detects the primary checkout by its real `.git` DIRECTORY (a linked
  worktree's `.git` is a FILE pointing at `gitdir:`) and blocks any commit attempted
  there outright. Emergency bypass is git's own `--no-verify` — use sparingly; if
  you're reaching for it here the real fix is almost always "make a worktree instead."
  **Why this had to become mechanical, not just documented:** on 2026-08-16, two
  concurrently-running campaigns (OPERATION QUAESTOR and OPERATION RESTITUO) both
  merged directly into this exact shared checkout, interleaved in the reflog, and one
  session's `git checkout` silently switched the other session's branch out from under
  it mid-task — real duplicate work and a real collision, not a theoretical risk. A
  session that finds itself with uncommitted work in the shared main tree has already
  violated this rule — the correct recovery is to move that work to a branch in the
  session's own worktree, never to commit it from main.
- **Migration is order-dependent:** never relocate or disrupt a checkout that has
  uncommitted work in it. Drain the shared tree to clean (each in-flight change on its
  own branch, committed via explicit-path staging) before creating worktrees for
  subsequent sessions. `git status` must show clean before migrating.
- If a checkout has uncommitted work that cannot be attributed to a known in-flight
  session, STOP and surface it — do not commit, stash, or move it on an assumption.

**Coverage extended beyond quorumbooks (ratified 2026-09-07).** The mechanical guard above
was quorumbooks-only until a live collision proved the gap: on 2026-09-07, at least four
concurrent Claude Code sessions were found editing `~/dev/code/memory-os-installer`'s primary
checkout directly and simultaneously (no worktrees), coordinated by a background multi-session
campaign. One session's uncommitted work was silently overwritten mid-edit by another session's
commit — no data was actually lost only because the two sessions had independently converged on
equivalent fixes; this was luck, not protection. The same `scripts/git-hooks/pre-commit` /
`scripts/install-hooks.sh` pattern (identical mechanism, each repo's own canonical path
hardcoded per the PR #110 scoping rationale above) is now also live in:

| repo | canonical primary checkout | remote |
|---|---|---|
| `sentinel` | `~/dev/sentinel` | yes — this is the law repo itself |
| `memory-os-installer` | `~/dev/code/memory-os-installer` | yes — proven live collision, 2026-09-07 |
| `quorumbooks-app` | `~/dev/code/qb/quorumbooks-app` | yes |
| `quorumbooks-web` | `~/dev/code/qb/quorumbooks-web` | yes |
| `quorumbooks-cockpit` | `~/dev/code/qb/quorumbooks-cockpit` | yes |
| `Distyll` | `~/dev/code/Distyll` | no — local commit is the terminal state (canonical-paths.md) |
| `vigilum` | `~/dev/code/vigilum` | no — local commit is the terminal state (canonical-paths.md) |

Each repo's hook and installer are tracked in that repo's own `scripts/git-hooks/` and
`scripts/install-hooks.sh` (not centralized here — mirrors quorumbooks' existing, already-proven
pattern; the anti-sprawl rule in AGENTS.md §0 governs *policy/law text*, not per-repo
implementation tooling that already varies by repo convention). A newly-registered governed
repo with a canonical primary checkout should get the same treatment — generate its hook from
this same template, install live, seed-test both the blocked and allowed paths, then land the
tracked files via a worktree PR (or a local merge for a no-remote repo) — same as every other
repo in the table above.

**Interim rule until every session is migrated to a worktree:** explicit-path staging
remains mandatory on every commit — stage owned paths only, verify `git status` before
committing, never `git add .` / `-A` / `-a`. This is what prevents collisions until
worktrees make them structurally impossible.

## Worktree lock — claim before work, release at close-out (directed by JP 2026-08-26)

Worktree isolation stopped sessions from seeing each other's uncommitted work, but nothing
stopped one agent from **deleting another agent's worktree mid-flight** — which happened
repeatedly (a tree removed while its owner was well past halfway). This rule closes that gap
with a lock registry plus mechanical enforcement, built same-day on the gate-receipt/custos
pattern.

**The rule (binds every agent, every vendor, per AGENTS.md §0):**

1. **Claim on create.** Immediately after `git worktree add`, register ownership:
   ```
   bash ~/dev/sentinel/bin/worktree-lock.sh claim --worktree <path> \
     --session-id <id> --agent <CODE> --branch wt/<slug> --effort "one-line description"
   ```
   The claim records WHO is working on WHAT and since WHEN — the machine-readable twin of
   the `<AGENT-CODE><MMDDYY>-` naming convention above.
2. **Never touch a locked worktree you don't own.** No `git worktree remove`, no
   `git branch -D` of its branch, no `rm -rf`, no committing into it, no `git checkout`
   switching its branch. A live lock means an agent is mid-flight there. This binds even
   when the tree *looks* abandoned — looking abandoned is not evidence (§9e).
3. **Release at close-out.** When the branch has merged and the worktree is being removed
   (`git worktree remove`, per the clean-state rule), run
   `worktree-lock.sh release --worktree <path>` in the same breath.
4. **Override only by attributed takeover.** If a lock's session is genuinely dead
   (crashed, archived) run
   `worktree-lock.sh takeover --slug <slug> --session-id <your-session-id> --owner <name> --reason "..."`
   — it appends a named, reasoned ledger entry. `--session-id` is what makes the guard
   recognise the new owner (the guard's block message prints the blocked session's id in
   its suggested command). Omit it only when the takeover is immediately followed by
   `release` (orphan sweep): a session-less takeover records an ownerless lock that the
   guard enforces against **every** session, the taker included, and that `claim` cannot
   repair. There is deliberately no `--force` and the hook honors no
   bypass token: deleting or editing a lock file to get past the guard is the exact
   theater AGENTS.md §5 condemns, and the daily report flags it as tampering.

**Mechanics (two layers — neither alone is the control):**
- Registry: `~/dev/sentinel/logs/worktree-locks/` — `latest-<slug>.json` per worktree plus
  an append-only `locks.jsonl` ledger (gate-receipt two-file pattern; the ledger makes
  out-of-band lock deletion detectable).
- In-the-moment: `bin/hook-worktree-guard.sh` (+ `.py`), a Claude Code `PreToolUse` Bash
  hook wired machine-wide via `~/.claude/settings.json` and in the sentinel repo. Blocks
  (exit 2) destructive commands — `git worktree remove`, `git branch -d/-D`, recursive
  `rm` — whose target resolves to a worktree locked by a *different* session. The owner is
  never blocked; unlocked trees are never blocked; unresolvable `rm` targets warn-don't-block
  (a false-positiving guard gets unwired, which is worse than the gap). Fails open.
- Daily audit: `bin/worktree-lock-report.sh` (LaunchAgent
  `com.sentinel.worktree-lock-report`, 07:17) scans the roots in
  `governance/rules/worktree-roots.txt` and flags **LOCK-ORPHAN** (live lock, tree gone =
  bypassed deletion), **UNREGISTERED** (tree with no claim), and **LEDGER-MISMATCH** (lock
  file deleted/edited out-of-band). Report: `logs/worktree-locks/report-<date>.md`.

**Cross-machine marker, and the one push that skips the pre-push hook (CHG-2026-09-28-009).
Sanctioned by: JP, 2026-09-29 — live in chat: "approve 83", after the bypass was put to him
in plain terms (it is confined to `refs/wip-locks/<branch>`, the marker carries no file of the
project, no hook is edited or disabled, and no other push is affected).**
- The registry above is local to one machine. `claim` therefore also pushes a marker ref,
  `refs/wip-locks/<branch>`, to the worktree's `origin`; `release` deletes it; a claim is refused
  when origin already has that ref. No marker goes to a remote JP does not own (Push / PR below).
- **The marker is an empty commit: empty tree, no parent.** It carries no file of the branch. Until
  2026-09-28 the tool pushed `HEAD` there, that is the branch's own commits, while its header called
  the ref "content-free".
- **`worktree-lock.sh` pushes and deletes that one ref with `git push --no-verify`. The repository's
  pre-push hook does not run for it.** This is a bypass and is written down as one. It covers the
  tool's two commands and nothing else: every other push, by any agent or script, runs the hook, and
  `--no-verify` on those stays what each hook's own header says it is — an emergency bypass, never a
  habit. The hook is not edited, disabled or reconfigured.
- **Why:** a pre-push hook gates code leaving the machine; the marker moves no code. quorumbooks'
  hook runs 20 gates and takes longer than the tool waits, and in a fresh worktree (dependencies not
  installed) it cannot pass at all — and "claim on create" means a fresh worktree. On 2026-09-28 two
  claims there died with an uncaught `subprocess.TimeoutExpired` and recorded nothing; the same
  failure is on record for `release` on 2026-09-02.
- **A machine knows its own marker.** `logs/worktree-locks/marker-<slug>.json` records the SHA of
  the marker this machine pushed. `claim` adopts, and `release` deletes, only a ref that still
  points at that SHA. The record holds one entry per branch the worktree has claimed; an entry is
  written just before the push and removed only when that marker is known to be gone; `release`
  removes every marker on record for the worktree. A marker with no entry is deleted by name in
  one case only: it points at a commit of this repository that has files in it, which is what
  the tool pushed (the branch `HEAD`) before 2026-09-28. Nothing else is touched
  (CHG-2026-09-29-003).
- **A claim is all or nothing.** Order: marker, Ceres record, local registry. If a later step fails
  the marker is taken back; if it cannot be taken back, re-running the same claim adopts it.
  `release` and `takeover` change nothing, locally or on origin, unless the Ceres record was
  accepted: `release` deletes the marker last. Waits are bounded: `WORKTREE_LOCK_GIT_TIMEOUT` and
  `WORKTREE_LOCK_CERES_TIMEOUT` (seconds per call, default 30), `WORKTREE_LOCK_TOTAL_TIMEOUT`
  (seconds for the whole run, default 90).
- **Limits:** a marker push typed by hand runs the hook like any other push. A `release` whose
  delete fails still releases locally and leaves a stale marker: the next claim of that worktree
  from the same machine adopts it, every other claim of that branch name is refused until it is
  removed; running the same release again retries. A lock claimed before 2026-09-28 has nothing
  on record, and a re-claim by its owner is refused by its own marker until the lock is released
  and claimed again. The deletion by name applies to any lock with nothing on record for its
  branch, and it cannot tell whose old-style marker it is: one pushed by another machine that
  still runs the tool as it was before 2026-09-28 is deleted too, when its commit is in this
  repository. An old-style marker whose commit is no longer in this repository, and a marker
  whose record was lost, are left on origin. No marker is used when `origin` does not fetch from
  and push to one and the same repository; a claim or release made in that state leaves the
  record as it is. The check that a marker is this machine's and its deletion are two calls, not
  one. A release made after `git worktree remove` reaches origin through the repository named
  in the record; a lock with nothing on record, or whose record was written before
  CHG-2026-09-29-003 and names no repository, cannot: release before removing the worktree. The
  record is kept per worktree name: two worktrees of the same name in different repositories
  share one.

**Honest limits, stated so no one mistakes this for a wall:** the hook binds Claude Code
sessions only — Codex, Aider, or a bare shell are bound *normatively* by this rule and
caught only by the daily report after the fact. A lock is a tripwire plus an audit trail,
not a filesystem permission. Subagents spawned by a lock-owning session may carry different
session ids and can be blocked from their parent's tree — route destructive worktree
operations through the owning parent session, which is good hygiene anyway.

**Build-time evidence (2026-08-26):** all guard paths proven by seeded test (foreign
`worktree remove`/`rm -rf`/`branch -D` blocked exit 2; owner and unrelated commands allowed
exit 0; foreign `release` refused; `takeover` attributed), and the report's first live run
found real drift — both then-existing QB worktrees (`CC082226-quorumbooks-app-pd-pointer`,
`CC082526-qb10-ws6-templates`) running unregistered.

## Worktree gate — a worktree exists for a work item (P4 slice S4, CHG-2026-10-19-007)

Design: Ceres P4 (durable work items), chunk 4. A worktree may exist for a Ceres work item only when
`ceres work check-worktree <item> --session-id <Ceres session>` allows it: the item is assigned or
in_progress, it is a code item, it has no hold, the claimant's agent is its assignee, and in quorumbooks its
`jira:QB-<n>` ref is attached. Ceres decides; sentinel asks (`bin/worktree_gate.py`).

1. **Claim with the item.** `worktree-lock.sh claim ... --work-item <item id> --ceres-session <your
   preflighted Ceres session id> [--work-namespace <ns>]` runs the check and refuses the claim, recording
   nothing and pushing no marker, when Ceres says no; the failed condition is printed. `--ceres-session` is the
   `agent_preflight` session, not `--session-id`: that stays the Claude Code hook id the guard matches on
   (`CERES_SESSION_ID` in the environment stands in for the flag). The lock file (`latest-<slug>.json`) and
   its ledger line then carry `work_item`, `work_namespace` and `work_item_verified`, and the Ceres queue record
   carries `work_item`.
2. **Phased rollout: `warn` by default.** Today no work items exist and no agent claims with one, so a
   claim or a `git worktree add` that names NO work item only warns. The mode is the first word of
   `governance/rules/worktree-gate-mode.txt` (`warn` | `require`). The file decides; the environment can only
   raise it to `require` (`WORKTREE_GATE_MODE=require`), and any other value there is ignored, so a session cannot
   switch the gate off. **To turn the refusal on, change that word to `require` in a reviewed commit.** The switch
   governs only a MISSING item: an item that is named and fails its check is refused in both modes.
3. **The create guard** (`bin/hook-worktree-guard.py`, the existing `Bash` hook): a `git worktree add`, and
   the `EnterWorktree` tool when it creates (a `name`, not a `path`), is read as a creation. No lock can exist
   for a path that does not exist yet, so the evidence of a work item is the command naming one
   (`WORKTREE_WORK_ITEM=<id> WORKTREE_CERES_SESSION=<id> git worktree add ...`, or those variables in the hook's
   environment), checked with `ceres work check-worktree`; or the calling session already holding a live lock that
   carries a `work_item`. With neither: `warn` prints a warning (hook JSON `systemMessage` plus
   `additionalContext`; no `permissionDecision`) and `require` blocks (exit 2). A named item that fails is blocked
   in both modes.
4. **Ceres unreachable fails open in `warn` mode, never silently.** No `ceres` binary, no answer in time
   (`WORKTREE_GATE_CHECK_TIMEOUT`, default 15 s, never less than 5; the hook uses at most 10), a database failure, or
   a CLI without the subcommand: under `warn` the claim or the add goes ahead UNCHECKED, prints a warning, and appends
   a `worktree.unverified` line to `logs/worktree-locks/events.jsonl` (path registered in `canonical-paths.md`).
   Under `require` a **claim** whose check is unverified is refused (the event is still written, marked
   `refused`), so a down Ceres, a missing binary or a short timeout is no way past it; the **create guard** still
   fails open there, as design chunk 4 has it. An answer from Ceres (exit 3, or exit 1 with a JSON error such as an
   unknown item) is never a fail-open.
5. **Backstop: `UNASSIGNED-WORKTREE`** in the daily lock report (`worktree-lock-report.sh`): a live lock with no
   `work_item`, or whose item (`ceres work show`, read-only) is not assigned/in_progress, is on hold, is not a code
   item or no longer exists. Under `warn` these are listed in an "Advisory" section, not counted and not notified
   (every lock claimed before the gate would fire it); under `require` they are findings. An item the report cannot
   read is a Warning line. In either mode the report also lists, as findings, every `worktree.unverified` and
   `worktree.guard_error` event of the last 24 hours (`GATE-UNVERIFIED`, `GATE-GUARD-ERROR`) and every live lock claimed
   with `work_item_verified: false` (`WORK-ITEM-UNVERIFIED`), and counts those locks in its header.

**Not wired by this change (settings are JP's).** To make Claude Code call the create guard for the
`EnterWorktree` tool, `~/.claude/settings.json` needs a second `PreToolUse` entry with `"matcher":
"EnterWorktree"` running `$HOME/dev/sentinel/bin/hook-worktree-guard.sh` (the Python already handles that tool
name). Whether the matcher fires for that tool name is UNVERIFIED.

**Agent isolation (`isolation: "worktree"`): documented as NOT going through `EnterWorktree`; the guard does not
see it.** Claude Code's hooks reference (`code.claude.com/docs/en/hooks`, read in full 2026-10-19) lists the cases
in which the `WorktreeCreate` event fires as separate bullets: `claude --worktree`; "Claude creates a worktree during
a session and switches into it with the `EnterWorktree` tool"; "A subagent uses `isolation: "worktree"`"; a workflow
agent; a background session; `remote-control` in `worktree` mode. It adds: "When Claude runs `git worktree add`
through the Bash tool rather than calling `EnterWorktree`, your WorktreeCreate hook doesn't run", and that
configuring a `WorktreeCreate` hook "replaces that default git behavior". So a subagent's isolated tree is made by
Claude Code itself, not by an `EnterWorktree` call or a `Bash` command, and neither `PreToolUse` matcher sees it.
UNVERIFIED by a live run (no agent was spawned, by instruction): this is the documentation's reading, and no local
transcript showed an `EnterWorktree` call or an `isolation` value to compare. `WorktreeCreate` is not a guard
point: wiring it makes the hook responsible for creating the tree. Only the claim (item 1) and the report
(item 5) cover a subagent's tree.

**Limits.**
- The Desktop app and subagents create their tree before a hook runs; Codex and Open WebUI are bound by this rule only.
- Agent names are self-declared at preflight, and Ceres compares the item's assignee with that name, so a cooperative
  agent can preflight as the item's assignee and pass the check
  ([sentinel-memory-os#433](https://github.com/FinTechGlobalSolutions/sentinel-memory-os/issues/433)).
- Once a session holds one live lock that carries any `work_item`, its later `git worktree add` calls pass the create
  guard without naming one.
- A claim with a made-up item id is refused by Ceres, but one with no item is only warned until `require` is on.
  `takeover` keeps the lock's `work_item` without re-checking it; a same-session re-claim with no `--work-item` keeps
  it too.
- `logs/worktree-locks/events.jsonl` is not rotated. Rotation of the shared logs is a follow-up. The report reads only
  the last 24 hours of it.
- Until a claim can be made before the tree exists, `require` makes every unannotated `git worktree add` a block.
- A guard that fails inside (a missing or broken `worktree_gate.py`) lets the creation through, but says so to the
  model and JP and writes a `worktree.guard_error` event; the report lists it.
- Flipping `worktree-gate-mode.txt` changes a rule file. Make the flip in a reviewed commit. Gate receipts
  (`gate-receipt.sh`, `hook-gate-guard.sh`) fingerprint only `governance/AGENTS.md` and `governance/rules/*.md`, so
  the `.txt` flip does not by itself void them (probed 2026-10-19); the edits to `git-workflow.md` and
  `canonical-paths.md` that introduce the gate do, and every session needs a fresh receipt after they land.

## Closeout guard — mechanical block on lazy/lossy closeout (ratified 2026-08-26)

Every governance control above this line about PR review and merge discipline was
enforceable only by an agent choosing to follow it — nothing stopped a session from calling
`ceres agent closeout` while sitting on unmerged/unpushed commits, or after marking a PR
review thread "resolved" with no reply (dismissed, not actually adopted/deferred/declined).
JP, 2026-08-26: "prevent lazy agents or lost work." This closes that gap mechanically, same
gate-receipt/custos pattern as everything else in this file.

**Mechanics:**
- `bin/hook-closeout-guard.sh` (+ `.py`) — a Claude Code `PreToolUse` hook wired machine-wide
  in `~/.claude/settings.json`, matcher `mcp__ceres__agent_closeout`. Before any session's
  closeout call reaches Ceres, it checks the git repo at the hook's `cwd`:
  1. **Merged and pushed** — the work at `HEAD` must have landed in `origin/<default-branch>`
     (`bin/landed.py`, shared with `bin/worktree-lock-report.sh`; sentinel #114): `HEAD` is an
     ancestor, **or** its tree equals the default branch's tree, **or** merging `HEAD` into the
     default branch changes nothing (`git merge-tree --write-tree`: every byte `HEAD` adds is
     already there), **or** a PR merged *into the default branch* has `headRefOid` equal to `HEAD`
     and its merge commit is in the default branch — a squash merge is never an ancestor, and this
     repo squash-merges. A PR merged into another branch (a stacked PR) proves nothing. If
     `landed.py` is missing, defective or raises, the ancestor-only test decides; an answer it
     cannot establish blocks, with a warning that says why. And every dirty path in the working tree that is **this session's** must be committed. The
     session's own uncommitted, unpushed or unmerged work blocks closeout; dirt it inherited
     does not (see *Dirty-path attribution* below).
  2. **PR thread disposition** — for PRs the closing session owns (see *Ownership* below),
     updated within the last 12h (generous window chosen to bias toward catching real misses
     over false confidence), every review thread must be resolved AND have a reply. A thread
     marked resolved with zero reply reads as dismissed/ignored, not dispositioned, and blocks.
  An OPEN PR in that window also blocks (not merged yet). A CLOSED-without-merge PR is
  reported informationally only — this control cannot mechanically verify "a stated reason
  was recorded," so it doesn't try to gate on it.
- **Ownership — the session's own work, not everyone's (2026-10-07, sentinel #88/#89/#103).**
  Candidate PRs are those authored by `@me` or (since QB-413) `app/tutanus`, but both
  identities are shared machine-wide, so author alone gated every session on every other
  session's open PRs, and read-only reviewers on the PRs they were reviewing. A candidate now
  counts only when its head branch is the session's, by its Claude Code session id or the
  closeout call's Ceres session id: a branch it **claimed** (a `claim` line in the
  [worktree-lock](#worktree-lock--claim-before-work-release-at-close-out-directed-by-jp-2026-08-26)
  ledger `locks.jsonl` — still its work once released), a branch whose lock it **holds** now (a
  takeover it kept to finish the work), or the cwd checkout's branch (unless detached, the
  default branch, or another session's live lock on that very checkout). A takeover released
  again — an orphan sweep — owns nothing. A PR on another session's claimed or held branch is
  named in one informational line; a PR on a branch **no** session claimed (govland's claims
  carry no session id; a session that never claimed) is a WARNING — it could be this
  session's. A session with no attributable branch at all is warned, not blocked — which is why
  claiming the worktree lock with `--session-id` matters: it is what keeps a session's own
  unfinished PR blocking.
- **Guest checkouts.** A checkout under another session's **live** lock (a reviewer in the
  author's worktree) has its check-1 findings reported, not blocking. The match is by worktree
  path only — the lock's worktree must be the cwd's git toplevel — never by branch name: a
  peer's lock on a same-named branch elsewhere waives nothing here. An ownerless or released
  lock does not make a guest. **The waiver stops at the guest's own dirty paths (2026-10-07,
  judge finding F6 on PR #105, closed by sentinel #110).** It exists because a guest cannot be
  held to a tree it did not dirty; it was never a licence to leave the guest's *own* work
  uncommitted in somebody else's worktree, which is exactly the lost work this check exists to
  prevent. **Attribution alone is not enough here, though** (2026-10-08 re-review): a path can be
  new since the snapshot because the **lock holder is working in that tree right now**, and
  blocking on that told the model to commit a peer's live work. So in a guest tree a path blocks
  only when the closing session's own transcript shows it wrote that path; everything else is a
  loud warning naming the paths, saying they are most likely the lock holder's and to leave them.
  With nothing attributable — no snapshot — the whole tree is waived exactly as before.
  **The authorship check is a lower bound, by design.** It reads the file-writing tools'
  arguments out of the session's transcript, so a file written by a shell command through `Bash`
  is invisible to it, and a subagent's writes land in its own `subagents/*.jsonl` rather than the
  parent transcript. Both fall to the warning, never to a block — in the one place where
  over-blocking would move somebody else's work, under-blocking is the right failure direction.
  The backstop is that nothing is actually lost: a file a guest leaves behind in the host's tree
  blocks **the lock holder's** own closeout, because there it is unattributable dirt in the
  holder's own checkout.
- **Read-only agents.** An `agent_type` of exactly `judge` or `scout` (set by Claude Code inside
  a subagent or an `--agent` session) blocks on nothing — it cannot commit, push or open a PR,
  and a subagent shares its parent's session id, so ownership would hand it the parent's PRs. A
  missing, empty or other `agent_type` exempts nothing. **Known risk:** a judge subagent runs
  under its parent's session id and can call `agent_closeout` with the parent's Ceres session
  id, closing out the *parent's* Ceres session while the parent's work is unfinished; the hook
  cannot tell that from a judge closing its own review. So the exemption is never silent: every
  finding it suppresses is appended, with the agent type and ids, to
  `~/dev/sentinel/logs/closeout-overrides/readonly-agent-exemptions.jsonl` (append-only, beside
  the override ledger), and if that line cannot be written the findings stand and the closeout
  blocks.
- **govsync output is not the session's work (2026-10-07, sentinel #103).** `bin/govsync`
  writes standing law into every governed repo's primary checkout and leaves it uncommitted for
  `govland` to land, so a session working there was blocked by a dirty tree it never touched.
  Check 1 now sets aside an uncommitted path that is govsync's own output, proven unedited —
  which paths govsync writes and what it writes there is read from `bin/govsync` itself, and
  each path must pass two tests. **Content:** `AGENTS.md` carries govsync's header, its body
  hashes to the header's `body-sha256`, and that `body-sha256` is the sha256 of the sentinel
  master `governance/AGENTS.md` at sentinel `HEAD` or at the header's commit (what govsync's own
  freshness test compares against — a body that merely hashes to its own header is not the
  law); each copied rule file is byte-identical to the sentinel source (now, or at the marker's
  `source-commit`); the `.generated-by-sentinel` marker agrees with `AGENTS.md`'s header and the
  master; the two relative symlinks, the `CLAUDE.md` loader and the generated `.aider.conf.yml`
  match exactly. **Over what HEAD held:** govsync refuses to overwrite hand-authored files, so
  wiping one to govsync's text is not govsync — `AGENTS.md` only over nothing, a symlink or a
  generated file; the bare `CLAUDE.md` loader only over nothing or a symlink to `AGENTS.md` (the
  prepend only onto a committed file not already loading it); `.aider.conf.yml` only over
  nothing or a generated one; the rules and marker only into a bundle that is absent or marked.
  Accepted paths are listed. Any other dirty path still blocks and is named; a hand-edited,
  header-stripped or forged generated file, a deletion, or a staged copy that differs from the
  working tree is not proven and blocks. If `bin/govsync` cannot be read, nothing is set aside.
- **What the agent sees.** A block (exit 2) shows the agent the block message and every note.
  On an allow, Claude Code sends a hook's stderr to its debug log only, so the hook prints a
  PreToolUse JSON object whose `hookSpecificOutput.additionalContext` carries every warning and
  informational line — that is how "warned" and "reported" above reach the agent — with no
  `permissionDecision` (an `"allow"` would skip the permission prompt). `hook-closeout-guard.sh`
  passes it through.
- **Known limits.** A session can escape check 1 on its own worktree by deliberately taking its
  own lock over under a different session id (`worktree-lock.sh takeover --session-id <other>`):
  the checkout then reads as a guest. The takeover is an attributed ledger entry (owner and
  reason), so it is visible after the fact, not prevented. Own work done inside a peer's locked
  worktree is likewise reported as the peer's; attributing a dirty tree path by path is the
  separate dirty-tree work (session `cc-20261007-closeout-guard-dirty-tree-attribution`).
- Fails OPEN (exit 0) on any internal error (`gh` unauthenticated, network failure, an uncaught
  exception, the guard process dying) with a warning — reported to the agent as above — this is
  a tripwire-plus-audit control, not a filesystem permission, same posture as the worktree-lock
  guard above. The SILENT allows are a cwd that is not a git repo and a repo with no origin:
  there is nothing to check. Every other skip tells the model the check did not run (sentinel
  #113 N3): an uncaught exception (caught in `main()`), hook input that is not JSON, a failed
  check-1 `git status` (an empty listing from a failed status is not a clean tree), and, in the
  wrapper, a missing guard file, a failed `mktemp` or a guard exit other than 0/2.
- Check 1 lists untracked files whatever the repo's `status.showUntrackedFiles` says (`-uall`);
  a session can set it to `no` on its own repo, so honouring it would hide that session's own
  files (#113 N4). The govsync-output proof accepts only law the sentinel checkout's `HEAD`
  holds (N1): a law commit counts only if it is an ancestor of `HEAD`, and a rule copy is compared
  with `HEAD:<source>`, not with the working tree, since govsync copies whatever the working tree
  holds. `HEAD` includes any unpushed local commit on that checkout. `gh repo view` runs in the
  judged checkout, and the wrapper's scratch file is `mktemp`'d.

**Dirty-path attribution — a session is blocked for its own dirt, not everyone's (2026-10-07,
sentinel #110).** The govsync-output rule above excuses one specific, very common kind of
inherited dirt by *proving its provenance*; this excuses the general case by *attributing it to a
session*. **The two compose, and neither widens the other:** a dirty path blocks only when it is
neither proven govsync output nor already dirty when this session started and byte-identical
since (a file over 50 MB, `MAX_HASH_BYTES`, is compared by size, mtime, device and inode instead of
content, so an in-place edit that preserves all four goes unseen). Provenance proof is the
stronger of the two where it applies — it needs no prior
observation, so it also covers sessions older than the snapshot hook and other vendors' sessions
— but it can only ever recognise govsync's own files. Everything else inherited is this rule's
job: the untracked `Instruct_BKUP/` directory a parent session created (the 2026-09-16 failure), a
peer session's scratch file, a half-finished edit nobody has claimed.

Check 1 used to require an absolutely clean working tree with
no notion of *who* dirtied it, which produced blocks no agent could satisfy by doing its job
correctly. Two live failures: a read-only reviewer subagent in `~/dev/code/the-archives` blocked
by an untracked `Instruct_BKUP/` directory its **parent** session had created (2026-09-16), and a
session whose own work was committed, pushed, PR'd and merged blocked by three govsync-regenerated
files that were **already uncommitted when it started** (2026-10-05/06). In both, the nearest fix
available to the blocked agent was to commit or stash files it does not own — which the
[worktree-isolation rule](#parallel-work--worktree-isolation-canonical-ratified-2026-07-30)
forbids outright ("if a checkout has uncommitted work that cannot be attributed to a known
in-flight session, STOP and surface it — do not commit, stash, or move it on an assumption"). Two
rules pointed opposite ways; this is the repair.

**Git records no author for an uncommitted change.** `git status --porcelain` says a path is
dirty, never who dirtied it. The only possible evidence is an observation made *before* the
session acted, so `bin/tree-snapshot.py` records one: the first time a Claude Code session is
seen in a git repo — at `SessionStart`, and **first sight wins** so a later run never overwrites
the earlier snapshot — every dirty path is written down with a digest of its working-tree content,
its porcelain status code, and its index blob when staged.

**A late first sight is refused, not trusted (2026-10-08, judge review of PR #111).** A snapshot
is only evidence of the tree *before* the session acted, so one taken afterwards is worse than
none: it shows the session's own edits as pre-existing and lets it close out on work it abandoned,
while the report tells it "do NOT commit, stash or move them". The engine therefore marks a
snapshot `pre_tool_use: false`, and the guard treats that exactly as no snapshot, whenever
the `SessionStart` source is anything other than `startup`, the transcript already contains a
`tool_use`, or the transcript cannot be read at all. **Trust is granted, not withheld by a
list** — an allow-list of known-late sources had already gone stale when `fork` shipped
(v2.1.214; a fork carries a *new* session id, so its parent's dirt read as inherited), so
`compact`, `resume`, `clear`, `fork`, an unknown value and a missing one all taint.
`bin/session-ledger.py` has keyed its own re-injection on the late sources since 2026-10-05, so
that `SessionStart` fires again mid-session is observed, not assumed.

**The guard re-asks the question at closeout**, against the transcript Claude Code hands *it*:
the snapshot's `taken_at` must precede the first `tool_use` recorded there. A transcript that is
absent or unreadable at closeout refuses the snapshot — leniency about a missing file belongs at
snapshot time, where it may not be written yet, not at closeout, where the session has certainly
acted. One exception: a transcript that exists and holds no `tool_use` at all is accepted, since
a session that ran no tool can have neither minted a forged snapshot (itself a tool call) nor
dirtied the tree. **This stops the naive forge only.** Claude Code supplies the path, but the
session can write the file it names; doing so is the same class of act as writing the snapshot
JSON by hand, which this design already concedes. Deliberate tampering is out of scope — a
tripwire with an audit trail, the posture `AGENTS.md` §9f states for every guard here. `UserPromptSubmit` was wired alongside `SessionStart` until the
same review and was removed: the only prompt at which it could take a valid snapshot is the first,
which `SessionStart` already covers, while every later prompt in a newly-entered repo produced
precisely the laundering snapshot above. Only a snapshot this script wrote **as the hook** counts;
the `write` subcommand tags its output `origin: "cli"` and the guard ignores it, so a blocked
session cannot unblock itself by running the tool the block message used to name.

**Honest coverage, narrower than it first looks:** a trustworthy snapshot exists only for the repo
the session's `cwd` pointed at when the session **started**. A worktree the session creates
mid-session is never covered, and every dirty path there blocks exactly as before.

**The common case is covered, measured not assumed.** `SessionStart` does fire in Desktop
app worktree sessions, well before the first tool call. Transcripts record it as an
**attachment** — `type: "attachment"`, `attachment.type: "hook_success"`,
`hookName: "SessionStart:startup"`, `hookEvent: "SessionStart"` — and *not* as the
`*_hook_summary` system record, which is why a first pass at this question wrongly concluded
the event was never logged. Measured 2026-10-09 by parsing every
`~/.claude/projects/*claude-worktrees*/*.jsonl`: of **33** transcripts, **20** carry exactly
`attachment` / `hook_success` / `hookName: "SessionStart:startup"`; **23** carry a SessionStart
attachment of any kind (the other 3 are `hook_non_blocking_error`, on 2026-09-18 / 2.1.267,
2026-09-24 / 2.1.280 and 2026-09-28 / 2.1.284); **10** carry none. Where it is logged it is
always **before** the first `tool_use` — by 3.7-290 s for the 20, or 3.5-290 s counting the error
records. (An earlier version of this text said 8-63 s; that does not reproduce — the `ceres-c*`
sessions alone give 11.4-63.3 s from 14 records, per the final review of #111 — and was a subset,
not the set. The figures drift as sessions are created, so they are stated with
the date and the method rather than as fixed truths.)

**Of the 10 with no record, 9 are one window on 2026-10-07 between 17:23 and 21:17 UTC**, all on
2.1.289 — a build that also appears among the transcripts that *do* carry it, so this is not a
version boundary. The tenth is an 11-line metadata-only stub (`92464979`, `CC091712-design-tracker-links`,
2026-09-19) of a session whose full transcript (`CC091812-mc-real-clerk`) carries the exact record, so
one session was counted twice. **Three sessions before that window lack the exact `hook_success`
record**, and naming them matters because an earlier version of this text wrongly claimed every
session outside the window had it: the three `hook_non_blocking_error` ones above (`45814527`,
`92d6a18b`, `890b6c29`), each of which did fire SessionStart, with an error. (This correction
follows the final review of #111. The duplicate, the three error sessions and the 11.4-63.3 s
range were re-measured on 2026-10-17 in the judge review of #144 and reproduce. The totals above
are as first measured on 2026-10-09; the same glob cut at that date now gives 34/20/26/8: three
transcripts have since gained `SessionStart:resume` records, and a 7-line transcript with no
SessionStart record is now counted.)

What this does establish: when the hook is logged it always precedes the session's first action,
the gap is never marginal, and the unlogged window is confined to 2.1.289 on one day. What it does
not establish is why those 9 sessions carry nothing — hook failure and absent logging are both consistent
with the data. Either way it fails safe: no snapshot means this check blocks exactly as it did
before dirty-path attribution existed. Wiring is tracked at
`governance/claude/tree-snapshot-hooks.json` and merged into `~/.claude/settings.json` by
`bin/install-claude-surfaces.sh`; the store is `logs/tree-snapshots/` — a `latest-<session>-<repo>.json`
pointer plus an append-only `snapshots.jsonl` ledger, the same two-file pattern as gate-receipt
and the worktree-lock registry. **Not** claimed as tamper-detection: nothing reads the ledger,
so it is an audit record, not a control.

At closeout the guard classifies every dirty path. **Every ambiguous case blocks:**

Paths already excused as proven govsync output never reach this table; what follows applies to
the remainder.

| the path, at closeout | verdict |
|---|---|
| dirty, and there is no usable snapshot for this session + repo | **blocks** — same verdict, exit code and stdout as before this existed; stderr gains one line naming the reason |
| dirty, and absent from the snapshot | **blocks** — the session created it |
| dirty, in the snapshot, digest **changed** since | **blocks** — it was already dirty and the session edited it anyway |
| dirty, in the snapshot, digest **unchanged** since | **reported, does not block** — inherited, and named so it stays visible |

The third row is what stops this being attribution in name only: path presence alone would clear
a session that edited a file somebody else had already dirtied, so the comparison is a content
digest, not a path list. The fourth row's informational line names the paths and repeats the
worktree-isolation instruction — **do not commit, stash or move them** — so inherited dirt is
surfaced rather than silently swallowed or silently adopted.

**Honest limits, stated so this is not mistaken for proof of authorship:**
- **Missing evidence never relaxes the gate.** No snapshot means the pre-2026-10-07 behaviour,
  with identical exit codes. On a block the stdout is the same and a stderr line naming the
  reason is new; on an allow the reason also reaches the model as `additionalContext`, so "byte
  for byte" is true of the verdict and not of the output. A session that
  predates the hook wiring, another vendor's session, or a repo the
  session first dirtied inside a single turn without ever being seen in it all block as before.
  This is deliberate (`AGENTS.md` §7: do not weaken a gate to turn red green) and it is also why
  the fix is worth little without the hook actually wired.
- **A third process that modifies an already-dirty file *during* the session** — govsync,
  obsidian-git, another session sharing the checkout — changes its digest, so the guard attributes
  it to the closing session. That **over-blocks**; it never under-blocks, which is the safe
  direction for a lost-work guard. The informational line names such paths explicitly so the
  agent can say "that edit was not mine" instead of committing it.
- **A session that edits an inherited dirty file and reverts it byte-for-byte** reads as
  untouched. The tree then holds exactly what the session found, so none of the session's own
  work is lost — the thing this check exists to prevent.
- Paths are enumerated with `-uall`, so a file created inside an **already-untracked directory**
  is the session's own, not inherited. Above 5000 dirty paths the snapshot is marked truncated and
  the guard treats it as no snapshot at all (blocks).
- A digest the snapshot could not read (`unreadable`) never compares equal to anything, including
  itself: a path we could not read cannot clear the session of having changed it.
- **This is a tripwire with an audit ledger, not a filesystem boundary.** The session's own
  machine writes the snapshot, so a session that forges one defeats the attribution — the same
  posture every other guard in this file states for itself. Gitignored files never appear in
  `git status` and are out of scope entirely.
- The **ancestry** half of check 1 is untouched: inherited dirt never rescues a session's own
  unmerged or unpushed commits.

**Vault exemption — the hook now matches the written rule (2026-10-07).** The Obsidian vault
(`canonical-paths.md`, "Obsidian vault (LAW)"; `$QB_VAULT` overrides the path, as in
`vault-guard.sh`) is exempt from check 1, exactly as the
[Clean-state standing rule](#clean-state-standing-rule-ratified-2026-08-25) already exempts it: it
is iCloud-synced and self-controlled, not a PR-workflow repo, and Obsidian rewrites workspace and
plugin state constantly, so a dirty tree there is not lost work. Until now the hook had no such
exemption and blocked a closeout run from the vault whenever the vault was dirty or an
auto-commit ahead of origin — which obsidian-git's 10-minute commit/push cycle makes routine. The
match is exact — the realpath of the repo's git toplevel must equal the vault's realpath (no
marker file, no name or prefix match), so no other repo is exempted. A `$QB_VAULT` that is not an
absolute path to an existing directory is ignored with a warning and the canonical path is used,
so a bad override cannot switch the exemption off. Check 2 (PR thread disposition) still applies
to the vault (the vault repo has no PRs, so it does not trigger in practice), and the hook prints
one informational line whenever it takes the exemption.

**Override — verified, never a matter of trust (`bin/closeout-override.sh`, revised same
day):** JP, on first seeing this design: "I don't know what's right or wrong so I shouldn't
be the approver" — an override that runs on the issuer's unverified word puts exactly the
wrong person in the loop. So `issue` now REQUIRES `--repo owner/name --pr N` and mechanically
verifies via `gh pr view` that the PR is actually `MERGED` before it will produce a code —
it refuses outright if the PR is still open or was closed unmerged. The orchestrator's job is
therefore not "vouch for this" but "actually merge it, then the tool confirms that for you."
`--owner`/`--reason`/`--session-id`/`--ttl-minutes` (default 120min) are still required for
attribution and scope, but they no longer stand in for verification. The guard spends the override
when it lets a blocked closeout through, recording the tool call's `tool_use_id`, so one override buys
one closeout attempt; the read-check-write runs under a file lock, so of two concurrent calls only one
can spend it. A PostToolUseFailure hook
(`hook-closeout-guard.py refund`, wired from `governance/claude/closeout-guard-hooks.json` by
`bin/install-claude-surfaces.sh`) gives it back only when that very call failed because Ceres
refused the closeout before closing the session (`MaintenanceCloseoutError`,
`MaintenanceHoldError`, `INVALID_SESSION`; never `SessionAlreadyClosedError`, never an interrupt,
never after expiry, at most 3 times), so the retry with a corrected status does not need a second
override (sentinel #133). Any signal that is missing or unreadable leaves it spent: the worst case is
a second override, never a reused one. Each consumption and refund is a line in the ledger
`logs/closeout-overrides/overrides.jsonl` — who authorized it, why, which PR was confirmed merged,
and that it was actually used — not just that it was issued; the refund hook's decisions are in
`refund-hook.jsonl`. The session's `latest-<session>.json` is the authoritative state: a refund is
recorded in the ledger before it is applied, so a failure between the two leaves a `refunded` line
for an override that is still spent. `closeout-override.sh status` lists
issued/consumed/expired overrides for audit.

## Open-work tracker — running count of unpushed/unmerged state (ratified 2026-08-26)

JP: "Ceres should keep a running count of open work which hasn't been pushed" — so visibility
into abandoned/lost work doesn't depend on any single session's closeout attempt catching it.

`bin/open-work-report.sh` scans every repo listed in `governance/rules/open-work-roots.txt` and
every repo govsync governs, and every worktree git lists for them (`git worktree list`, wherever
the tree lives; [#118](https://github.com/FinTechGlobalSolutions/sentinel/issues/118)). For each
it checks: any uncommitted/staged changes, any local commits not yet on a remote, and whether the
current branch (if not the default) is actually merged into `origin/<default-branch>`. A repo with
no remote (Distyll, vigilum) is compared with its local default branch instead, and each branch
never merged into it is reported. A directory under a `worktree-roots.txt` root that git does not
know is reported as possible orphaned work. Two-layer output,
same pattern as every other report in this file:
1. A dated local file, `logs/open-work/report-<date>.md`.
2. A forced `ceres queue submit` record (type `open_work_snapshot`) — counts and per-repo
   findings, so the running total is durably queryable in governed memory, not just a file
   nobody opens.

Scheduled daily via LaunchAgent `com.sentinel.open-work-report` (07:29). Live-tested
2026-08-26: found real, verified open work on first run — 6 repos with an uncommitted
`AGENTS.md`/`.sentinel/governance/.generated-by-sentinel` regeneration (a legitimate govsync
side-effect of the sentinel `main` commit advancing when this same session's PR #17 merged),
plus the sentinel repo's own in-flight changes for this feature. Not silently fixed across
unrelated repos — reported, left for their own owning sessions/PR flow.

**Landing is automated since 2026-09-08 (`bin/govland`).** JP: "govsync is supposed to do all
of that itself." `govsync` only distributes files; `govland` lands them the way this file
requires — a worktree branch `chore/governance-sync-<commit>`, an explicit-path commit of the
generated files only, a PR, squash auto-merge on green, then the primary checkout is
fast-forwarded and the worktree removed (fast-forward for repos with no remote; the two
`JPF1111` repos are pushed under that identity and the CLI is switched back). `sentinel-service`
runs it after every `govsync --apply` that wrote anything **and whenever any governed repo holds
a govsync mirror that is correct on disk but uncommitted** (since 2026-10-17, sentinel#135 — an
out-of-band `govsync --apply` had left 31 repos in that state for 36h while the service read
healthy), and reports `landed` / `land_failed` in `logs/service/governance-status.json`. First
live run: 29 repos in one pass.

**Where to look when a repo did not land (since 2026-10-07).** `governance-status.json` is one
overwritten cycle and names only the current failures (`land_failed_repos`); the durable record
is `logs/service/governance-sync-history.jsonl` — one JSON line per cycle (and one per out-of-band
`govsync --apply` that wrote something) carrying the trigger,
the sentinel commit, each repo's outcome and the reason it failed or was exempt, and the cycles
that never started because the lock was held. The raw per-cycle output lives beside it in
`logs/service/governance-sync-runs/`. Both are bounded (400 cycles / 40 raw runs per kind), so
neither is an unbounded write under AGENTS.md §4. Before this, the `.out` files were truncated
at the start of each run and `launchd.out` stamped lines with the time but no date, so a failed
landing stopped being diagnosable once the next cycle began — which is exactly what defeated the
2026-10-07 question about the-archives' unlanded 2026-10-04 mirror.

An uncommitted mirror is a finding, not an in-sync repo (since 2026-10-17). `govcheck --all
--report-uncommitted` prints an `UNCOMMITTED` row for a non-exempt repo whose generated `AGENTS.md`
or `.sentinel/governance` is modified or untracked; it is not drift and does not change govcheck's
exit code. The service runs that and, when any repo is uncommitted, runs govland (which walks every
governed repo with a dirty governance path, not only those), then re-checks: whatever is still
uncommitted keeps `status` at `drift` and is named in `uncommitted` / `uncommitted_repos` and in the
history line's `uncommitted` object. A repo whose primary checkout govland could not fast-forward
after landing (govland prints `primary not fast-forwarded:` with git's reason; a PR still waiting on
its checks never causes that) also keeps `status` at `drift`, named in `primary_stuck_repos` in the
status file and the history line: it needs a person. The post-landing govcheck is kept in
`governance-sync-govcheck-after.out` and archived per run as `postcheck-*.out`. A `govsync --apply` run outside the service leaves its own
history line (`outcome: "out-of-band-write"`, with the invoker), so it is distinguishable from a
service cycle instead of simply absent. **Limits:** govland visits every primary with any dirty
governance path, and `CLAUDE.md` is one of them, so an uncommitted hand edit to `CLAUDE.md` alone is
enough for govland to visit that repo and revert the edit; the retry adds such passes (sentinel#152). The out-of-band line is
appended without the service's lock and can be lost if it lands during the service's trim of the
history file (only once the file exceeds 400 lines).

**Honest limits, stated so this isn't mistaken for airtight:** PR ownership is decided by the
worktree-lock registry, not by gh (which has no session concept) — see *Ownership* in the
closeout-guard section above. A session that never claimed a lock, and whose cwd is not on
its PR's branch, has nothing attributed: its open PR is warned about, not blocked on. The 12h
window still applies, so a PR this session touched outside it is missed. (Until 2026-10-07 the
heuristic was author-plus-window, so one session's PR blocked every other session's closeout —
sentinel #88/#89/#103.) This binds Claude Code sessions only, same as every other `PreToolUse`
guard in this file.

**Build-time evidence (2026-08-26):** live-tested end to end against real repos and a real
GitHub PR (sentinel#17) — dirty-tree block, GraphQL thread-resolution query (caught and fixed
a real schema error: `PullRequestReviewThread` has no `url` field), override issue → consume
→ re-block cycle, and a real `hashlib` import bug caught by the guard's own fail-open path
during testing (proving fail-open works, then fixed so the real check runs).

**Build-time evidence for dirty-path attribution (2026-10-07):**
`python3 tests/test_hook_closeout_guard_dirty_attribution.py` — 63/63, and 23 mismatches against
the pre-fix hook, all of them inherited-dirt, mixed or reporting cases: every case where a
session's own work must still block passes on both hooks — **except `i2` and `k2`, which are
the point of the change: own work inside another session's locked worktree did not block before
it.** The two existing suites
(`test_hook_closeout_guard_vault.py` 28/28, `test_hook_closeout_guard_attribution.py` 69/69) pass
unchanged, which is the no-snapshot path proving its verdict and exit code unchanged (on a block
its stdout too, with one added stderr line; on an allow the reason also reaches
`additionalContext`). Live runs with the real
`gh`, the real lock registry and the real snapshot engine, in a fresh clone of this repo whose
`HEAD` is an ancestor of `origin/main`: two untracked files created *before* the snapshot then a
closeout → **exit 0** with both named as inherited; the same session then creating one file of its
own → **exit 2** naming only that file, with the inherited pair still reported as not its to
commit; a session id with no snapshot → **exit 2** with the pre-fix message; the session editing
one of the inherited files → **exit 2**, that path reclassified as its own.

## Session ledger — SOW, naming, audit close-out (ratified 2026-10-05)

**Sanctioned by: JP, 2026-10-05, live in chat** — "any work that is not apart of the sessions
original SOW needs to be created in an entirely new session so that it can be tracked and taken
through completion", "a single command that will have claude perform a full audit of its chat
history and report back everything which it accomplished and things that are still open from
that session not others", "every chat sessions name changed to a unique name … a uniform naming
structure", the record kept in Obsidian because "jira isnt gonna be around for ever"; then "go".
Full operator documentation: `docs/session-ledger.md`. Engine: `bin/session-ledger.py`.

1. **Every substantive session has a statement of work (SOW).** At the start of the session (or
   the moment the ledger hook reports none) the session runs `/sow`, which records the SOW,
   2–6 probe-checkable acceptance items and the out-of-scope list in a ledger file keyed by the
   Claude session id (`~/dev/closeouts/ledger/<session-id>.md`, `canonical-paths.md`).
2. **Work outside the SOW is spun off, never done inline.** The session hands it to a new
   session (Desktop: `spawn_task`, whose prompt starts with `/sow …` so the child opens its own
   ledger; elsewhere: a named session JP starts) and records the hand-off with
   `session-ledger.py spin`. Answering a quick question is not work. Changing the SOW itself
   needs JP's explicit say-so (`init --force`).
3. **Uniform session names: `<client>-<YYYYMMDD>-<topic-slug>`.** Client codes:

   | code | client |
   |---|---|
   | `cc` | Claude Code — desktop app Code tab (also the IDE extensions) |
   | `term` | Claude Code — terminal CLI (`claude -n <name>` sets it at start) |
   | `cloud` | Claude Code on the web / cloud sessions |
   | `routine` | scheduled routines and SDK-driven runs |
   | `dispatch` | Dispatch sessions |
   | `c` | claude.ai chat |
   | `work` | Cowork |
   | `codex` | OpenAI Codex |

   `/sow` renames the session itself where the client allows it (Desktop:
   `set_session_title self`); elsewhere the session tells JP the name to set. This is separate
   from, and does not change, the worktree `<AGENT-CODE><MMDDYY>-` convention above.
4. **`/closeout` is the one audit command, runnable any time — by the session itself or by JP —
   scoped to this session only.** It
   inventories the session's own transcript (`session-ledger.py audit` — every request including
   mid-turn messages and anything compaction hid, files written, commit/PR commands, spin-offs),
   classifies each request DONE / OPEN / SPUN-OFF / DROPPED with a probe run at close time
   (§9e), writes the close-out note to the vault folder `Session Closeouts/` (append-or-replace
   one named file, through `vault-guard.sh`), closes the ledger, and records the Ceres receipt.
   Every OPEN item is recorded (`spin`, the open-items handoff, the vault note) — never left only
   in chat. It is non-terminal. `/closeout archive` runs it and then the close-out report below.
5. **Safety net.** Hooks wired machine-wide from `governance/claude/session-ledger-hooks.json`
   by `bin/install-claude-surfaces.sh`: `UserPromptSubmit` re-injects the SOW and the scope rule
   every turn (or nudges once when no ledger exists); `PreCompact` logs a snapshot; `SessionStart`
   after compaction/resume re-injects it; `SessionEnd` marks an open ledger `unclosed` (and a
   `closed` one too when requests arrived after its close-out) and writes an `unclosed` stub for a
   ledger-less session of 3+ requests; `/sow` replaces such a stub.
   `session-ledger.py list --status unclosed` is the backlog of sessions that never closed.
6. **The vault note is the record, not the tracker.** Jira, Project Desk and Ceres records may
   point at it; none replaces it.

**Sessions close themselves out (ratified 2026-10-11).** **Sanctioned by: JP, 2026-10-11, live in
chat** (session `cc-20261010-cockpit-capture-backlog-register-write`): "[also] remove the
restriction thats says only i can closeout i never asked for that", then "[with that note] I want
all sessions closed   out and labeled as such once all their deliverables are completed and
validated. Any remaining work or work needing performed needs to be recorded. Master closeout
reports to be completed on everyting too." The rule is JP's. The wording below, and each reading
marked *(chair's reading)*, are the Strategist chair's (delegated).
- `/closeout` is model-invocable. Nothing reserves it for JP; the skill no longer carries
  `disable-model-invocation`.
- **When a session closes** *(chair's reading: "deliverables" are the SOW's acceptance items)*:
  every acceptance item is DONE with a close-time probe (§9e) — where they apply, PRs merged with
  CI completed/success, versions tagged, worktrees removed — and every other request is DONE,
  SPUN-OFF (recorded with `session-ledger.py spin`) or DROPPED (the turn quoted); nothing OPEN
  belongs to the SOW. The session then runs `/closeout archive` itself, without waiting to be
  asked, from a checkout of the repo the work landed in, so the closeout guard checks the right
  repo (its pull-only primary usually works; unrelated dirt there without a session-start snapshot
  can block).
  Its ledger closes and the status board labels it `finished`, within the board limits below.
- **Remaining work is recorded, never left in chat.** The durable record of each item is the
  open-items handoff, the vault note, and `session-ledger.py spin --title …`. A session card is a
  convenience on top: in the desktop app, `start_session` with `own_initiative` for a follow-up the
  session found, or `offer` for a JP request while JP is present. A session with nothing open says
  so in the handoff.
- **Master close-out report** *(chair's reading: JP's term is not defined elsewhere in law)*: the
  Session Metrics Report below, with its vault statistics note and open-items handoff. It is
  completed at every `/closeout archive` — a session closing on finished work, or one JP retires
  (or a backlog sweep he ordered closes) with its remainder recorded — not only when JP archives a
  session by hand. A session never retires itself to skip validation. Whether JP also means
  a cross-session roll-up, like `~/dev/closeouts/MASTER-TRACKER_v2.md`, was put to him as an open
  question on 2026-10-11.
- A SOW deliverable not yet validated means the session does not close. It finishes the work,
  hands it off, or records what is outstanding (`session-ledger.py state waiting --reason …` when
  it waits on something outside the session).

**Honest limits.** The hooks bind Claude Code only; Codex, claude.ai and Cowork follow this rule
normatively and are named by hand. Nothing forces a session to close itself out: that is a norm,
and `session-ledger.py list --status unclosed` stays the backstop list. The `finished` label is a
desktop sidebar title: the reconciler updates it only while the app is open, and the title persists
after. Terminal CLI sessions carry the ledger's and the vault note's `status:`; Codex and claude.ai
have neither hooks nor a ledger. A prompt after a close-out does not reopen the ledger, and
`SessionEnd` then lists it `unclosed` while the board keeps it `finished`. `close` sets `finished`
even when SOW items remain (a retired session); until the engine follow-up proposed 2026-10-11, the
vault note's `status: open-items-remain` and the Ceres `blocked` receipt carry the truth. The
closeout guard checks one repo per close-out: the one the session closes from. The scope reminder is advisory text — the model judges what is in scope; nothing
blocks an off-SOW edit mechanically. Whether `SessionEnd` fires when a
Desktop session is archived (rather than its process exiting) is UNVERIFIED — the `unclosed`
list may miss such sessions until they are resumed. The audit lists what was attempted; only
the close-time probes establish what landed.

### Session status board (ratified 2026-10-09)

**Sanctioned by: JP, 2026-10-09, live in chat** — sessions "go stale because they get buried";
he wants to see at a glance whether each session is done or in progress, colour-coded to match
Operon's statuses and priorities (priority ≠ status), titles carrying project + status +
priority + agent app + ref # + description without the full date, sessions filed into groups and
moved automatically, and "if there is something which works for us we should adopt it". Ruled:
Needs Action takes red, In Progress yellow; priority shown as its letter; 15-minute reconcile.

1. **Operon is the source of the taxonomy.** Statuses are the Operon pipeline *Session*;
   priorities are Operon's S–F (default C). The sidebar shows the nearest emoji because a
   session title cannot carry a colour:

   | State | Sidebar | Operon colour | Means |
   |---|---|---|---|
   | `needs-action` | 🔴 | #DC2626 | JP must act: a permission, a question, a decision, a review — or a stopped session that finished its acceptance without closing itself out: resume it so it runs `/closeout archive` |
   | `in-progress` | 🟡 | #CA8A04 | an agent is working now |
   | `waiting` | 🟣 | #9333EA | blocked outside the session: CI, PR review, another session, a third party |
   | `planned` | 🟠 | #FF7B0F | queued, not started |
   | `parked` | 🔵 | #1A7EBC | deliberately paused, or idle 7+ days |
   | `finished` | ⚪ | #787878 | ledger closed (or, with no ledger, its PR merged / a routine run) |
   | `dropped` | ⚫ | #1F1F1F | cancelled |

2. **Display title:** `<state emoji> <priority> · <PROJECT> · <app> · <ref> · <description>`,
   e.g. `🟡 S · QB · cc · QB-711 · node tsconfig typecheck`. `<ref>` (Jira key, PR #, issue) is
   omitted when there is none. The `<client>-<YYYYMMDD>-<topic-slug>` name of rule 3 stays the
   ledger's searchable key; the sidebar shows the display title instead. Project codes live in
   `PROJECTS` in `bin/session-ledger.py` — extend that table, never invent codes per session.
   **Reading a title:** the [Session Label Key](https://claude.ai/artifact/KWcVJeGiYpNEaWBqcAR7Kz)
   (a private artifact; its source is [`docs/session-label-key.html`](../../docs/session-label-key.html))
   decodes every part, including Operon's definition of each priority letter. Those definitions
   live in Operon's settings in the vault (`.obsidian/plugins/operon/data.json`,
   `taxonomy.priorities`); the key copies them and is not their source.
3. **Sidebar sections, one per state**, named `<emoji> <State>`. A session's section and title
   are written only by the reconciler or by `/sow`, never by hand-curated pins.
4. **Transitions are automatic.** Hooks (`session-ledger-hooks.json`): a prompt → `in-progress`;
   `Notification` of a permission prompt or elicitation dialog → `needs-action` (it overrides a
   hold; other notification types are ignored); `Stop` → `needs-action` unless the ledger holds
   `waiting`/`parked`/`planned`/`dropped`; `/closeout` → `finished`. Under the live applier
   (`reconcile --from-app`) app rows carry no running flag and a closed ledger's hooks skip the
   state flip, so a session that takes more prompts after its close-out stays `finished` (engine
   follow-up proposed 2026-10-11). Priority and project come from the ledger
   (`state --priority`); only a session without a ledger keeps hand edits made to its title. A session sets a
   hold with `session-ledger.py state waiting --reason "<what it waits on>"`.
   `session-ledger.py reconcile` derives every row (running, open PR, merged PR, idle days,
   routine repeats) and emits the plan; a 15-minute scheduled task applies it through
   `set_session_title` and `move_sessions`. The plan is computed by the script, not judged by
   the model that applies it.

**Honest limits.** Emoji approximate Operon's hex colours. The scheduled task runs only while the
desktop app is open. Renaming a session whose title JP set by hand asks him first and is declined
in an unattended run (app behaviour) — such rows stay as they are until he approves once.

**Nested side sessions share their root's section (2026-10-18).** **Ruled by JP, 2026-10-18, live in
chat** (session `cc-20261017-ceres-381-sanction-status-fix`, read from its transcript): "well then
technically the root session shouldnt be in the finished as well considering this spawn still has
work to to." The rule: a root is never filed under ⚪ Finished while any session nested under it, at
any depth, still has open work; it files under the most urgent state among itself and its live
nested descendants, in the table's order (Needs Action first). The app files a side session under
the session that started it, at every depth (sessions three levels below a root were live on
2026-10-18), and `move_sessions` refuses to move it alone ("nested sessions move with their parent.
Nothing was moved"; the refusal names the top of the chain, the root). So a family shows in one
section, which is how the rule is applied: the reconciler moves only roots, and a finished root with
a needs-action child is moved to 🔴 Needs Action. The reconciler also follows a chain through an
archived session: a live session reached that way counts toward the live top of its chain, and a
family whose top is archived is not moved. The app keeps nesting through an archived session: when
the reconciler moved `local_6f2828bc` to 🔴 Needs Action after 1.7.39 reached the primary checkout
(2026-10-18, 10:05Z), all six live sessions it reaches through archived parents moved with it
(`get_session`, read after the move). Titles stay per session, so that root keeps its ⚪ title
*(chair's reading: JP's words concern the section; the title still says the root's own work is
done)*. Within what the board can move, no nested session is filed under a section less urgent than
its own state. The plan reports as blocked, with the root named, each nested session that will not
show in its own section: one less urgent than its family (a finished child of a needs-action root),
and every session of a family whose root is archived or a routine run, which the board cannot move
at all, so they stay wherever that root was filed. Only detaching puts such a session in its own
section: "Detach to top level" in the sidebar (JP), or `detach_session`, which acts only on a
session the calling session started and the user has not moved, or on `self` when another session's
Claude started it, and refuses forks, task chips and a side session that shares another session's
worktree (its tool description, 2026-10-18). The 15-minute task started none of them and runs
unattended, where the tool is unavailable, so it never detaches. A detached session (`detached:
true`) is filed on its own. A fork the app files record only as `forkedFromSessionId` is treated as
top level. That is a disclosed risk, not a finding: on 2026-10-10 the app refused moves of three
such forks as nested while the top of their chain was live, and `get_session` does not report fork
nesting; one of them, `local_7ed717df`, has since been moved into its own group. No fork of a live
chain was live on 2026-10-18. A refused move is dropped by the applier and tried again on the next
run, and the fork's state is not rolled up to its root. Moving a pinned session into a group unpins
it (the tool's description); the board moves a root whether or not it is pinned, as it did before
this rule. `reconcile --sessions` reads nesting from `get_session`'s `parentSessionId`/`detached`,
or from `list_sessions`' `startedBy`, which appears on linked sessions only (side sessions Claude
started and linked forks; not on sessions spawned from a task without `sideSession`, plain forks or
detached sessions); `--from-app` reads it from the app's files.

## Session close-out — mandatory metrics report (canonical, ratified 2026-07-30)

**Trigger — every `/closeout archive` (JP, 2026-10-11; the reading is the chair's).** This report
runs at every `/closeout archive`: a session closing on completed, validated work, run by the
session itself without being asked or typed by JP, or a session being retired with its remainder
recorded (§ Session ledger, "Sessions close themselves out"). It is the session's **final act**
before it goes dark. A session that is merely
paused, waiting, or between slices does NOT produce this report; the non-terminal audit
(`/closeout`, § Session ledger above) may run at any time. `/closeout archive` runs that audit
first, then this report. Until 2026-10-11 this report fired only when JP issued the archive command
himself; JP ruled he never asked to be the only one who could close a session out.

Non-negotiable rule for every number in it: label each as **VERIFIED** (checked against
git/disk at report time) or **ESTIMATED** (reconstructed from conversation, could be off).
Never state a number that can't be sourced one of those two ways. Anything without a real
source is stated as UNVERIFIED with the reason — never omitted silently, never approximated
into looking precise.

**Required contents:**

*Agents & tools*
- Total subagents launched (Agent/Workflow tools), broken out by purpose (recon/build/
  review/etc.), with each agent's self-reported token + tool-call usage from its own result
  block, summed.
- Own main-thread tool-call count by tool (Bash/Edit/Read/Write/etc.) — ESTIMATED unless a
  real counter exists.
- Total token usage (main + subagents) ONLY with a real source; else UNVERIFIED with the
  reason (no introspection tool) — do not back into a number.

*Git / code*
- Every commit SHA the session authored, verified to still exist (`git cat-file -t` or
  `git log --grep` on own commit messages) — not recalled from memory.
- Unique files touched, deduplicated across commits (`git show --name-only`, sorted -u).
- Lines added/removed (`git log --shortstat` over the same commit set).
- Branches/worktrees created and their state (merged / open / removed).
- EXCLUDE concurrent sessions' commits that landed in the same window — match by SHA/message
  actually authored, never by whole-repo diff.

*Quality signals (often more valuable than raw volume)*
- Test counts before/after; whether full suites were rerun to confirm, and how many times if
  flakiness was being ruled out.
- CI/gate results touched or added and their pass state.
- Bugs found vs fixed vs deferred-with-a-ticket.
- Judgment calls flagged for sign-off rather than decided silently, and how they were ruled.
- Self-caught mistakes (a fix that made something worse before diagnosis) — reported, not
  hidden.
- Ratifications/decisions recorded to governed memory (Ceres/vault) with record IDs.

*Format:* verified numbers first (table if >3); estimated numbers clearly marked with a
one-line reconstruction method; anything unsourced stated as such.

**Vault write (last step, after the report is finalized in chat):** write the same report to
an Obsidian note titled `Session Statistics Closeout — <YYYY-MM-DD> — <one-line topic>.md`,
appended to (or replaced, if re-run same day) — not a new unbounded file per run. This is a
narrative close-out, not a governed D-###/C-### record, so write it directly (no Ceres
queue), run it through the vault-size guard (vault-guard.sh) as required for any direct
narrative write, and confirm the write succeeded by reporting the note's path back — never
assume it landed.

**Open-items handoff (required, alongside the metrics report).** An archived session is going
dark permanently — anything it knows that isn't written down is lost. So before it signals done,
the session writes an **Open Items & Issues handoff** to a durable file the Strategist will read:
`~/dev/code/qb/session-handoffs/OPEN-ITEMS-<YYYY-MM-DD>-<session-topic>.md` (create the
dir if absent; append if the file exists for that day). It contains, in plain prose or a list —
every unfinished thread, known bug or gap the session found but did not fix (with file:line and
enough context to act on it cold), anything left UNVERIFIED, any judgment call still awaiting a
ruling, any tracked-debt tickets it opened, and any "if you pick this up next, start here" notes.
If the session has genuinely nothing open, the file still gets written with an explicit
"No open items — all work landed and verified" line, so the Strategist knows the absence is real
and not an omission. This is separate from the metrics report (which accounts for what was done);
the handoff captures what remains. Report the handoff file's path back, same as the vault note.

**Terminal signal.** When — and only when — the metrics report is delivered, the vault note is
written and its path confirmed, and the open-items handoff is written and its path confirmed, the
session states exactly: **`READY TO BE ARCHIVED`**. That phrase is the explicit signal that the
session has completed every close-out obligation and can be abandoned. A session that has not
written both files, or that still has an unresolved question it needs answered before it can
finish, does NOT say it — it surfaces what it's still waiting on instead. `READY TO BE ARCHIVED`
means nothing is owed in either direction.

## Branching
- Never commit directly to `main`/`master` on shared repos. Branch: `feat/`, `fix/`,
  `chore/`, `docs/`, `refactor/` prefixes.
- One logical change per branch where practical.

## Commits
- Conventional Commits: `type(scope): summary` (≤72 char subject).
- **Any repo change carries a version bump and a CHANGELOG entry in the same commit as the
  work.** (Sanctioned by: JP, 2026-09-16 — moved verbatim from `~/CLAUDE.md`, where it had
  lived outside the law tree; see the release tagging rule above for tagging each bump.)
  **Repos without a version manifest get one** (Ruled by JP, 2026-09-16: "add a version").
  Implementation (agent's choice, not part of the ruling): a root `VERSION` file holding a semver
  string, bumped with every change like any other manifest (sentinel: `VERSION`, ledger
  `changes/CHANGELOG.md`). **Ruled by JP, 2026-09-24:** "exempt from making the repos do a
  version change but not the actual gov file. it must be versioned and it should also be
  documented in its changelog what the changes were" — generated `govsync`/`govland` mirror
  commits landed in *consuming* repos are exempt from this rule: they do not need to bump
  that repo's own version manifest or CHANGELOG. The exemption runs the other way for the
  governance source itself — any change to sentinel's own governance content (`governance/`)
  still bumps sentinel's `VERSION` and gets a `changes/CHANGELOG.md` entry describing what
  changed, in the same commit as the change, same as any other repo change under this rule.
- Body explains *why*, not *what*, when the change isn't self-evident.
- No co-author trailers unless JP requests them.

## Push / PR
- Never `git push --force` to a shared branch. Use `--force-with-lease` only on your
  own feature branch, and only after stating why.
- **Push authority (updated 2026-07-13; scope corrected 2026-08-16; merge authority settled
  2026-10-16 — the auto-merge ruling below governs where older wording differs):** The
  Strategist-chair gate exists to stop an
  **autonomous multi-agent campaign** (Workflow-spawned agents, background Agent-tool
  fan-outs, orchestrated swarms running without JP present in the loop) from self-merging
  to `main` unsupervised — that was the entire original problem it was built to solve.
  It was never meant to apply to a **single CODE session that JP is directly, interactively
  driving in the same live conversation**, and it is not a JP approval step. Concretely:
  - **Autonomous/multi-agent campaigns:** no worker self-merges to `main` unsupervised. The
    campaign's orchestrator is its Strategist chair: it runs the judge-tier review of each
    worker's PR and merges it under the auto-merge ruling below. "Strategist-chair review"
    means that orchestrator review, never a wait for JP.
  - **A single CODE session:** the session's statement of work (`/sow`, Session ledger above)
    is the instruction to merge what it delivers. No per-PR or current-turn instruction from JP
    is needed. **Verify, then act. Do not manufacture a second approval step.**
  - **Auto-merge — every in-SOW delivery is merged (Sanctioned by: JP, 2026-10-16,
    CHG-2026-10-19-001; extends the 2026-08-25 auto-merge-on-green policy and JP's 2026-10-11
    "... stop asking me for approval to merge shit" (session `5c25bcec`, 2026-10-11T16:32Z)):**
    "NO DELIVERY WILL BE LEFT UNMERGED EVER. I DO
    NOT NEED TO APPROVE A MERGE IF IT WAS APART OF THE SOW. ALL ISSUES RESOLVED AND FIXED
    AUTOMATICALLY, AUTO MERGE WHEN DONE, AND DELETE BRANCH"
    - **Who merges:** the session that delivered the work, or its orchestrator, as soon as
      the gates are green. JP never approves these merges (a review gate he set on the repo is
      the one exception; see the last limit below). No in-SOW delivery is left open,
      parked, or reported as "green, awaiting merge".
    - **Gates, on the exact head being merged:** CI `status: completed` +
      `conclusion: success` wherever the repo runs CI (a repo with no PR checks says so in
      the PR and runs its local tests instead of claiming green); a judge-tier review (§9f)
      recorded on the PR; every review thread resolved; repo-specific gates (version
      manifest, documentation completeness, and the like); `main` re-fetched immediately
      before the merge, which uses `--match-head-commit`. Merging on a misread or partial
      gate result violates this rule; it does not exercise it.
    - **Findings and conflicts are the session's to fix:** judge findings, reviewer threads,
      merge conflicts, a VERSION or CHG id that `main` already took (renumber upward). Fix,
      re-verify, merge. None of these is a reason to hand the merge back.
    - **After the merge, delete the branch:** `gh pr merge --squash --delete-branch`, or
      delete the ref, then remove the worktree per the Clean-state rule above. This ruling
      sanctions deleting a merged branch; it is not escalated. Where the repo has GitHub's
      "automatically delete head branches" on (`gh api repos/<owner>/<repo> --jq
      .delete_branch_on_merge`; true on `sentinel` and `sentinel-memory-os` on 2026-10-19), the
      remote ref is already gone after the merge; the local branch and worktree still need
      removing.
    - **A tool or classifier refuses the merge** (for example Claude Code auto mode's "Merge
      Without Review"): fix what it objects to, for example by posting the judge verdict on
      the PR (`gh pr review --comment`, since a shared GitHub identity cannot approve its own
      PR), then merge; or have the orchestrator merge under this ruling. Never park the merge
      on JP.
    - **Limits that still bind:** §5 (money movement, legal commitments, member/owner rights)
      still needs JP. This ruling covers merging delivered work, not deciding those
      questions. Work outside the SOW is spun off (Session ledger), not merged under this
      ruling. The escalation list and the owned-repo rule below are unchanged.
    - **A review gate JP chose is not an escalation.** A repo ruleset or CODEOWNERS rule that
      requires JP's review (sentinel since 2026-10-19, CHG-2026-10-18-005: CODEOWNERS `*
      @JPF1111` and the ruleset `sentinel-review-required`, which requires code-owner review and
      last-push approval) is a gate JP chose, not an escalation. Request his review, report the
      PR as "awaiting the ruleset's code-owner review", and merge as soon as it lands. Never
      bypass it, including through a bypass integration. "Never park on JP" forbids asking for a
      discretionary approval the SOW already gave.
  - **Why the 2026-08-16 correction exists:** the original wording ("do not ask JP — present the
    report to the Strategist chair") was written with only the multi-agent case in mind, but
    read literally it also blocked the single-session case — and did, causing real harm: a
    2026-08-16 audit found genuine, never-recovered work sitting in this exact gap (an
    orphaned 110-test adversarial security suite, an orphaned reconciliation doc, and three
    worktrees of fully uncommitted feature work), because sessions kept treating "ask JP" as
    forbidden even when JP was the one asking. See the Ceres decision logged same-day for
    the incident record.
  - Still escalate real irreversibility regardless of session type — see below.
- **Still escalate to JP (real irreversibility, not ceremony):** history rewrite, force-push
  to `main`, tag deletion, deleting a branch that has not merged, secret rotation, anything
  touching production data or DNS. Deleting a branch after its PR merged is not escalated (auto-merge
  ruling above).
- **Never push to a repo JP does not own. (Ruled by JP, 2026-09-28: "NEVER push to any repo which I
  do not own.")** Owned means a remote on one of the GitHub accounts in
  `bin/gov-owned-accounts.txt` (`FinTechGlobalSolutions`, `JPF1111`, `QuorumBooks`), and every push URL
  of the remote must be on one. Anything else — another person's project, another host, an SSH alias, a
  remote that cannot be read — is read-only: fetch, never push, never open a PR, never write generated
  files into it. A repo with no remote is JP's and is landed locally; a repo with remotes but no
  `origin` is judged by all of them. This binds every agent and every script, whoever asked and
  whatever the task, and none of the push-authority, auto-merge or clean-state rules above relaxes it.
  Enforced in code, not only here: `bin/govsync`, `bin/govcheck` and `bin/govland` skip such a repo and
  write nothing into it (`govland` also refuses to push to one as a last line of defence);
  `bin/worktree-lock.sh` pushes no marker ref to it; and `bin/hook-push-owner-guard.py` blocks a
  `git push` typed in a Claude Code Bash call, through newlines, `&&`/`;`/`|` chains, `cd`, `git -C`,
  wrappers (`time`, `timeout`, `sudo`, `env`, `xargs`…), `bash -c`, `eval`, `$( )`, named remotes,
  explicit URLs and every push URL of a remote, and refuses a push that retargets itself (`GIT_DIR`,
  `--git-dir`, `-c remote.*`). **Where the hook runs:**
  `bin/hook-push-owner-guard.sh` (the ownership check alone) is registered in
  `~/.claude/settings.json` (JP, 2026-09-28), so it binds every Claude Code session on this machine,
  including one opened in a clone of someone else's project — `headroom`, `YABA`, `ruflo`.
  `bin/hook-push-guard.sh`, which runs the same check plus the force-push ban and the origin/main
  token, is registered per project (sentinel, memory-os-installer, quorumbooks, Palladio,
  quorumbooks-cockpit, quorumbooks-www and a number of worktrees). Both read the command with
  `bin/gitpush.py`, so text that merely mentions a push (a commit message, an echo, a heredoc, a
  `git+ssh://` URL) is not a push. **Other stated limits:** the hooks do not see a push inside a
  script, alias, shell function or `make`-style runner, or from another vendor's tool. When the
  ownership guard cannot tell which repo a push runs in (an unresolved `$VAR` in a `cd`) or cannot
  parse the command, it allows that push with a warning; the force-push ban and the origin/main rule
  do not depend on the repo and still apply (to an unparseable command by its text). A `gh pr create`
  or `gh api` write against a foreign repo is blocked only because it needs a pushed branch first.
  Why it exists: on 2026-09-28 a sync wrote generated law into clones of `Subfly/YABA` and
  `headroomlabs-ai/headroom` (replacing a tracked upstream file in one, prepending a line to another)
  and `govland` tried to push to YABA; GitHub's 403 was the only thing that stopped it.
- Open PRs with a clear title, summary, test evidence, and risk note.
- **PR is now mandatory on `main`, mechanically (ratified 2026-08-16).** GitHub enforces
  this via an org-level ruleset (`protect-main`, `orgs/<org>/rulesets`), not just the
  local `hook-push-guard.sh` convention — a direct `git push origin main` fails at
  GitHub regardless of any local token, for anyone, including JP. This removes the
  local push-guard's meaningfulness as a hard control (it's now redundant with a real
  server-side rule) but not its value as a fast, in-session signal to redirect toward
  a PR instead of discovering the rejection after a push attempt.
- **Every Copilot (or other automated reviewer) suggestion on a PR must be reviewed
  and resolved before merge — mandatory (ratified 2026-08-16).** For each comment: take
  one of adopt / defer / decline, and say which, in the PR conversation thread itself
  (a reply, not just a code change with no comment back) — "adopt" means the suggested
  change actually landed in a commit, "defer" means a real follow-up item exists
  (session-handoff, backlog line, or a new Ceres/vault entry) naming who owns the
  follow-up, "decline" states why in one or two sentences so the same suggestion
  doesn't get silently re-litigated next time. Then mark the thread **Resolved**.
  **Mechanically enforced**: the `protect-main` ruleset's `pull_request` rule now sets
  `required_review_thread_resolution: true` — GitHub blocks the merge button while any
  conversation thread on the PR is unresolved, Copilot's included. This applies
  org-wide (every repo under the `protect-main` ruleset), not just `quorumbooks`.
  aspirational (ratified 2026-08-16, restates the `AGENTS.md` §2 rule: never report CI
  green until `status: completed` + `conclusion: success`).** Local pre-push gates (the
  quorumbooks `pre-push` hook — 15 steps as of qb-46, 2026-08-26, corrected here after Copilot
  review caught this doc still saying 14) are a fast mirror of CI, not a substitute for it —
  they run a narrower set of checks (no E2E, no full `pnpm test`) specifically so they stay
  fast enough to run on every push. "All local gates passed" is not the same claim as "CI
  is green" and must never be reported as if it were. Before writing "done," "shipped,"
  or "ready to merge" anywhere (chat, a PR description, a vault note), run
  `gh run list` / `gh pr checks` against the actual PR/commit and confirm
  `status: completed` and `conclusion: success` — paste the real command output, not a
  recollection of an earlier run. **Not yet mechanically enforced at the GitHub level**
  for `quorumbooks` specifically: the CI workflow was rewritten 2026-08-15/16 to gate
  every job (including the previously-unconditional Typecheck/Lint/Placeholder-scan
  jobs) behind a `changes`-detection job with path filters, and GitHub's
  required-status-checks feature has a known failure mode where a required check that
  gets legitimately *skipped* for a given PR (not merely slow) can block that PR's
  merge indefinitely. Adding `required_status_checks` to the ruleset needs a live test
  first — deliberately open a PR that skips one of the conditional jobs and confirm the
  merge button still behaves correctly — before it's safe to flip on. Track this as an
  open follow-up, not a silently-accepted gap.

## Before declaring done
- `git status` clean or intentionally staged.
- Tests/build pass if a test path exists.
- No secrets, no large binaries, no stray debug files.
