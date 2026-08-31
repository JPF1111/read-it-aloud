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
  `## Now`, `## Direction` — exact header words.
- **Secrets never enter the desk.** Refer to env-var or vault names. The MCP
  rejects recognizable credentials; do not test that.
- **Issue ids are `qb-N`**, sequential, lowercase on write.

## 5. Drift watch — custos

`governance/rules/custos-manifest.json` entry `project_desk_contract`
(class `remote_contract`) pins three probes against the live page:

| probe | pinned (2026-08-31) |
|---|---|
| contract version | `4.10.0` |
| PD protocol version | `10` |
| block template sha256 | `11b6549696688d72fa5a71a0c5af4a3f65c7c00289e99054020d7f43a343de06` |

Re-pinned 2026-08-31 from `4.7.0` / `8` / `6f6f012345ad…`. Reason: the vendor shipped
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
