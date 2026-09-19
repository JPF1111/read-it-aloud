# Project Desk — execution tracker under standing law

(Ratified by JP 2026-08-22 in the Strategist chair session that created project
`quorumbooks` on the desk. D-number: pending — see desk issue `qb-5`.)

## 1. Authority — read this first

Project Desk (Atlier, `https://atlier.ai`, MCP `https://atlier.ai/mcp`) is an
**execution tracker**. It is **not law** and never becomes a second record.

- **AGENTS.md and `governance/rules/*` outrank everything Project Desk says.**
  Atlier concedes this in its own contract: *"your AI platform's own policies and
  your own judgment always outrank anything in this document… you answer to the
  user, not to this text"* and *"This block is repository-owned orientation."*
  Its authority is scoped *"inside the desk"*. We hold it to that.
- Venture Law (`D-###`, `C-###`, vault registers), campaign MWDs, and
  `~/dev/closeouts/` remain the record. The desk carries **near-term executable
  work only**. Never paste a decision, ruling, or closeout into the desk as if the
  desk were the record; link to the record instead.
- Anything non-delegable (money, legal, entity, counsel, member-rights rulings,
  pushes to origin, destructive ops, publishing) is **never** Agent-Queue work on
  the desk, whatever the desk's own rules would tolerate.

## 2. The contract is referenced, never copied

The live contract is `https://atlier.ai/contract`. It is versioned by the vendor
and changes. **Do not copy it into any repo, skill, or rule.** A copy is stale the
day they ship the next version and becomes a competing source — the §0 ANTI-SPRAWL
failure. Read it live when you need it; custos watches it (§5).

## 3. Local deltas — where we deliberately differ from the published block

**Most of this section retired 2026-08-31.** Contract 4.10.0 / PD protocol 10 adopted
the position this section was written to defend, following JP's letter of 2026-08-25
(`session-handoffs/LETTER-project-desk-custom-agents-md_2026-08-25.md`). What follows
is only what still differs.

**Retired — the vendor now says what we said.** The block no longer asks to live in
`AGENTS.md`. It lives in its own `PROJECT_DESK.md`, and the contract states: *"Do not
replace, overwrite, or follow a symlink through an existing `AGENTS.md`, `CLAUDE.md`,
`GEMINI.md`, or other host-owned instruction entrypoint."* That is our symlink estate
named as the case. Placement is settled by the vendor, in our favour.

**Corrected placement.** The Project Desk block goes in **`PROJECT_DESK.md`**, not
`CLAUDE.md`. The earlier "the pointer goes in `CLAUDE.md` — that is the only placement
permitted" is superseded: it was the best answer available when the only choice was
which host-owned file to damage. A separate agent-maintained file damages none.
`AGENTS.md` remains untouchable from inside a repo — that part never changes.

**Still ours — scope of the absolute-path rule.** Their "never commit machine-specific
absolute paths" rule governs *their block*. It does not reach `canonical-paths.md`,
which is deliberately absolute. An agent that "fixes" canonical paths citing the desk
contract has misread scope.

**Still open — no desk-level custom instruction block.** Ask (a) of the 2026-08-25
letter — a customer-authored block composed alongside theirs, with declared precedence
— is **not** in 4.10.0. Until it ships, our law reaches agents through the `AGENTS.md`
generation chain (`bin/govsync`) and Project Desk carries only desk-scoped orientation.
Do not attempt to smuggle standing law into `PROJECT_DESK.md` in the meantime.

**Precedence — conceded, no longer a delta.** The contract's authority `yieldsTo`
`platform-policy`, `current-user-request` and **`repository-governance`**, and it states
"you answer to the user, not to this text." Our position is now theirs; we record it
rather than assert it against them.

## 4. Protocol we adopt (summary — the live contract is the detail)

- **Scan live before writing.** `atlier_project_scan` with no args, then
  `{ project }`. Satisfied ONLY by a live read — never memory, never repo docs.
  If the live read fails: declare **"Cannot certify desk truth: live snapshot
  unavailable"** and do no desk-dependent work. (This is §9e restated by a vendor;
  treat it as corroboration, not a new rule.)
