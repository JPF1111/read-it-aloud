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
  and escalated). This is not a one-time cleanup — it is the default condition a repo
  should be in after every session that touches it.
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

- **Location:** `~/dev/code/qb/worktrees/<branch-slug>/` — sibling to the main
  checkout, never nested inside it (nested worktrees confuse tooling). See
  `canonical-paths.md` for the registered root.
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
  pass green on the wrong runtime and mask a pin-specific failure until CI.
- One worktree per active session. A session works ONLY in its own worktree.
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
   `worktree-lock.sh takeover --slug <slug> --owner <name> --reason "..."` — it appends a
   named, reasoned ledger entry. There is deliberately no `--force` and the hook honors no
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
  1. **Merged and pushed** — `HEAD` must be an ancestor of `origin/<default-branch>` and the
     working tree must be clean. Uncommitted, unpushed, or unmerged work blocks closeout.
  2. **PR thread disposition** — for PRs authored by the session (heuristic: `gh pr list
     --search author:@me`, updated within the last 12h — not session-ID-scoped, gh has no
     such concept; generous window chosen to bias toward catching real misses over false
     confidence), every review thread must be resolved AND have a reply. A thread marked
     resolved with zero reply reads as dismissed/ignored, not dispositioned, and blocks.
  An OPEN PR in that window also blocks (not merged yet). A CLOSED-without-merge PR is
  reported informationally only — this control cannot mechanically verify "a stated reason
  was recorded," so it doesn't try to gate on it.
- Fails OPEN (exit 0) on any internal error (not a git repo, `gh` unauthenticated, network
  failure) with a warning printed — this is a tripwire-plus-audit control, not a filesystem
  permission, same posture as the worktree-lock guard above.

**Override — verified, never a matter of trust (`bin/closeout-override.sh`, revised same
day):** JP, on first seeing this design: "I don't know what's right or wrong so I shouldn't
be the approver" — an override that runs on the issuer's unverified word puts exactly the
wrong person in the loop. So `issue` now REQUIRES `--repo owner/name --pr N` and mechanically
verifies via `gh pr view` that the PR is actually `MERGED` before it will produce a code —
it refuses outright if the PR is still open or was closed unmerged. The orchestrator's job is
therefore not "vouch for this" but "actually merge it, then the tool confirms that for you."
`--owner`/`--reason`/`--session-id`/`--ttl-minutes` (default 120min) are still required for
attribution and scope, but they no longer stand in for verification. The guard consumes the
override exactly once (a second closeout attempt on the same session re-blocks), and logs
consumption — who authorized it, why, which PR was confirmed merged, and that it was actually
used — not just that it was issued. `closeout-override.sh status` lists
issued/consumed/expired overrides for audit.

## Open-work tracker — running count of unpushed/unmerged state (ratified 2026-08-26)

JP: "Ceres should keep a running count of open work which hasn't been pushed" — so visibility
into abandoned/lost work doesn't depend on any single session's closeout attempt catching it.

`bin/open-work-report.sh` scans every repo listed in `governance/rules/open-work-roots.txt`
plus every active worktree under the roots in `worktree-roots.txt`, and for each checks: any
uncommitted/staged changes, any local commits not yet on `origin/<default-branch>`, and
whether the current branch (if not the default) is actually merged into it. Two-layer output,
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
runs it after every `govsync --apply` that wrote anything and reports `landed` / `land_failed`
in `logs/service/governance-status.json`. First live run: 29 repos in one pass.

**Honest limits, stated so this isn't mistaken for airtight:** the PR-ownership heuristic is
time-window based, not session-scoped (gh has no session concept) — a PR someone else
touches in the same 12h window could be caught by a different session's closeout attempt; a
PR this session touched outside the window would be missed. This binds Claude Code sessions
only, same as every other `PreToolUse` guard in this file.

**Build-time evidence (2026-08-26):** live-tested end to end against real repos and a real
GitHub PR (sentinel#17) — dirty-tree block, GraphQL thread-resolution query (caught and fixed
a real schema error: `PullRequestReviewThread` has no `url` field), override issue → consume
→ re-block cycle, and a real `hashlib` import bug caught by the guard's own fail-open path
during testing (proving fail-open works, then fixed so the real check runs).

