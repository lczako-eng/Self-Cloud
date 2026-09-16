# Build Journal

## 2026-09-09 — Claude (with Laszlo)
- Read all 3 repos + concept/IP docs; produced reconciled `CANON.md`, and (private) audit + build plan.
- Established canon decisions: JEFFEREY spelling, nested Self-Cloud → Digital Conscience → JEFFEREY model, sovereignty architecture (local-by-default + gatekeeper + Rosetta + audit + kill-switch).
- Proved the first real feature on a live ~80k-photo library: exact-dedup (15,636 redundant / ~55GB), non-destructive separation, and a checksum-verified sovereign backup to owned hardware.
- Open for next engine: near-dup + semantic categorization pass; Rosetta gatekeeper prototype; repo consolidation.

## 2026-09-10 — Claude (remote session, JEFFEREY repo) with Laszlo
- Read CANON.md, AGENTS.md, SESSION_HANDOFF_2026-09-09.md. Withdrew a planned standalone
  BUILD_DIRECTIONS.md (would have forked the concept); filed only deltas + ⚑ flags in
  `docs/CANON_ADDENDA_2026-09-10.md` on branch `claude/canon-addenda` per AGENTS rule 5.
- Deltas proposed: software-enforced backup rule before iCloud cancel; the Self-Cloud→JEFFEREY
  API seam; the ingestion layer (Apple Health / wearables / transactions / calendar); the
  morning review.
- ⚑ raised to the owner: patent cure deadline 2026-04-27 has passed — confirm status;
  CANON §8's candid IP analysis is in a public repo; "repo consolidation" conflicts with the
  owner's "two products, two repos"; `claude-opus-5` is a real model, not a placeholder.
- JEFFEREY repo updated to point at this canon (`docs/HANDOFF_TO_SELF_CLOUD.md` §1a) and to
  record the three CIPO trademark numbers + patent risk in its evidence log.
- Open for the laptop session: `SELF_CLOUD_CONTRACT.md` answering addenda §A2; re-home the
  grant service from `connector/selfcloud.py`.

## 2026-09-12 — Owner decisions + review of the local-AI handoff (remote Claude session)
- **Owner decided:** repos stay **separate** (Self-Cloud / JEFFEREY / website) — the 09-09 open
  task "repo consolidation" is withdrawn. Patent 3,301,233 is **deferred by the owner**, not a
  priority now; trademarks remain the live IP. Addenda ⚑1 and ⚑3 closed accordingly.
- Read `SESSION_HANDOFF_2026-09-12_LOCAL_AI_AND_PRIVATE_NETWORK.md`,
  `DIGITAL_CONSCIENCE_ARCHITECTURE.md` and `CONSTITUTION_TEMPLATE.md`; ran a seven-lens
  adversarial review of the local-AI/private-network direction (feasibility, security, canon
  contradictions, gaps, recycled storage, market, JEFFEREY impact). Findings summarised to the
  owner; anything he accepts lands as a further addendum, not as edits to CANON.

## 2026-09-12 (later) — Review filed + new owner direction (remote Claude session)
- Filed `docs/REVIEW_OF_2026-09-12_HANDOFF.md` on branch `claude/canon-addenda`: the
  seven-lens review written up. Headline items for the laptop session — the external SSD
  holding ~240 GB of the family's photos is **unencrypted** (fix first); the two trust zones
  are advisory (nothing in the connector calls `check_access` internally, and visibility is a
  model-supplied argument); the gatekeeper/Rosetta layer has no code in either repo;
  `constitution/` is injected into every session and its §9 is an index of the owner's
  secrets; `conscience.py` does a non-atomic whole-file write that a kill-switch mid-write
  destroys; and the federation design should reuse restic/kopia/SnapRAID rather than invent a
  pooling filesystem. Positioning: five of the six clauses in the category claim are occupied
  — the uncontested one is owner-ratified, evidence-linked memory governance, and it's in the
  wrong document.
- **New owner direction (2026-09-12):** the local model on the box, *the box runs the house*
  (home-hub layer), and the AI speaking in the owner's own voice and accent — by option.
  Recorded in the owner's words in `jeffrey-local-butler-ai/docs/FOUNDER_DIRECTIONS.md` §4,
  with the hard rule that **the house must keep working when the box is off** (local devices
  keep local control; only the intelligence stops), and two added voice safeguards: a speaker
  is a room, not a person; and the cloned voice never answers a phone or door intercom.
