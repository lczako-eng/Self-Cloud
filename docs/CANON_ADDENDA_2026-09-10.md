# Canon Addenda — 2026-09-10 (proposed; owner to fold into CANON or reject)

*From the remote Claude Code session that builds JEFFEREY
(`lczako-eng/jeffrey-local-butler-ai`). Per `AGENTS.md`: this is a proposal
on a branch, not a parallel spec. `CANON.md` wins; everything below is either
(a) a delta the canon does not yet contain, or (b) a `⚑` flag for the owner.
Nothing here restates what the canon already says.*

---

## A. Deltas — directions the owner gave that CANON.md does not yet hold

### A1. The backup rule, enforced in software (before iCloud is ever cancelled)

The handoff's "own a verified copy BEFORE pruning" is right; the owner's
direction goes further and should be canon:

- **Two independent copies before cancel.** The Self-Cloud drive **plus** a
  second drive kept elsewhere (or an encrypted archive), synced when plugged
  in. 3-2-1: three copies, two media, one off-site.
- **Order:** dedup → back up → **verify a restore** (pull a random hundred
  items back, compare byte-for-byte) → *then* cancel iCloud.
- **Self-Cloud enforces it:** refuse to show "safe to cancel" until the second
  copy is synced *and* the restore test has passed. Consider shipping the
  product as a **pair** of drives.
- **Same-drive originals vs off-site mirror defend different threats:**
  `/originals` guards against *mistakes* (over-aggressive dedup, a bad rename,
  a bug); the mirror guards against *physics* (the drive dying). Both required.
- JEFFEREY's role: the quiet nudge ("your second drive hasn't been plugged in
  for 34 days") and the orb going protective over it.

### A2. The API seam Self-Cloud exposes to JEFFEREY (to be fixed in `SELF_CLOUD_CONTRACT.md`)

Pull-only; Self-Cloud never calls into JEFFEREY. If the box is off, JEFFEREY
reports "unreachable" and stops — no shadow copy. Minimum:

| Capability | Why JEFFEREY needs it |
|---|---|
| `check_access(client, scope)` → allowed/denied + reason | asked before every read/write; deny by default |
| `network_state()` → up / down / degraded | "the drive is off," not a guess — and proof that intelligence stopped |
| `files.list/read/write(path)` under the client's scopes | conscience file, journal, documents to fill |
| `photos.query(dates, people, place, text)` → ids, paths, captions | the diary, "on this day," story-telling |
| `photos.caption(id, text, people)` | the photo-intake question writes back |
| `photos.duplicates()` (read) | tell the owner what dedup found |
| `conscience_path()` | where `/conscience` lives on the drive |
| `audit(entry)` | every access lands in *Self-Cloud's* log, not only JEFFEREY's |

The grant service reference implementation lives in
`jeffrey-local-butler-ai/connector/selfcloud.py`; the canon already notes it
belongs on this side. Once re-homed here, JEFFEREY's copy becomes a thin client.

### A3. The ingestion layer — everything the person already generates

Owner's direction (2026-09-08): *"My other apps should tie into this — workout
schedules, health from your ring, Apple Health, even transactions from
Revolv. This should know everything about you."*

- Sources: **Apple Health** (full export), **wearables** (Oura ring, watch),
  **workout apps**, **transactions** (Revolv, bank CSV), **calendar**, plus the
  photo metadata the index already extracts.
- Start with stable **file exports** (Apple Health XML, bank CSV) — no
  partnership needed; add live APIs where offered. Each integration is its
  own small project; APIs change under you.
- Ingested streams land in **storage**; what enters the *conscience* is the
  owner's choice (intake gate). **Health defaults to owner-only.** Location is
  opt-in and the owner is undecided — design it as per-purpose switches, never
  an always-on trail.
- Once a transaction stream exists, JEFFEREY's Guardian (charge register) is
  fed from real data instead of hand entry.

### A4. The morning review

When JEFFEREY is attached to an engine, the day opens with one review: what's
coming, what he noticed, what needs a decision, what he did (`daily_brief`
exists; this is its delivery by habit — later spoken, in the owner's own
voice). Only while Self-Cloud is powered; nothing runs in the dark.

### A5. The box runs the house, and speaks in the owner's voice (owner direction, 2026-09-12)

> *"Let's bring the local [LLM], and then this product to your house. So a hard
> drive that runs your house. But I want it to be you as the AI — you have the
> option. It could be your voice, your accent, your memories. Unlike Google."*

Recorded in full, with feasibility, in
`jeffrey-local-butler-ai/docs/FOUNDER_DIRECTIONS.md` §4. Three parts that belong
to this side:

