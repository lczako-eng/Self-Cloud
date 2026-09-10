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

---

## B. `⚑` Flags for the owner (not changes — decisions only you can make)

1. **⚑ The patent may already be lost — check today.** CANON §8 records a
   CIPO notice that the *description* document was missing and the
   application is "deemed never to have been filed" unless cured by
   **2026-04-27**. That date is four and a half months gone. If it was cured,
   record the cure date in CANON §8. If it was not, "patent pending" must stop
   being said anywhere (the public orb section on jeffereyai.com currently
   says it) and counsel should advise on refiling. This is the single most
   time-sensitive item in the whole portfolio.

2. **⚑ CANON §8 is candid IP-weakness analysis sitting in a PUBLIC repo.**
   `AGENTS.md` rule 4 says the candid audit lives in the *private* workspace
   — but §8 (the missing-description notice, "at-risk" status, valuation
   ranges, LinkedIn figures) is in the public canon. Application *numbers and
   dates* are fine to publish; the defect analysis and money figures arguably
   are not. Owner's call: move §8's second half to the private repo, keeping
   only the filing index public.

3. **⚑ "Repo consolidation" vs "two products, two repos."** The session handoff
   lists folding all three repos into this one as open work. The owner's
   direction on 2026-09-09 was explicit: *"keep Jefferey and Self-Cloud — they're
   not the same thing; they have two different repos."* Recommend: keep two
   product repos (Self-Cloud, JEFFEREY) and the website; consolidate only the
   scattered *concept* PDFs into this repo. Owner decides.

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