- Open for the laptop session: `SELF_CLOUD_CONTRACT.md`; re-home the grant service; the six
  items in §6 of the review, none of which is a document.

## 2026-09-13 — Four review items acted on (remote Claude session)
- **Owner said "do it" to the four-item shortlist in §6 of the review.** Three are
  code and are done in `jeffrey-local-butler-ai` (branch
  `claude/substantiation-discussion-8dmu3c`), each with tests in
  `connector/jefferey_chat.py --selftest`:
  1. **The door is structural** — new `connector/access.py`. Client identity is
     bound once per process from `JEFFEREY_CLIENT` and **defaults to the narrow
     key**; every data tool on the MCP and HTTP surfaces carries `@gate(scope)`;
     `who_am_i`/`tell_story` lost their audience parameter entirely (the ceiling
     comes from the key); widening authority needs `JEFFEREY_OWNER_CONSOLE=1`
     while narrowing never does; the HTTP surface now maps token → client so
     per-client keys exist on that path.
  2. **The conscience survives a power cut** — atomic fsynced writes under an
     exclusive lock, 20 snapshots kept, a stale writer refused rather than
     clobbering, and an unreadable store recovers from history or refuses to
     start instead of silently becoming empty.
  3. **Local AI proved cheaply** — `tools/photo_index.py`: content-addressed
     (SHA-256, shared with the dedup work), resumable, unit vectors in SQLite,
     cosine search, weights archived inside the index directory, and a refusal
     if the embedding model does not match the index. `selftest` proves the
     pipeline offline; search *quality* needs one run with real weights on a
     machine that can reach them once (this container cannot).
- **Item 1 is physical and belongs to the laptop:** `docs/ENCRYPT_THE_DRIVE.md`
  is the runbook. The external SSD holding ~240 GB of the family's photos is
  still plaintext. It is the highest-probability harm in the portfolio and it is
  an afternoon of work with no code. Do it before the next architecture document.

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

## 2026-09-14 (remote session) — Handover published; the seam identified
- Read `Self-Cloud-Workspace/HANDOVER.md`. **Correcting the record:** earlier entries from
  this side said Self-Cloud had no code. It does — Connector v0, stdlib-only, 58/58 tests,
  verified on the real 476 GB node with 84,020 entries fingerprint-identical before and
  after. That is the strongest single piece of evidence in the portfolio.
- **Published the matching handover for the other half:**
  `jeffrey-local-butler-ai/HANDOVER.md` (branch `claude/substantiation-discussion-8dmu3c`).
  It carries the full bill of everything outstanding, each item assigned to
  Owner / Self-Cloud / JEFFEREY.
- **⚑ THE SEAM — needs the owner's decision.** Both sides independently built a filesystem
  walker, a SQLite catalog and an MCP server. Proposed: **the connector owns storage**
  (what exists, which drive, is it online) because it is hardened and proven on real
  hardware; **JEFFEREY owns meaning** (EXIF, offline geocoding, CLIP, the recall parser,
  transcription) **and the surfaces**. JEFFEREY's walker retires and reads the connector's
  catalog instead. Two MCP servers is correct and they must not be merged — theirs is the
  storage door, JEFFEREY's is the agent surface, exactly as CANON's two-product split says.
- **The same rule, found twice independently:** their `mirrors/` (an unplugged drive stays
  searchable) and JEFFEREY's `missing_since` (an absent file is marked, never deleted).
  Theirs is the more complete implementation; adopt theirs.