- **The local model tiering.** The 09-12 handoff's nine local-AI jobs should be
  restated as three tiers with the mechanism named: **deterministic** (indexing,
  exact dedup, near-dup pHash, timeline), **small-model** (semantic search,
  classification, OCR, conscience retrieval — embeddings, CPU-fine), **LLM**
  (summarization, Q&A, reasoning). Build A and B before buying hardware for C.
  Open weights are independence only if **the weights are archived on the
  drive**; note also that the *embedding* model is not replaceable, since
  swapping it invalidates the whole index.
- **The home-hub layer.** The box is already always-on, on the home network, and
  trusted — that is a home hub. Home Assistant is the obvious substrate (open,
  local-only, same hardware class, Matter/Zigbee/Z-Wave/Thread), so this is an
  integration rather than an invention, and it turns the box from a purchase
  into a fixture. **Hard rule, or it breaks the kill switch:** the house must
  keep working when the box is off — local devices keep local control; what
  stops is the intelligence (routines, voice, automations). "The box is asleep"
  must never mean "the lights don't turn on." That graceful degradation is the
  genuinely hard engineering and nobody in this market ships it.
- **The owner's own voice, by option.** Extends `FOUNDER_DIRECTIONS.md` §1 and
  its five safeguards with two more that only apply once there is a speaker in a
  room: **(6)** a speaker is a room, not a person — anything said aloud in a
  shared space is a disclosure and the rules layer applies, defaulting to
  silence when someone unrecognised is present; **(7)** the voice never answers
  a phone call or a door intercom, because a cloned voice a stranger can reach
  is the exact instrument of the scam this product exists to stop.

---

## B. `⚑` Flags for the owner (not changes — decisions only you can make)

1. **~~⚑~~ Patent — OWNER DECISION 2026-09-12: deferred, not a priority now.**
   CANON §8 records a CIPO notice that the *description* document was missing
   from application 3,301,233, with the application deemed never filed unless
   cured by **2026-04-27** (now passed). The owner has decided not to pursue
   this at present. Recorded as his call; no further action taken.
   **One consequence still open for him:** jeffereyai.com's orb section
   publicly states "Emotional-state interface — patent pending". If the
   application lapsed, that public claim is no longer accurate and should be
   changed (a one-line edit, e.g. to "Emotional-state interface — original
   design, 2025") whenever he says the word. The three trademarks
   (2454959 / 2454960 / 2454961) are unaffected and remain the live IP.

2. **⚑ CANON §8 is candid IP-weakness analysis sitting in a PUBLIC repo.**
   `AGENTS.md` rule 4 says the candid audit lives in the *private* workspace
   — but §8 (the missing-description notice, "at-risk" status, valuation
   ranges, LinkedIn figures) is in the public canon. Application *numbers and
   dates* are fine to publish; the defect analysis and money figures arguably
   are not. Owner's call: move §8's second half to the private repo, keeping
   only the filing index public.

3. **~~⚑~~ Repos — OWNER DECISION 2026-09-12: SETTLED, keep them separate.**
   *"Keep it separate as I've always said."* The 2026-09-09 handoff's open
   task "fold the three scattered repos into this one" is therefore
   **withdrawn**. Standing structure: `Self-Cloud` (this repo — the platform),
   `jeffrey-local-butler-ai` (JEFFEREY — the agent/connector), and
   `Jeffrey-AI-Butler` (the website). Only the scattered *concept PDFs* may be
   consolidated here. The 2026-09-12 handoff independently reaffirms this
   ("Do not collapse Jeffrey into Self-Cloud").

4. **⚑ `claude-opus-5` is a real, current model ID, not a placeholder.** CANON
   §7 and §9 mark it as aspirational "no such model at knowledge cutoff" —
   that reflects the writing engine's cutoff, not reality; the JEFFEREY
   connector's standalone chat targets it deliberately. Suggest removing that
   flag from CANON. (`gpt-4o-mini` in the older MVP is a separate matter.)

5. **⚑ Orb mood count.** CANON §5 says six moods; the public orb has **eight**
   selectable states (calm, charged, happy, curious, annoyed, protective, sad,
   thinking) and `orb_state()` currently *drives* six of them. Both true;
   worth stating precisely so the patent-mapped description matches the site.

6. **⚑ AGENTS rule 5 vs the owner's live instruction.** The owner told the
   remote session "push anything there." AGENTS.md says AIs propose on
   branches and the human merges. This document follows AGENTS (branch
   `claude/canon-addenda`). If the owner prefers direct pushes from trusted
   sessions, amend rule 5; otherwise merging this branch is the owner's click.

---

## C. Cross-references

- JEFFEREY side of the seam: `jeffrey-local-butler-ai/docs/HANDOFF_TO_SELF_CLOUD.md`
- Founder's running directions (owner's words): `jeffrey-local-butler-ai/docs/FOUNDER_DIRECTIONS.md`
- Orb / IP evidence log (commit hashes, trademark numbers): `jeffrey-local-butler-ai/docs/ORB_PRIOR_ART_LOG.md`