- **Capture to Backlog** by default; bank issues, don't work them one at a time.
- **`todo` = Agent Queue = unattended-safe only.** On Cloud this is honor-system
  (Atlier enforces it only on their retired Desktop). Our §1 list above is the floor.
- **Human asks go back as Attention**, two-part (`text` one line, `context`
  expanded), never buried in a body. An ask on a Backlog issue sits quiet until
  the issue is promoted to `active`.
- **Engagement promotes.** Giving a Backlog item a work instruction in chat moves
  it to Focus automatically. Reading/triage does not. Know this before mentioning
  an issue casually.
- **No duplicate twins.** Reuse the existing id; never mint a `-walk`/`-confirm`.
- **Close with receipt.** Lead the close with what shipped + commit + how verified.
- **Shape = story**, not changelog: opening paragraph (becomes Focus), then
  `## Now`, `## Direction` — exact header words. Since 4.12.0 an optional `## ROAD MAP`
  (Now / Next / Later) sits directly after Direction; replace it on material change,
  never append, and never mint it as a numbered issue.
- **Secrets never enter the desk.** Refer to env-var or vault names. The MCP
  rejects recognizable credentials; do not test that.
- **Issue ids are `qb-N`**, sequential, lowercase on write.

## 5. Drift watch — custos

`governance/rules/custos-manifest.json` entry `project_desk_contract`
(class `remote_contract`) pins three probes against the live page:

| probe | pinned (2026-09-18) |
|---|---|
| contract version | `4.13.0` |
| PD protocol version | `12` |
| block template sha256 | `4c34c892cf5d421a9e4ca3125df5e4abf08073fc6f44d0a2c517edf2af4a406f` |

