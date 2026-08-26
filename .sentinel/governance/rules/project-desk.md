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

Atlier's block asks to live in `AGENTS.md` with `CLAUDE.md` reduced to a pointer.
**That is inverted here, and it is dangerous here:**

- In every QB repo, `AGENTS.md` is a **symlink** to
  `~/dev/sentinel/governance/AGENTS.md` — global standing law for all repos and all
  vendors. Writing a repo-scoped block there writes into law everywhere.
  **Never write to `AGENTS.md` from inside a repo.**
- Per §0, `AGENTS.md` is the constitution; repo-specific orientation lives in the
  repo's `CLAUDE.md` beneath its pointer at the constitution. The Project Desk
  pointer goes in `CLAUDE.md`. That is the only placement permitted.
- Their "never commit machine-specific absolute paths" rule governs *their block*.
  It does not reach `canonical-paths.md`, which is deliberately absolute. An agent
  that "fixes" canonical paths citing the desk contract has misread scope.
- Their "where a stale agent-instruction file disagrees, this is the current one
  to follow" is scoped *inside the desk*. It never overrides AGENTS.md.
- Rewriting their block breaks their template-hash drift check. Accepted trade;
  custos carries the drift watch instead (§5).

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

| probe | pinned (2026-08-22) |
|---|---|
| contract version | `4.7.0` |
| PD protocol version | `8` |
| block template sha256 | `6f6f012345ad3b67eeb336c54b1d8c4b79f7df36ac6b0a6bb3dc8eeb9b171f1d` |

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

| repo | desk project | id prefix | pointer |
|---|---|---|---|
| `quorumbooks` | `quorumbooks` | `qb-N` | `CLAUDE.md` → this file |
| `quorumbooks-web` | `quorumbooks-web` | `qbw-N` | `CLAUDE.md` → this file |
| `quorumbooks-cockpit` | `quorumbooks-cockpit` | `qbc-N` | `CLAUDE.md` → this file |
| `quorumbooks-app` | `quorumbooks-app` | `qba-N` | `CLAUDE.md` → this file (role ruled 2026-08-22; remediation `qba-1`) |
| `Palladio` | `palladio` | `pal-N` | no `CLAUDE.md` in repo; README carries campaign law. Pointer deferred until ratification. |

**Deliberately no project:**
- `quorumbooks-www` — publish target only, never edited directly. Work that lands there is `quorumbooks-web` work.
- `DESIGN-CANON` — ratified canon, not a git repo, not a work surface. Changes arrive via PALLADIO ratification.
- `session-handoffs` — campaign record (MWDs, completes, ACTIVE-CAMPAIGN marker). It IS the record; the desk never mirrors it.
- `worktrees/`, `quarantine-*`, `wt-charter-*` — transient.

One desk, many projects, one prefix each. Never reuse a prefix across projects.
