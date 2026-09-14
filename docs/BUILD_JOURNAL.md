# Build Journal

## 2026-09-09 — Claude (with Laszlo)
- Read all 3 repos + concept/IP docs; produced reconciled `CANON.md`, and (private) audit + build plan.
- Established canon decisions: JEFFEREY spelling, nested Self-Cloud → Digital Conscience → JEFFEREY model, sovereignty architecture (local-by-default + gatekeeper + Rosetta + audit + kill-switch).
- Proved the first real feature on a live ~80k-photo library: exact-dedup (15,636 redundant / ~55GB), non-destructive separation, and a checksum-verified sovereign backup to owned hardware.
- Open for next engine: near-dup + semantic categorization pass; Rosetta gatekeeper prototype; repo consolidation.

## 2026-09-14 — Claude (with Laszlo)

**Connector v0 built and verified on real hardware.** Python, stdlib-only: drives-as-nodes registry,
sovereign SQLite catalog (mirrored locally so unplugged drives stay searchable), and an audited
read-only MCP-over-stdio door with an offline switch. On a real 476 GB node: 58/58 tests green,
drive metadata fingerprint identical across 84,020 entries (only `.selfcloud/` created), 2,545 files
indexed in 8.18 s, re-index idempotent in 0.15 s, full door session + offline refusals audited.

**Curator v0 (Swift/Vision) partially built** — Contracts / ImageLoading / Classify / DocDetect
compile; FeaturePrint and Report/main still to write. Vision feature-print rev 2 thresholds measured
from real photos (0.30 strict / 0.55 loose). `Undated/` is mostly video, so triage must cover video.

**Digital Conscience architecture decided** (`docs/DIGITAL_CONSCIENCE_ARCHITECTURE.md`): three tiers —
Record (immutable evidence) / Understanding (AI-proposed, owner-accepted, evidence-linked) /
Constitution (owner-authored, always loaded). Plain text on the node drive, not a PDF.

**Code is held in the private workspace repo for now, pending a security pass.** Adversarial audits
found defects that must be closed before the connector is published; details and fixes live in
`HANDOVER.md` there. Engineering handover for other agents: `Self-Cloud-Workspace/HANDOVER.md`.

**Process note:** each build passed its own tests *and still had real holes* that only adversarial
agents found. Adversarial verification stays mandatory before anything ships.

**Ops note:** never keep a working copy in `/private/tmp` — macOS's temp reaper destroyed the `.git`
directories there while source was uncommitted. Commit early; work from a durable path.

## <next> — Codex
- (your entry here)