## Session close-out — mandatory metrics report (canonical, ratified 2026-07-30)

**Trigger — archival only.** This report is NOT part of routine close-out or a session going
idle. It fires ONLY when JP issues the explicit close-out command for a session — the command
that means the session is being **abandoned and archived**: no further messages, never used
again. When that command is given, the Session Metrics Report is the session's **final act**
before it goes dark. A session that is merely paused, waiting, or between slices does NOT
produce this report — only the one being retired. The Strategist requests it as the last
thing asked of that session; the session answers, and is then abandoned.

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
  `changes/CHANGELOG.md`). **Still PROPOSED — awaiting JP:** generated `govsync`/`govland` mirror
  commits are exempt. Until JP rules on that, the sanctioned text above governs them too; a sync
  PR that can't meet it discloses that and flags it to JP.
- Body explains *why*, not *what*, when the change isn't self-evident.
- No co-author trailers unless JP requests them.

## Push / PR
- Never `git push --force` to a shared branch. Use `--force-with-lease` only on your
  own feature branch, and only after stating why.
- **Push authority (updated 2026-07-13; scope corrected 2026-08-16 — read this correction,
  not just the original text below):** The Strategist-chair gate exists to stop an
  **autonomous multi-agent campaign** (Workflow-spawned agents, background Agent-tool
  fan-outs, orchestrated swarms running without JP present in the loop) from self-merging
  to `main` unsupervised — that was the entire original problem it was built to solve.
  It was never meant to apply to a **single CODE session that JP is directly, interactively
  driving in the same live conversation.** In that case JP himself is the Strategist chair
  for that session — there is no separate approval channel to route to, and routing to one
  anyway just adds a step that accomplishes nothing except sitting on finished, gate-verified
  work. Concretely:
  - **Autonomous/multi-agent campaigns:** the original rule stands unchanged. No agent may
    self-authorize a push/merge to `main`, even under a stale or general prior instruction.
    Verify gates, present the report, wait for explicit Strategist-chair review of *that*
    push before it happens.
  - **A single interactive CODE session with JP live in the conversation:** once JP has
    given an explicit, current-turn instruction to push or merge a *specific* piece of work
    (not a standing/ambient permission from an earlier session), the session may proceed —
    push feature branches, and merge PRs via `gh pr merge` — after verifying gates are
    actually green (typecheck clean · tests green, no regression · scanners pass ·
    review threads resolved · CI green). **Verify, then act. Do not manufacture a second
    approval step JP already gave in this turn.**
  - **Auto-merge on green gates — standing policy (ratified 2026-08-25):** for a PR the
    session itself opened during a live interactive session, once every gate is verified
    green (tests, lint, type-check, CI, review threads resolved, and any repo-specific
    gates — version-manifest/documentation-completeness verify for `sentinel-memory-os`,
    the analogous checks elsewhere), **merge it without waiting for a fresh per-PR
    instruction.** JP does not need to re-approve a PR whose gates the session already
    verified — that repeats work JP delegated by starting the session at all. This does
    not touch the escalation list below (still real irreversibility only), does not extend
    to autonomous/multi-agent campaigns (unchanged, still requires explicit review), and
    does not relax "verify gates are ACTUALLY green" — a session that merges on a
    misread or partial CI result has violated this policy, not exercised it.
  - **Why this correction exists:** the original wording ("do not ask JP — present the
    report to the Strategist chair") was written with only the multi-agent case in mind, but
    read literally it also blocked the single-session case — and did, causing real harm: a
    2026-08-16 audit found genuine, never-recovered work sitting in this exact gap (an
    orphaned 110-test adversarial security suite, an orphaned reconciliation doc, and three
    worktrees of fully uncommitted feature work), because sessions kept treating "ask JP" as
    forbidden even when JP was the one asking. See the Ceres decision logged same-day for
    the incident record.
  - Still escalate real irreversibility regardless of session type — see below.
- **Still escalate to JP (real irreversibility, not ceremony):** history rewrite, force-push
  to `main`, branch/tag deletion, secret rotation, anything touching production data or DNS.
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
