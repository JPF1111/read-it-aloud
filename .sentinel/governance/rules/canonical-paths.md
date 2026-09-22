# Canonical Paths — GLOBAL (all ventures, all repos, all agents)

**Never guess, never reconstruct, never "helpfully" correct a path from memory.**
If a path here conflicts with something you read elsewhere, THIS FILE WINS — and the
other source is a defect: flag it.

## Quorum Books (HOA SaaS)

| Thing | Path |
|---|---|
| **Monorepo (build target)** | `~/dev/code/qb/quorumbooks` → `github.com/FinTechGlobalSolutions/quorumbooks` (corrected 2026-09-07 on JP's instruction; the `JPF1111/quorumbooks` remote named here before is not where the repo lives) |
| Marketing site (Astro 5 + Tailwind v4) | `~/dev/code/qb/quorumbooks-web` |
| App | `~/dev/code/qb/quorumbooks-app` |
| Cockpit | `~/dev/code/qb/quorumbooks-cockpit` |
| Fleet-level canon | `~/dev/code/qb/CLAUDE.md` · `~/dev/code/qb/AGENTS.md` |
| **Obsidian vault (LAW)** | `~/Library/Mobile Documents/iCloud~md~obsidian/Documents/Prosperity Springs` |
| Campaign hub | `…/Prosperity Springs/CAMPAIGNS/ACTIVE/Quorum Books/` (corrected 2026-08-10, CENSOR W0 — live vault folder is "Quorum Books", not "HOA SaaS"; the old name is preserved in older decision-entry source citations as audit trail, per D-086) |
| **Sentinel toolkit** | `~/dev/sentinel` → `github.com/FinTechGlobalSolutions/sentinel` |
| **Worktree root (parallel-session standard)** | `~/dev/code/qb/worktrees/<branch-slug>/` — sibling to the main checkout, one per active session; see `git-workflow.md` |
| **Ceres source / installer** | `~/dev/code/memory-os-installer` |
| **Ceres CLI** | `~/.local/bin/ceres` |
| **Ceres MCP service** | LaunchAgent `io.ceres.memory.mcp` |
| **Ceres durable intake** | `~/.local/state/ceres/queue/` |
| **Ceres intake relay** | LaunchAgent `io.ceres.memory.intake-relay` |
| **Register archives** | In the governed Obsidian campaign folder; indexed from the active register |

## MYLO Health Systems

Recorded 2026-08-31. The venture had **no** entry in this file, on Project Desk, or
anywhere under `governance/` — and one of its two repos is not discoverable by name.
An agent asked to act on "the MYLO repo" had no way to resolve which repo was meant,
and got it wrong twice on 2026-08-31 before JP corrected it by hand.

| Thing | Path |
|---|---|
| Documentation hub | `~/dev/code/mylo-documentation-hub` → `github.com/FinTechGlobalSolutions/mylo-documentation-hub` |
| Legacy systems research | `~/dev/code/legacy-systems-research` → `github.com/FinTechGlobalSolutions/legacy-systems-research` |

**`legacy-systems-research` IS a MYLO repo** despite the name — it is the clean-room
research hub for legacy health plan platforms (QNXT and adjacent payer systems). Its
GitHub description was set on 2026-08-31 to name MYLO so the association is
discoverable; do not remove it. Public sources only — the repo's own README forbids
ingesting PHI, PII, customer-confidential data, NDA artifacts, or internal exports.

Both repos are private, both are governed by `bin/govsync`, and both sit under the
org-level `protect-main` ruleset like the rest of the estate. `legacy-systems-research`
additionally carried repo-level branch protection requiring 1 approving review, which
no peer repo had; that was removed 2026-08-31 to bring it in line, leaving
`protect-main` as the single control.

No Project Desk project exists for MYLO. That is a gap, not a ruling — if MYLO work
starts being tracked, give it a project and a prefix per `project-desk.md` §6.

## ElevenLabs — Read It Aloud (recorded 2026-09-07, JP: "they are in JPF1111 … update the path correctly, don't move repo locations")

| Thing | Path |
|---|---|
| Extension source | `~/dev/code/elevenlabs-extension/read-it-aloud` → `github.com/JPF1111/read-it-aloud` (public) |
| Feedback tracker | `~/dev/code/elevenlabs-extension/read-it-aloud-feedback` → `github.com/JPF1111/read-it-aloud-feedback` (private) |
| Extension package | `~/dev/code/elevenlabs-extension/read-it-aloud-extension` → `github.com/FinTechGlobalSolutions/read-it-aloud-extension` |

The first two live under JP's personal account, not the org. The automation identity most
agent sessions run as (`gh auth status` → `QuorumBooks`, an org token) cannot see them at all —
`gh repo view JPF1111/read-it-aloud` and `git fetch` both answer "not found", which on
2026-09-07 was misread as the account no longer existing. It exists; switch identity before
touching them (`gh auth switch -u JPF1111`, and for git pushes run with the gh credential
helper: `GIT_CONFIG_COUNT=1 GIT_CONFIG_KEY_0=credential.helper GIT_CONFIG_VALUE_0='!gh auth
git-credential'`), then switch back to `QuorumBooks`. Neither JPF1111 repo carries a ruleset;
land changes through a PR anyway (git-workflow.md). Do not move them into the org.

## DEAD PATHS — never write, never reference, never recreate

- ❌ `~/Documents/DEV/` — **entire tree deleted 2026-07-13.** Any reference is a defect.
- ❌ `~/Documents/code/` (and everything under it, incl. `~/Documents/code/qb/...`,
  `~/Documents/code/memory-os-installer`) — **relocated to `~/dev/code/` on 2026-08-10**
  to move the whole tree off active iCloud Desktop/Documents sync (Ceres decision
  `a5da6b5a-3e8a-4620-8bf8-724664c2b5fb`, corrected form of `d8ee6585…`). The move used a
  purpose-built tool (EXODUS, `~/dev/code/exodus`); JP drove the live run himself.
  `9387eb7` (this repo) fixed six scripts that hardcoded the old path via
  `${HOME}/Documents/code/...`, a form neither the original rewrite pass nor its later
  `~/`-shorthand fix had checked for — this table's own rows were the remaining miss,
  discovered live by a MINERVA W0/W1 session on 2026-08-10 (see that session's Ceres
  close-out). Any reference under the old prefix is a defect.
- ❌ `~/Documents/DEV/Github/Sandbox/QuorumBooks`
- ❌ `~/Documents/DEV/Github/Sandbox/clearstory-ledger`
- ❌ `quorum-marketing` / `quorumbooks-site` as *source* repos (`quorumbooks-web` is source;
  `quorumbooks-site` is the publish target only).

## Governance coverage — repos ruled on individually (JP, 2026-08-31)

Three repos under `~/dev/code` were dirty after every govsync run for structural reasons.
Ruled on rather than left ambiguous:

- ✅ **`~/dev/code/Distyll`** and **`~/dev/code/vigilum`** — GOVERNED, local-only. Neither
  has a GitHub remote (verified via `gh repo view` under both `FinTechGlobalSolutions` and
  `JPF1111` — neither resolves). `govsync` writes their `AGENTS.md`/`.sentinel/` normally;
  the generated content is committed locally on each repo's own default branch. There is no
  PR to open and no remote to push to — a local commit is the terminal state for these two
  until a remote exists, at which point they follow the normal branch → PR → merge flow like
  every other governed repo. **How that local commit is made (JP, 2026-09-07 — "don't care
  what path, just resolve it"):** since the worktree-isolation guard rolled out with
  sentinel#36, the primary checkout's pre-commit hook blocks direct commits there, so the
  generated mirror is committed on a throwaway worktree branch and fast-forwarded into the
  default branch (`git worktree add <tmp> -B chore/governance-sync-<commit> <default>` →
  `govsync --repo <tmp> --apply` → commit there → `git merge --ff-only` in the checkout →
  `git worktree remove`). No commit is created in the primary checkout, so the guard and
  this ruling agree; `--no-verify` is not the answer.
- ⛔ **`~/dev/code/slugthugshield-v3-audit-old`** — EXEMPT (not deleted, still on disk and in
  active use), like `vault` is exempt from the
  org `protect-main` ruleset. Its GitHub default branch is `SlugThugShell`, not `main` — a
  named working branch, not a stable trunk to generate law onto. Encoded as a name-based skip
  in `bin/gov-exempt-repos.sh`'s `EXEMPT_REPOS` array (sourced by `bin/govsync` and
  `bin/govcheck`, and by `bin/govland` as of `CHG-2026-09-21-003`). If this repo ever gets a
  real default branch, lift the exemption and let it be governed like its peers.

- ⛔ **`~/dev/code/qb/quorumbooks-www`** — EXEMPT (**ruled 2026-09-07: JP delegated the call — "don't
  care" — and the proposing session applied it**). It is
  the rendered publish target of `quorumbooks-web` (project-desk.md §6: never edited directly), and
  `quorumbooks-web`'s `publish.yml` replaces its entire tree with `dist/` via `rsync --delete` on
  every publish. The govsync mirror landed there by its PRs #3–#6 was deleted by publish PRs #8
  and #9 on 2026-09-07 within minutes of the next sync (#7, closed unmerged), and would be deleted
  again on every future publish. Hostinger serves that tree, so a mirror there also risks
  publishing standing law and this file at `quorumbooks.com/AGENTS.md` (404 as of 2026-09-07
  16:50Z; whether it was served between 2026-08-26, when agent files were first committed there,
  and 2026-09-07 is UNVERIFIED). Encoded as a name-based skip in `bin/gov-exempt-repos.sh`'s
  `EXEMPT_REPOS` (sourced by both `bin/govsync` and `bin/govcheck`).

- ⛔ **[`~/dev/code/everything-claude-code`](file:///Users/jpfinley/dev/code/everything-claude-code)**
  — EXEMPT (**ruled 2026-09-21: JP directive "fix known issues, don't leave them" — delegated
  disposition, applied after verification**). A vendored third-party clone, not repo-owned
  content: `origin` = [`FinTechGlobalSolutions/everything-claude-code`](https://github.com/FinTechGlobalSolutions/everything-claude-code),
  `upstream` = [`affaan-m/ECC`](https://github.com/affaan-m/ECC), `HEAD` =
  [`c8b555a3`](https://github.com/FinTechGlobalSolutions/everything-claude-code/commit/c8b555a33c6e8a182d88c2df3804e8b876fc0a13)
  ("chore: replace with current affaan-m/ECC (was a stale WorldFlowAI fork)",
  [PR #3](https://github.com/FinTechGlobalSolutions/everything-claude-code/pull/3), 2026-09-18).
  Its `AGENTS.md` (8805 bytes) is upstream content — `git diff upstream/main -- AGENTS.md` shows
  only upstream's own post-import drift (version bump 2.2.1→2.2.2; upstream **added** the
  `ecc:` agent-name prefix in
  [commit `e404468a`](https://github.com/affaan-m/ECC/commit/e404468a629517b7bdc9188723f1aed8ea3a32b0)
  after our import — nothing was removed locally), no local hand-editing — so `govsync`
  correctly refused to overwrite it (`blocked: 1`, no
  `GENERATED FILE` provenance header to match). Governing it would fork the mirror from the
  upstream project it exists to track. Encoded as a name-based skip in `bin/gov-exempt-repos.sh`'s
  `EXEMPT_REPOS`.

## Hard placement constraints (learned, non-negotiable)

- **Sentinel MUST live at `~/dev/sentinel`, NOT under `~/Documents`.** macOS TCC blocks
  launchd-spawned bash from reading `~/Documents`. This is permanent, not a preference.
- **Postgres:** EnterpriseDB PostgreSQL 18 (`/Library/PostgreSQL/18`), LaunchDaemon
  `com.edb.postgresql-18`, runs as user `postgres` (D-085). Homebrew postgres is RETIRED.
- **Node 24**, pinned via `.nvmrc` + `engines`, selected by `fnm`. Node 26 breaks
  `better-sqlite3` (V8-ABI node-gyp addon). **Do not bump.**

## PORT ALLOCATION — THE single authoritative machine-wide record

**This table is the one authoritative port allocation record for this machine, across every
venture and every system.** Any new service — QB, Ceres, Sentinel tooling, or anything else —
**must be registered here (with a ruling if it collides with an existing row) before it is
assigned a port.** Do not check this document and then start a service anyway if it collides;
do not add a second conflicting entry instead of correcting a superseded one. If disk state
(`lsof`, `docker ps`) disagrees with this table, the table is the defect — fix it here, not by
ignoring it.

**Correction 2026-07-29 (supersedes the prior Quorum Books row below):** the port set
previously ratified for Quorum Books's Qdrant/Neo4j/OpenSearch under OQ-6 (2026-07-29,
vendor-default ports 6333/6334, 7474/7687, 9200/9600) was a documentation-only ruling that
was never checked against the machine's actual running state. Those exact ports were already
bound by Ceres's own Docker containers (`ceres-qdrant`, `ceres-neo4j`, `ceres-opensearch`),
discovered only when Quorum Books's stack was actually built. Sharing Ceres's instances was
evaluated and explicitly REJECTED (no auth/access separation achievable — Ceres's Neo4j is
single-user Community edition, its OpenSearch runs with `DISABLE_SECURITY_PLUGIN=true`, its
Qdrant has no API key). Quorum Books's stores are therefore reassigned to the offset ports
below, as fully separate, isolated Docker containers (Strategist ruling, INF-1/2/3 tranche,
`infra/docker-compose.quorumbooks-stack.yml` / `scripts/quorumbooks-stack.sh` in the
`quorumbooks` repo). The OQ-6 row is corrected in place, not duplicated.

**Correction 2026-08-01 (D-146, superseded same day by D-155 below — left intact as audit
trail):** the separate-container build above was never actually installed before this
ruling landed. JP ruled Quorum Books instead **adopts the existing running Ceres containers
as a co-tenant substrate**, isolating by namespace (Qdrant `qb_`-prefixed collections, a
dedicated Neo4j `qb` database, OpenSearch `qb-*`-prefixed indices — Gate 8/C-49), not by a
separate port or process, since these containers have no server-side auth boundary of their
own. The `infra/docker-compose.quorumbooks-stack.yml` separate-container build is confirmed
UNUSED (`docker volume ls` shows zero `quorumbooks-*` volumes as of OPERATION BEDROCK,
2026-08-01) — dead code from the pre-D-146 plan, not yet removed. **The three
`quorumbooks-*` rows below are therefore STALE** (a build that was never run) and the
**`ceres-*` rows are the ones QB actually connects to**, on their existing ports, via the
namespace guards in `packages/shared/src/adapter/{qdrantNamespace,openSearchConnectionConfig}.ts`
and `packages/shared/src/config/neo4jConnectionConfig.ts`. This note is additive per this
file's own preserve-history rule — the 2026-07-29 correction and REJECTED language above are
left intact as audit trail, not deleted; D-146 is what changed, not the 07-29 record of why
sharing looked wrong before Ceres's containers had a viable namespace-isolation code path.

**Correction 2026-08-01 (D-155, same day as D-146 — reverses it; read THIS one as current):**
D-146's co-tenancy premise did not survive contact with two live findings during OPERATION
BEDROCK: (1) Neo4j **Community edition cannot `CREATE DATABASE` at all** — proven live
against a scratch container on the identical image, so the dedicated `qb` database D-146's
Neo4j isolation depended on was never achievable, not merely blocked on a credential grant;
(2) `ceres-opensearch` runs with its security plugin **disabled cluster-wide** and
`ceres-qdrant` has no API key — an unauthenticated raw HTTP call against a ceres index
succeeded live, proving there was no server-side auth boundary on either store, only QB's
own namespace-prefix code. **The `infra/docker-compose.quorumbooks-stack.yml` separate-
container build (the same one D-146 called dead code) is what actually got stood up instead**
— `docker ps` verified 2026-08-01: `quorumbooks-qdrant`/`quorumbooks-neo4j`/
`quorumbooks-opensearch` all `Up ... (healthy)`, own network `quorumbooks-net`, own volumes,
offset host ports, and every service refuses to start without a real credential (Qdrant
rejects no API key, Neo4j rejects `neo4j/neo4j`, OpenSearch runs with its security plugin
ENABLED and a strong admin password — verified live: unauthenticated requests against all
three return `401`, and QB's own credentials do not authenticate against the corresponding
`ceres-*` container on its own port). **The three `quorumbooks-*` rows below are therefore
LIVE, not stale**, and the D-146 "adopted Ceres substrate" rows below are downgraded to
audit trail — QB no longer connects to any `ceres-*` container. The `ceres-*` rows revert to
collision-avoidance-only records, confirmed still healthy and untouched by this change. This
note is additive per this file's own preserve-history rule; the D-146 correction above and
its REJECTED-then-adopted reasoning are left intact, not deleted.

| Service | Port(s) | Bind | Notes |
|---|---|---|---|
| **Quorum Books — dev** | | | |
| web (Vite) | 5173 | — | `strictPort: true` — conflict FAILS LOUD, never relocates |
| admin (Vite) | 5174 | — | `strictPort: true` |
| api | 3001 | — | |
| marketing site (quorumbooks-web, Astro) | 4321 | — | `strictPort: true` (qbw-11, 2026-09-17) — was previously unpinned/unrecorded, the one repo in the fleet without this discipline; 4321 is Astro's own existing default, pinned in `astro.config.mjs` + `vite.server.strictPort` |
| cockpit | 5200 / 5201 | — | UI + paired API process |
| postgres (EDB 18, D-085) | 5432 | — | canonical local Postgres; Docker Postgres is a non-default escape hatch only |
| **Quorum Books — own derived-store stack (D-155, current, live-verified 2026-08-01, reverses D-146)** | | | |
| quorumbooks-qdrant | 6343 (REST) / 6344 (gRPC) | 127.0.0.1 only | LIVE — `docker ps` shows `Up ... (healthy)`, image `qdrant/qdrant:v1.18.2` (not `-unprivileged`, confirmed via `docker inspect`). Refuses to start without `QB_QDRANT_API_KEY` (Keychain-provisioned). Own network `quorumbooks-net`, own volume `quorumbooks-qdrant-data`. `qb_`-prefixed collection namespace (D-105/Gate 8) kept as defense in depth, not the primary boundary anymore. |
| quorumbooks-neo4j | 7484 (HTTP) / 7697 (Bolt) | 127.0.0.1 only | LIVE — `docker ps` shows `Up ... (healthy)`, image `neo4j:2026.06.0`, Community/GPLv3 (C-47). **Targets the default `neo4j` database, not `qb`** (D-155 ruling — Community edition cannot create a second database at all; this container is QB-only, so there is no co-tenant to isolate the default database FROM anymore). `QB_NEO4J_PASSWORD` provisioned in the macOS Keychain; rejects `neo4j/neo4j`. `_tenant`-property tagging (`Neo4jGraphStore`) kept as defense in depth. |
| quorumbooks-opensearch | 9210 (HTTP) / 9610 (perf analyzer) | 127.0.0.1 only | LIVE — `docker ps` shows `Up ... (healthy)`, image `opensearchproject/opensearch:3.7.0`. Security plugin **ENABLED** (unlike ceres' instance) — HTTPS + admin basic auth required; unauthenticated request verified live → `401`. Own volume `quorumbooks-opensearch-data`. `qb-*`-prefixed index template (`qb-search-template`, 0 replicas, single-node) kept as defense in depth. TLS cert is OpenSearch's stock unverifiable demo cert (`CN=node-0.example.com`) — client-side `rejectUnauthorized: false`, scoped to local dev, documented in `packages/shared/src/adapter/openSearchConnectionConfig.ts`; a real cert is production go-live scope. |
| **Quorum Books — adopted Ceres substrate (D-146, SUPERSEDED 2026-08-01 by D-155 above — audit trail only, QB no longer connects to any of these)** | | | |
| ~~quorumbooks-qdrant (adopted)~~ | ~~6333 (REST) / 6334 (gRPC)~~ | — | SUPERSEDED. Was the same container as `ceres-qdrant` below. D-155 proved this co-tenancy had no server-side auth boundary. QB now runs its own `quorumbooks-qdrant` (see live row above). |
| ~~quorumbooks-neo4j (adopted)~~ | ~~7474 (HTTP) / 7687 (Bolt)~~ | — | SUPERSEDED. Was the same container as `ceres-neo4j` below. D-155 proved Neo4j Community cannot create the dedicated `qb` database this row's isolation depended on. QB now runs its own `quorumbooks-neo4j` (see live row above). |
| ~~quorumbooks-opensearch (adopted)~~ | ~~9200 (HTTP) / 9600 (perf analyzer)~~ | — | SUPERSEDED. Was the same container as `ceres-opensearch` below, whose security plugin is disabled cluster-wide — proved live to have no auth boundary. QB now runs its own `quorumbooks-opensearch` (see live row above). |
| **Ceres (separate system — record for collision-avoidance only; do not touch; confirmed still healthy and untouched by D-155, 2026-08-01)** | | | |
| ceres-qdrant | 6333 (REST) / 6334 (gRPC) | 127.0.0.1 only | vendor default; no API key configured |
| ceres-neo4j | 7474 (HTTP) / 7687 (Bolt) | 127.0.0.1 only | vendor default; single-user Community edition |
| ceres-opensearch | 9200 (HTTP) / 9600 (perf analyzer) | 127.0.0.1 only | vendor default; `DISABLE_SECURITY_PLUGIN=true` |
| ceres-open-webui | 8080 | 127.0.0.1 only | |
| ceres-opensearch-dashboards | 5601 | 127.0.0.1 only | |
| **Other** | | | |
| Obsidian Local REST API | 27124 | — | |

General rules that apply to every row above: Vite-family services run `strictPort: true` — a
port conflict must FAIL LOUDLY, never silently relocate. Every datastore above (QB and Ceres
alike) binds **127.0.0.1 only, never 0.0.0.0** — these processes hold sensitive tenant or
operator data (C-42 GLBA Safeguards posture for Quorum Books; governance data for Ceres), and
there is no managed-provider network boundary to inherit instead. Ports are CONFIG, never
hardcoded; credentials are sourced via Keychain (Quorum Books: INF-28 convention,
`packages/shared/src/keychain/`), never a literal default embedded anywhere.

## Historical references — DO NOT "fix"

Dated log entries, completion reports, and Decisions-Log entries that name old paths or the
retired `Clearstory` / `Glass Ledger` codenames are **audit trail (D-086)**. Preserve them
verbatim. Only *live directives* get corrected. Rewriting history to look tidy is a defect.
