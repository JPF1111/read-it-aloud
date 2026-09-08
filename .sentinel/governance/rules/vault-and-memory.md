# Venture Law & Ceres — GLOBAL standing orders

## The three layers (never blur them)

| Layer | Home | Role |
|---|---|---|
| **VENTURE LAW** | Obsidian vault | `D-###`, `C-###`, and canonical registers: the human-readable business, compliance, and operator record. |
| **CERES** | Governed local control plane | Classifies, deduplicates, places, persists, and audits governed records. Ceres is not law and does not author policy. |
| **CODE** | Git repositories | Implementation. In-repo canon files are generated or pointers, never competing law. |

**SAME is RETIRED as an active memory or governance component (2026-07-27).** Historical
references remain audit trail; live instructions must not query, write, or depend on SAME.
Operational retirement was completed on 2026-08-03: the former live binary
`~/.local/bin/same`, vault `~/dev/same-vault`, and config `~/.config/same` were removed from
active locations and preserved only as retired evidence under
`~/.local/state/ceres/retired/same/2026-08-03T123600Z/`.
Ceres may index and project governed data, but the canonical human-readable record remains in
its governed Obsidian location. Projections are rebuildable storage surfaces, not second law.

## Write routing — STANDING ORDER

Agents may write code, tests, implementation documentation, narrative session artifacts,
progress notes, handoffs, and close-outs directly. Direct Obsidian writes are limited to named
narrative files that do not create or amend governed records.

Canonical registers, numbered `D-###` rulings, numbered `C-###` compliance records,
authoritative governance state, and any artifact requiring classification, placement,
deduplication, ID assignment, or cross-register integrity must go through Ceres.

For permitted direct narrative writes, the control remains an alarm:
- `~/dev/sentinel/bin/vault-guard.sh` — WARN ≥10MB/file · ALARM ≥100MB/file · WARN ≥5GB total.
- Writes are **append-or-replace of a NAMED file**. Never write in an unbounded loop.
  (A runaway append once grew a file to 434GB. That is now *detectable*, not forbidden.)
- If vault-guard returns exit 2, STOP and flag to JP immediately.

## Ceres governed-read protocol

Ceres is standing memory, not write-only storage. Agents use it before materially relying on
project memory, prior decisions, or assertions about what exists.

- At the start of substantive project work, call Ceres `initialize_context` for the relevant
  namespace/project when a Ceres MCP client or local connector is available.
- Before asserting prior state, prior decisions, whether something exists, or whether work is
  complete/missing, run a Ceres `search`/retrieval query scoped to the project or namespace.
- A negative claim requires two checks: Ceres retrieval plus one appropriate primary probe
  (disk, Git, database, API, or live service). If either path is unavailable, label the claim
  `UNVERIFIED — requires confirmation`.
- If Ceres tools are deferred, load them before making the claim. If Ceres is down, say
  `CERES UNAVAILABLE`, continue only with explicit uncertainty labels, and do not treat
  stale chat memory as authoritative.
- Ceres retrieval does not replace source verification. It tells the agent what the governed
  memory/control plane knows; disk, Git, database, service, and register checks remain the
  primary probes for current implementation state.

## Ceres agent-session receipt protocol

Every Ceres-capable session must prove Ceres use with an auditable receipt, not merely claim it
in chat. The receipt ledger lives in PostgreSQL at `ceres.agent_sessions` and is also reflected
in Ceres audit events.

Required lifecycle:
1. Start with `agent_preflight` or `ceres agent preflight`. It must record agent name, client,
   namespace, health, context snapshot, scoped search query, search count, and SHA-256 receipt.
2. Use the governed-read protocol before state claims and the governed-write/queue protocol for
   durable records.
3. End with `agent_closeout` or `ceres agent closeout`, using status `complete`, `blocked`, or
   `failed`.
4. Run `agent_receipt` or `ceres agent require` before claiming compliance. Close-outs and
   handoffs include the session ID plus close-out receipt SHA-256.

If the receipt tool is unavailable, the agent must say `CERES RECEIPT UNAVAILABLE` and still use
available Ceres read tools. If all Ceres paths are down, the agent must say
`CERES UNAVAILABLE / UNVERIFIED` before relying on memory-derived claims and must route governed
writes to the durable intake queue.

## Ceres governed-write protocol

- Governed writes fail closed unless classification, deduplication, write-gate approval,
  persistence, placement, and audit all succeed.
- If a governed write cannot complete, use the durable intake queue below. Do not bypass Ceres with
  a raw register write.

## Durable intake queue contract

**Operational location:** `~/.local/state/ceres/queue/`

States: `incoming/`, `pending/`, `processing/`, `processed/`, `rejected/`, and
`quarantine/`. Queue contents are operational evidence, never law. This path is local-only,
outside Git repositories and the iCloud vault, mode `0700`; envelopes are mode `0600`.

The filesystem is the intake contract. Managed clients atomically write a complete envelope
to `incoming/` using write-temp + file `fsync` + rename + directory `fsync`. Submission never
depends on the relay, PostgreSQL, Obsidian, Ollama, or any projection. Relay downtime leaves
losslessly landed items unacknowledged until its next sweep.

