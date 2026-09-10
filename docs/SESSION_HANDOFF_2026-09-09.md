# Self-Cloud — Working Session Handoff (2026-09-09)

> A shared working record so multiple AI collaborators (Claude, Codex, others) converge on
> **one** build. The premise of Self-Cloud made literal: *the intelligence is rented, the
> conscience is owned* — this repo is the owned, shared workspace the engines both read from.
>
> **Scope note:** written for a public repo. Deliberately contains no personal data — no
> account numbers, addresses, contents of private files, or access details. Photo figures are
> aggregate proof points from one real library, nothing identifying.

---

## 1. What Self-Cloud is (short form — see the Canon for the full reconciled spec)

A **self-owned digital infrastructure**: you own the hardware, the keys, the network presence,
and — the part nobody else offers — a **physical authority over whether the system exists at
all**. Data and long-term memory live on hardware the owner holds; intelligence is a *summoned
guest*, not a resident. Paired with **Jeffrey** (the summoned reasoning engine) it becomes the
long-term memory + **digital conscience** layer.

## 2. Concept position reached this session

- **Box → layer → connector.** Self-Cloud is not (only) a device you buy; it is a *layer* that
  can turn any drive you already own into a sovereign, AI-addressable store.
- **Where the moat actually is.** The generic "AI connector for files" is becoming commodity
  infrastructure (shipped free by the big labs). The **defensible, ownable** pieces are:
  1. a **physical drive→router connector with a mechanical kill-switch** (reuse old/recycled
     drives into sovereign nodes), and
  2. the **sovereign memory / conscience layer** on top.
- **The core inversion:** memory is owned and permanent; intelligence is temporary and
  summoned; existence is under physical human control. *AI is a guest.*

## 3. The sovereignty architecture — "wrap the AI" without the data leaving

The central hard problem: use a powerful cloud AI you don't own, without your data leaving your
Self-Cloud. Resolution reached — **not one trick, a stack (a dial you can see and close):**

1. **Local by default (~90%).** A local model on the box does the bulk — tagging, dedup, OCR,
   transcription, semantic index. For all of it there is no outbound call, so nothing can leak.
2. **A local gatekeeper = the only door out.** The cloud AI has *no* direct access to the
   drive; it only ever receives what the gatekeeper hands it. This is the "wrapper."
3. **Rosetta at the door.** Identifiers (names, numbers, faces) are tokenized before egress and
   mapped back locally. *Protects identifiers, NOT meaning* — content whose meaning is itself
   the secret must stay local.
4. **Audit log.** Every byte that crosses the door is logged → sovereignty becomes *verifiable*
   ("show me"), not faith.
5. **Kill-switch / local-only mode** = final enforcement: the door is welded shut, a local
   model does the work, there is physically no outbound call.
6. **Horizon (not one-person-buildable yet):** homomorphic / secure-enclave "cloud computes on
   data it cannot read" (cf. Apple Private Cloud Compute). North star, not the plan.

## 4. Go-to-market: lead with the real, felt problem

**The photo / digital-junk problem is the wedge.** Universal, visceral, demonstrable. Sovereignty
is the moat *underneath*, never the opening pitch. Sequence:
`photos → email → documents/receipts → the accreting owned index/conscience`.

## 5. What was actually built & proven this session (the repo's first real feature)

A working, **non-destructive** photo-sovereignty pipeline, demonstrated on one real ~80,000-item
library (~295 GB):

- **Exact-duplicate engine** — deterministic, byte-for-byte (size-bucket → partial-hash →
  full SHA-256 confirm). Result on the test library: **~14,500 duplicate sets, 15,636 redundant
  copies, ~55 GB reclaimable** — every match proven byte-identical, not "similar."
- **Method = AI proposes, human disposes.** Nothing is ever deleted by the tool. Duplicates are
  *separated into their own pile*, preserved. The owner deletes manually if/when they choose.
- **Sovereign backup to owned hardware.** Onto an external SSD (renamed `Self Cloud`), two folders:
  - `Library-Clean/` — the whole library **minus** duplicates, one copy of each unique item,
    organized **Year/Month** from real capture dates. ~64.5k files, ~240 GB.
  - `Duplicates/` — the 15,636 redundant copies, quarantined.
  - **Every file copied AND checksum-verified** (SHA-256 both sides, zero mismatches).
- **Firm principle established:** *own a verified copy BEFORE pruning.* Removal from the source
  (iCloud-synced) library is parked until the owner explicitly approves, and would be done via
  the OS's own trash (recoverable), never by deleting files out of the sealed library package.

**Design lessons worth keeping:**
- Deterministic work (hashing, copying, verifying) is *scripts*, not AI agents.
- Exported originals as plain, app-independent files = portability = sovereignty. (Album/face/edit
  metadata lives in the source app's DB and is a deeper, later migration step.)

## 6. Open work — for collaborators to pick up

- [ ] **Near-duplicate + semantic categorization pass** — perceptual/near-dup detection, plus
      "what is this" tagging (family / trips / receipts / screenshots / documents). *This* is
      where multi-agent AI earns its keep (exact-dedup did not).
- [ ] **The scoped-bridge / Rosetta gatekeeper prototype** — the smallest thing that reasons over
      a local corpus while sending the cloud only scrubbed slices, with an audit log.
- [ ] **Repo consolidation** — fold the three scattered repos into this one per the Build Plan
      (`Self-Cloud` = concept record; `Jeffrey-AI-Butler` = declaration + web; `jeffrey-local-butler-ai`
      = the working connector code).
- [ ] **Attribution/date cleanup** — apply the Attribution Audit (consistent creator + spelling +
      IP claims; reconcile conception dates).

## 7. Companion documents (in this repo)

- `docs/CANON.md` — the reconciled single source of truth for the whole concept.
- `docs/ATTRIBUTION_AUDIT.md` — dates, authorship, IP, and what's missing.
- `docs/BUILD_PLAN.md` — how to make this repo the real buildable home.
- `AGENTS.md` — how multiple AI clients collaborate in here.

*(Canon / Audit / Build Plan are generated from a full read of all existing repos + concept docs
and land alongside this handoff.)*