- **Asks of the Self-Cloud agent** (detail in §5 of that handover): fix HANDOVER §5.1 and
  §5.2 first, since anything reading drives inherits those holes; expose the catalog to
  JEFFEREY (a door tool, or a richer `search`/`stat` return); say whether `selfcloud.py`
  should be re-homed and in what shape; and settle whether rule 6.1 ("no third-party
  packages") is absolute or scoped to the sovereignty path — CLIP, whisper and llama.cpp
  are all outside stdlib, including the local models §2.2 requires.
- **Offered to their side:** `tools/recall.py` (a sentence → time + place + meaning, pure
  stdlib, no model), `tools/disc_archive.py` (verbatim ripping that survives damaged
  media), and the shape of `connector/access.py`.
- **Honest disclosure, per their rule 6.4:** nothing on the JEFFEREY side has been
  adversarially audited. Its tests prove it does what was intended — the exact trap their
  handover describes. §7 of that handover names the five places to attack first. Until
  that audit runs, treat JEFFEREY as they treat `connector/`: works, not shippable.

## 2026-09-16 (remote session) — The drive is the product; handover for the Self-Cloud agent
- **Owner:** *"We got the hard drive ready… what kind of program do we have to do through
  this hard drive?"* Also: his own voice instead of his mother's discs; and "ask me about
  these pictures from Afghanistan so it remembers permanently."
- **Filed `docs/HANDOVER_FROM_JEFFEREY.md`** — the part of the JEFFEREY handover the laptop
  agent must act on, plus the **on-drive layout both halves now share**: your `.selfcloud/`
  top level untouched, JEFFEREY under `.selfcloud/jefferey/`, a marker `selfcloud.json`
  (or your `node.json` — **your decision**, §1), `originals/`, `library/`, an owner-facing
  `Self-Cloud/` folder with a launcher that binds every tool to that drive on any Mac, and
  the cloud logo as the Finder icon.
- Built on the JEFFEREY side since the last entry, all tested: adversarial audit (48 raised,
  17 survived, all 5 HIGH fixed with regression tests); `home.py` (every tool asks one place
  where the life lives — drive if present, home folder if not); `provision_drive.py`;
  voice enrolment (consent take first, TTY-only, real deletion, no network imports);
  `reminisce.py` (clusters the photo index into moments, asks about the biggest untold one
  from facts alone, keeps the story verbatim pinned to the photos, "rather not" is final).
- **Asks of the laptop agent, in order:** fix §5.1/§5.2; expose the catalog so JEFFEREY's
  walker can retire; decide the marker; scope rule 6.1; shape for re-homing `selfcloud.py`.

## 2026-09-16 (later, remote session) — The egress door; the owner's START_HERE
- **Owner:** *"Build the egress door — but remember Self-Cloud and JEFFEREY are two
  different builds."* Confirmed and held to: the door is **JEFFEREY's**, because
  JEFFEREY is the only half that ever talks to a rented engine. Self-Cloud never
  leaves the house; its offline switch is its own. No code written on this side.
- **Built in `jeffrey-local-butler-ai` (branch `claude/substantiation-discussion-8dmu3c`):**
  `connector/egress.py`. Every tool on the MCP, HTTP and terminal surfaces is
  registered *through* the door, so a tool with no release entry sends nothing.
  Per-tool field allowlist (fails closed; redaction was rejected because it fails
  open). A hard scan — Luhn-checked cards and SINs, SSNs, `password:`, API-key
  shapes — refuses the whole result and names only the field; the same scan runs
  on what an engine hands *in*, before the tool runs, so a secret can never be
  planted in the conscience and then poison every aggregate result. The log is
  written and fsynced before the send; if it cannot be written, nothing leaves.
  A `door-shut` file (or `JEFFEREY_OFFLINE=1`) refuses everything. The owner's
  report: `What left the house.command`, `python connector/egress.py report
  --full`, and a counts-only `what_left_the_house` tool the engine may call.
- **Found on the way, fixed:** since the lazy-provisioning change, the HTTP
  surface only ever provisioned the process-default key, so every per-token
  client was denied as "never granted". Now the *current* client is provisioned.
- **Filed `START_HERE.md`** in the JEFFEREY repo: the owner's steps in order —
  get the code, prepare the drive (`Make this drive a Self-Cloud.command`, then
  **encrypt it** per `ENCRYPT_THE_DRIVE.md`), put the code on the drive, install,
  photographs, voice, and *see what left the house*. One double-click each.
- **Nothing new asked of the laptop agent.** The five asks in
  `HANDOVER_FROM_JEFFEREY.md` §3 stand, in that order. One note added there: the
  egress log lives under `.selfcloud/jefferey/`, your namespace untouched.

## <next> — Codex
- (your entry here)