Every envelope includes:
- `schema_version`, `queue_id`, `created_at`, `producer`, `submission_type`, and `namespace`;
- the full payload, `source_refs`, `classification`, and `sensitivity`;
- a stable intent-derived `idempotency_key` and payload SHA-256;
- encryption state and, after validation, a receipt containing queue ID, timestamp, and
  SHA-256. The submitting client records that receipt in its close-out.

Accepted types include memories, session close-outs, project brain dumps, source documents,
and governed-record proposals. Payloads are not prematurely summarized. Governed proposals
remain drafts until JP ratifies them.

If the Keychain key is available, payloads land encrypted. If unavailable, intake MUST
continue at `0700/0600` with `encryption: "deferred"`; the relay encrypts them on restored key
access and monitoring alarms while any deferred envelope exists. Queue disk use alarms at
75%. Managed submission rejects at 90% with an explicit actionable error.

Drain semantics:
1. The optional LaunchAgent validates landed envelopes, encrypts deferred payloads when
   possible, and issues receipts.
2. Ceres drains deterministically by `(created_at, queue_id)`; this order is also sequential
   `D-###` / `C-###` allocation order.
3. Ceres applies normal schema, sensitivity, classification, deduplication, Qwen review,
   collision-safe ID allocation, placement, persistence, projection, and audit controls.
4. Canonical registers are written only through allowlisted Ceres Obsidian REST append,
   patch, or bounded governed maintenance operations. Agents never write registers,
   PostgreSQL, or projections directly.
5. Successful envelopes move to `processed/` permanently as provenance. Control rejections
   move to `rejected/` with a machine-readable reason. Uncertain integrity failures move to
   `quarantine/`; the Strategist chair owns resolution and monitoring alarms at 24 hours.

Automated drain is permitted. Manual `ceres queue drain` is reserved to the Strategist chair.
Open WebUI and Ollama use the local relay. ChatGPT and Claude browser projects cannot write to
this Mac without a functioning Ceres MCP connector.

## Universal retention and sealed-archive policy

Rotation is mechanical, never discretionary, and never deletes evidence. Sealed archives are
immutable: mode `0444`, SHA-256 recorded in a manifest, Ceres integrity-verified on read, and
preserved by backup. Corrections are new active entries that reference the archived record;
archives are never edited, regenerated, renumbered, or tidied in place.

Every vault register has an active target size of approximately 100KB (raised from 50KB by
JP on 2026-09-08 — the 50KB trigger re-armed within hours on busy days and blocked agent
close-outs constantly; Ceres now rotates to roughly 80% of the trigger and a daily
LaunchAgent runs `ceres retention rotate-due`, so rotation is no longer an agent chore).
The trigger is surface-generic: any canonical vault register over the threshold rotates by its natural safe
unit, not by a named allowlist. ID-keyed registers seal the oldest contiguous ID block.
Chronological registers seal the oldest dated entries. Backlogs seal completed epics/slices
only; open, in-flight, blocked, or active-BRD-referenced work remains active regardless of age.
Near-threshold registers are not rotated early on judgment; the trigger is mechanical.

Every archive has a one-read manifest/index mapping its range to its archive file. Manifest
rows carry `range_type` (`id` or `date`) and `range`; legacy `id_range` values are treated as
`range_type: "id"` for compatibility. The active register carries the range type, range,
wikilink, SHA-256, and seal date at its top. Vault register archives remain in the Obsidian
vault and are legal/compliance evidence without exception.
- **Queue evidence:** prior-quarter `processed/` and `rejected/` envelopes rotate to
  `processed-archive/YYYY-QN/` and `rejected-archive/YYYY-QN/`. Each manifest maps queue ID
  to assigned `D-###` / `C-###` identifier and content hash. `quarantine/` never rotates
  directly; it must resolve to processed or rejected first.

Vault register archives inherit the vault's iCloud synchronization, Git tracking/history,
and normal vault backup. They are excluded only from active working-set greps, never from
sync, Git, backup, graph/search discovery, or Ceres integrity checks. Queue archives remain
outside Git and cloud sync and are explicitly included in Ceres backups.

Monitoring exposes queue depth, oldest item, failures, disk use, deferred encryption,
quarantine age, unarchived processed count, active vault-register sizes against 100KB,
and missing or unreadable archive manifests.

## Cutover evidence — transition lifted 2026-07-28

Both mandatory cutover tests passed with durable evidence:
1. Ceres appended ratified errata to a Decisions Log larger than 150KB without reconstructing
   the whole file, allocating D-114 and D-115 through PostgreSQL serialization.
2. With the relay stopped, both complete encrypted envelopes landed at
   `~/.local/state/ceres/queue/incoming/`; Ceres later issued receipts, drained them FIFO,
   persisted governed memories and audit evidence, placed both register entries, and retained
   both processed envelopes.

The transition rule is therefore lifted. The durable queue contract above is operative.
Side-channel register writes remain prohibited; a queued proposal is still only proposed
until its processed receipt resolves to a placed governed record.

## Anti-sprawl — ONE source, everything else points

Agent instructions must NOT be duplicated across `.claude/`, `.agents/`, `.codex/`, `.air/`,
`AGENTS.md`, and `CLAUDE.md`. **Global law lives at `~/dev/sentinel/governance/`.**
Agent-facing files and `~/.claude/rules/` are pointer/symlink surfaces. Repo-level files carry
only truly repo-specific operational detail and defer to the Agent Constitution.

**If you find yourself writing the same rule into a second file, STOP.** Point at the first one.