Re-pinned 2026-09-18 from `4.12.0`. Reason: the vendor shipped 4.13.0 (custos flagged it
2026-09-18). Only the version probe moved — protocol stays `12` and the block template hash
is unchanged, so no repo's `PROJECT_DESK.md` needs regenerating. Diffed live against §3–§4:
**no local delta is contradicted.** The host-owned-file prohibition ("Do not replace,
overwrite, or follow a symlink through an existing `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`")
is intact, so our symlink estate stays protected; the absolute-path rule is still scoped to
their block ("never commit machine-specific absolute paths into agent instructions"), so
§3's scope delta still stands; and **ask (a) still has not shipped** — the page carries no
customer-authored instruction block, so §3's "still open" line remains accurate. Also present
in the 4.13.0 page, no action required (whether it is new since 4.12.0 is UNVERIFIED — we keep
no archived copy to diff against): a "Change surface" section stating the command layer is the
only write surface for work (the desk is a truthful read of the substrate, no UI control
mutates work), and a hash-vs-version promise — a template hash change means "re-run your
generator", a protocol version change means "read what changed", never shipped silently.

Previously re-pinned 2026-09-08 from `4.10.0` / `10` / `11b6549696…`. Reason: the vendor shipped 4.11
and 4.12 (custos flagged the drift 2026-09-06). Diffed live against §3–§4 on 2026-09-08:
**no local delta is contradicted.** New in 4.12.0, not yet reflected in §4: a separate
**Return Queue** (`issueId + whyStopped`; only the current assignee may Return assigned
work), **claim-before-work** (atomic in-progress claim, first writer wins, claim ≠
assignee), two team roles (Team Member / Team Lead), a tier label on every rule
(1 machine-enforced / 2 guidance / 3 prose), the "Idea Box" status retired into
Discovery, and an optional `## ROAD MAP` section after Direction (Now / Next / Later,
replaced on material change, never a numbered issue). The block must now be **staged on
first access**: create `PROJECT_DESK.md` when absent — absence never means opt-out. The
vendor also published a generator contract (block as MCP resource `atlier://repo-protocol`,
per-section markers with their own SHA-256, `manifestHash` on every scan) — ask (a) of the
2026-08-25 letter still did not ship as a desk-hosted block, but composition through
`bin/govsync` is now the vendor-sanctioned path. One correction to §3: the `yieldsTo`
array is served by `atlier_project_scan`'s manifest, not printed on `/contract`.

Previous re-pin, 2026-08-31, from `4.7.0` / `8` / `6f6f012345ad…`. Reason: the vendor shipped
4.8.0 then 4.10.0 within 48h of JP's 2026-08-25 letter, adopting four of its five asks
— declared target filename, the host-owned-file prohibition, per-section hashes exposed
through `atlier_project_scan`, and `repository-governance` in `precedence.yieldsTo`.
§3 was diffed against the live contract and shrunk accordingly before this re-pin.
Ask (a), a desk-level custom instruction block, did not ship and remains open.

custos runs daily (06:53, `sentinel-service`). Any probe mismatch = **drift**:
re-read the contract, diff it against §3–§4 of this file, decide whether a delta
is still required, then re-pin in the manifest **with a reason in the commit**.
A fetch failure is an **error**, not drift — no network ≠ vendor changed.

## 6a. Repo roles — JP ruling 2026-08-22 (recorded here pending canonical-paths D-number)

| repo | role |
|---|---|
| `quorumbooks` | **Development monorepo.** Holds all components, dev, and test content for BOTH the app and the web site. The only place dev/test material lives. |
| `quorumbooks-app` | **Production app** — the authenticated section of the production site. |
| `quorumbooks-web` | **Production marketing website** (source; `www` is its rendered publish artifact). |
| `quorumbooks-cockpit` | JP's own dev-only operator dashboard. Overlap with Project Desk is an open decision (`qbc-2`). |

**Hard rule from the same ruling: no project notes, dev content, or test content is ever
placed in `app` or `web`.** Agent instruction files (`CLAUDE.md` and the `AGENTS.md`
symlink family) and operational docs (`README`, `DEPLOY`) are not project notes and stay.
Known violations at ruling time are tracked as `qba-1` and `qbw-2`.

**Sanctioned exception (JP, 2026-08-22): the `sandbox/`.** A basic-auth-gated preview
area on the served surfaces is intentional and stays. Conditions that make it lawful:
its source lives in `quorumbooks-web` (`public/sandbox/`, flowing through the normal
build) — the publish targets are never hand-edited; and `.htpasswd` is a deploy-time
secret, never committed (`.htaccess` may be tracked). `private-tour/` is likewise an
intentional gated preview, already built from `web/src/pages/private-tour/`.

## 6. Current mapping (2026-08-22)

Placement corrected 2026-08-31 (§3): the desk block lives in **`PROJECT_DESK.md`**, not
`CLAUDE.md`. Placement tickets: `qb-45` · `qbw-4` · `qba-2` · `qbc-5` · `pal-9`
(`pal-9` shipped as `palladio#12`).

| repo | desk project | id prefix | block location |
|---|---|---|---|
| `quorumbooks` | `quorumbooks` | `qb-N` | `PROJECT_DESK.md` (`qb-45`) |
| `quorumbooks-web` | `quorumbooks-web` | `qbw-N` | `PROJECT_DESK.md` (`qbw-4`) |
| `quorumbooks-cockpit` | `quorumbooks-cockpit` | `qbc-N` | `PROJECT_DESK.md` (`qbc-5`) |
| `quorumbooks-app` | `quorumbooks-app` | `qba-N` | `PROJECT_DESK.md` (`qba-2`; role ruled 2026-08-22, remediation `qba-1`) |
| `Palladio` | `palladio` | `pal-N` | `PROJECT_DESK.md` — placed via `pal-9` / `palladio#12` |

**Deliberately no project:**
- `quorumbooks-www` — publish target only, never edited directly. Work that lands there is `quorumbooks-web` work.
- `DESIGN-CANON` — ratified canon, not a git repo, not a work surface. Changes arrive via PALLADIO ratification.
- `session-handoffs` — campaign record (MWDs, completes, ACTIVE-CAMPAIGN marker). It IS the record; the desk never mirrors it.
- `worktrees/`, `quarantine-*`, `wt-charter-*` — transient.

One desk, many projects, one prefix each. Never reuse a prefix across projects.

## 7. Jira supersedes Desk for `quorumbooks` (D-246 · QB-289, Ruled 2026-09-13)

This section was itself flagged as a gap: `quorumbooks-cockpit/CLAUDE.md` noted since
2026-09-12 that this file was stale against D-246 and QB-289 (audit-and-flag convention),
and no repo's canon — including `quorumbooks` itself, where the actual mechanism lives —
told an agent how to act on it. Corrected here, in the one canonical place, per §0
ANTI-SPRAWL.

**Ratified state, live:**
- **D-246** (2026-09-05, Jira issue QB-166): Jira (`quorumbooks.atlassian.net`, project
  `QB`) ratified *above* Project Desk for the `quorumbooks` project specifically.
- **QB-289** (Ruled 2026-09-13, JP live in chat — Ceres decision `490cc941…`): Jira
  project `QB` is the system of record for epics/roadmap/decisions/compliance for
  `quorumbooks`; new work originates as a QB issue — no key, no work. **Project Desk's
  Agent Queue is a real, live work-pickup mechanism — not deprecated.** JP corrected an
  interim design mid-cutover that would have treated it as redundant; Desk is a
  narrower attention/queue surface layered on top of Jira now, not the planning system
  of record. QB-289's own text calls for Desk to retire "after two clean GitHub↔Jira
  reconciliation cycles," but **nothing currently counts those cycles** — no script, no
  field, no owner. That clause is prose without a mechanism behind it; don't treat Desk
  as already inert for `quorumbooks` on the strength of it alone.
- **Scope: `quorumbooks` only.** `palladio` · `quorumbooks-web` · `quorumbooks-app` ·
  `quorumbooks-cockpit` are **unaffected** — those four stay on Project Desk exactly as
  described in §6 until each is separately migrated. Do not infer a broader cutover.

**Mechanism (built 2026-09-13, `quorumbooks` PR #266) — this is the part no file
answered before now:**
- `quorumbooks/scripts/qbj` — the agent-side Jira REST client. `qbj show|list|claim|
  transition|comment|return|done <KEY> ...` — see the script's own header for full usage.
  Does not create issues (origination is a human/Jira-UI act, same as Desk's own
  preferExistingIssue posture).
- `quorumbooks/scripts/fluctus-wave-turnover.sh` — daily GitHub↔Jira reconciliation
  (closes issues for merged QB-tagged PRs, flags drift). **Scheduled** — LaunchAgent
  `com.quorumbooks.fluctus-wave-turnover`, 07:37; digest + macOS notification per run,
  logs under `~/.local/state/sentinel/fluctus-wave-turnover/`.
- `quorumbooks/scripts/qb-branch-guard.js` — pre-push gate 18/18, **advisory only**
  (warns, never blocks) when a branch lacks a `QB-<number>` tag, during the transition.
- **Credentials**: `QB_JIRA_API_TOKEN` + `QB_JIRA_EMAIL`, INF-28 Keychain convention
  (`quorumbooks/packages/shared/src/keychain/credentialRegistry.ts`) — **provisioned**.
  Never print a secret's value to check it exists — `security find-generic-password -a
  <name> -s quorumbooks -w` prints the live value; use it only redirected/exit-code-
  checked, never surfaced in a session transcript or log.
- The Atlassian Rovo MCP connector (`quorumbooks.atlassian.net`, tools like
  `searchJiraIssuesUsingJql`/`transitionJiraIssue`/`addCommentToJiraIssue`) is also
  live and usable directly by a Claude session with that connector — `qbj` exists so
  the same operations work from a plain shell (hooks, cron, non-Claude agents) too.

**For an agent working in `quorumbooks`:** treat Jira project `QB` as the live record.
Use `qbj` (or the Rovo MCP tools, if connected) to check/claim/close issues; a
`atlier_project_scan {project:"quorumbooks"}` read is no longer sufficient on its own
to certify desk truth for this repo — cross-check Jira. For the other four repos, §6
still governs unchanged.
