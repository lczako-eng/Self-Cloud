# Review — `SESSION_HANDOFF_2026-09-12_LOCAL_AI_AND_PRIVATE_NETWORK.md`

*From the remote session that builds JEFFEREY. Per `AGENTS.md`: a proposal on a
branch, not a parallel spec. This reviews the 09-12 handoff against the rest of
the corpus (CANON, the 09-09 handoff, DIGITAL_CONSCIENCE_ARCHITECTURE,
CONSTITUTION_TEMPLATE, and the JEFFEREY connector source). It is deliberately
blunt; the things it praises, it means.*

**Verdict.** The strategic direction is right and the doc is the clearest thing
in either repo. The problem is that it is the tenth architecture document and
this repo still has zero lines of code, while the JEFFEREY repo — which does
have code — does not enforce a single one of the boundaries this doc now treats
as settled. Documentation is outrunning build roughly ten to one. **Nothing
below asks for more design; almost everything asks for less.**

---

## 1. What the doc gets right

**a. "Local AI is not merely a cheaper fallback. It is a privacy boundary."**
This is the best line in the corpus. It converts local inference from a cost
optimization into an architectural requirement, which is the only framing that
survives contact with a competitor who has cheaper cloud inference than you.

**b. "Deterministic policy rather than unrestricted LLM autonomy."** This is the
single line that *tightens* the system rather than expanding it, and it should
be promoted to CANON as a principle in its own right. Everything consequential
— money, disclosure, deletion, egress — should be decided by code the owner can
read, with the model only proposing.

**c. The Jeffrey/Self-Cloud separation, stated plainly and early.** Reaffirms
the owner's 09-12 decision. Good.

**d. The dedup work is the real foundation and the doc buries it.** The 09-09
result — ~80,000 items, size-bucket → partial-hash → full SHA-256, 15,636
redundant copies, ~55 GB, nothing deleted, every copy verified on both sides —
is the *only* part of this corpus proven against reality. It is also exactly the
primitive federation needs: replica placement, bit-rot detection, copy counting
and restore verification are all downstream of a content-addressed inventory,
and you already have one. Say so in the doc. Federation is an *extension of the
dedup work*, not a new subsystem.

---

## 2. Six things to fix before any more architecture

Ordered by probability of actually hurting the owner.

### 2.1 There is an unencrypted, portable, complete copy of the family's life sitting on a desk

09-09 §5 records ~64.5k files / ~240 GB copied to an external SSD. CANON §7
lists encryption-at-rest as CONCEPT ONLY. So the sovereign backup that exists
*today* leaks by being picked up — in a car, at a border (a threat CANON §3
names by name), in a break-in. The kill switch, the gatekeeper and Rosetta are
all irrelevant to that drive.

Of everything in this review, this is the item most likely to actually happen in
the next twelve months, and it is fixed in an afternoon: encrypt the volume
(APFS Encrypted, or LUKS if the box goes Linux), put the passphrase in a
password manager **and** on paper in a safe — not only in the Mac keychain,
because an encrypted drive whose only key died with a laptop is data loss. Do it
while the second copy still exists. Then decide explicitly whether the source
library on the Mac is FileVault-protected; if not, that's the same exposure with
more copies.

### 2.2 The two trust zones are advisory. Nothing enforces them.

In the JEFFEREY connector, `check_access()` and `check_disclosure()` are exposed
to the model as *optional tools it may choose to call*. No data-returning
function calls them internally — `who_am_i`, `tell_story`, the life, money and
profile reads all return content with no gate. `who_am_i(include=...)` takes the
trust level as a **model-supplied argument defaulting to the most permissive
value**, and there is no client identity anywhere in the call path: the same
server answers `claude-raw` and `jefferey` identically. Today a plain Claude
Desktop session — the preset that is supposed to see facts and priorities only —
can call `who_am_i()` and receive the entire private life layer.

The scope system is a calculator the model is politely asked to consult, not a
door. Make it structural before writing more: bind one client identity per
process at startup, wrap every tool registration in a decorator that checks the
required scope and raises on denial, and **delete the `include` / `audience`
parameters** — the ceiling must come from the bound identity, never from an
argument the model picks. Until that exists, the docs should say "planned," not
"two trust zones."

### 2.3 The gatekeeper the whole model rests on does not exist

Line 64 refers to "the gatekeeper/redaction layer" as though naming a component.
`rosetta`, `redact`, `gatekeep`, `egress` return zero hits in any `.py` file in
either repo. And Rosetta as specified (09-09 §3.3) tokenizes *identifiers* and
explicitly "protects identifiers, NOT meaning" — so even once built it does not
make a diary slice or a medical note safe to send, which is precisely what the
trust-zone language implies passing through the door achieves.

Build the smallest real door: **one function every outbound call goes through**,
taking `(client, purpose, payload)`, applying a **field allowlist** rather than a
redaction regex — allowlists fail closed, redaction fails open — and appending
the exact outbound bytes to an egress log. Then one hard rule the allowlist
enforces: categories whose *meaning* is the secret (health, finance, legal,
diary, anything the owner marked private-forever) are never eligible for egress
at any redaction level. That rule is the thing Rosetta structurally cannot give
you.

### 2.4 The Constitution — which is injected into every session — contains an index of the owner's secrets

`DIGITAL_CONSCIENCE_ARCHITECTURE.md` §4.1 says `constitution/*` is injected at
the start of every session **for every agent**. `CONSTITUTION_TEMPLATE.md` §9
asks the owner to write out "Private forever (no agent, no family, no cloud —
ever)." If that ships to Claude or GPT at session start, the frontier vendor
receives the single most sensitive artifact in the system: a curated list of
what this person is hiding, plus their ranked values, their hard rules, and who
matters to them.

Split it at the storage layer, not by convention:
`constitution/public-charter.md` (tone and hard rules expressed as prohibitions
on the *agent's* behaviour — safe for any engine) and
`constitution/private-boundaries.md` (§9, and anything naming a person,
condition or amount), which never crosses the door and is enforced as a
post-retrieval deny-list locally. Amend §4.1 accordingly.

### 2.5 The conscience store can be destroyed by the product's signature feature

`conscience.py::_save()` is a full `write_text()` of the whole store on every
mutation — no temp-file-and-rename, no fsync, no lock — while `_load()` swallows
exceptions and returns the empty default, so a truncated file silently becomes
an empty conscience and the next save overwrites the survivor with nothing.
Three surfaces can hold the file open at once. **The failure trigger is the
headline feature: a solenoid cutting power mid-write.**

About thirty lines: write to `.tmp`, fsync, `os.replace`; take an exclusive
lock; keep the last N snapshots in `conscience.history/`; and on a JSON parse
failure **refuse to start with a loud error** rather than resetting to defaults.
Grants and the audit log should eventually move out of that blob into their own
append-only files — an audit log that lives as a mutable field inside a JSON
document written by the agent it audits is not an audit log.

### 2.6 Don't invent a pooling filesystem — the problems are solved

`ZFS`, `Btrfs`, `SnapRAID`, `mergerfs`, `restic`, `borg`, `kopia`, `scrub`,
`bitrot` appear **nowhere** in either repo. The federation design is being
invented from scratch against fifteen years of prior art. SnapRAID exists
specifically for heterogeneous consumer disks of different sizes that aren't
always online; mergerfs unions mismatched mounts into one namespace;
restic/kopia give content-addressed, encrypted, deduplicated repos with a native
integrity `check` — encryption, dedup, bit-rot detection and
filesystem-agnosticism in one dependency.

Recommended, and worth writing into CANON so no future session re-litigates it:
`/originals` and `/library` stay **plain files on the primary** (required by the
architecture's own "readable in 30 years with any editor" principle); recycled
drives hold **restic or kopia repositories as replicas**. Read SnapRAID's design
before writing a line of placement code. Note the warning in the same corner of
the market: Perkeep solved "my blobs live across drives that come and go,"
solved it well, and never got traction — because the storage layer was never the
hard part.

---

## 3. Contradictions this document creates with the rest of the corpus

1. **JEFFEREY holds the broadest key and runs on a cloud model.** The
   `jefferey` preset has twelve scopes including life and money read/write;
   `claude-raw` has three. But JEFFEREY *is* the rented engine wearing a
   directive pack — everything it reads lands in Claude's or GPT's context. Under
   this doc's new rule ("frontier cloud models should be treated as guests…
   minimum task-specific context"), that preset is the largest violation of the
   new doctrine, and it is already code. **Pick one and write it down:** either
   trust zones are per-*client* and granted scope is the boundary (in which case
   drop "local vs cloud is a privacy boundary" and say "location is a risk
   factor, not the boundary"), or split JEFFEREY into an on-box component with
   broad scopes and a cloud component restricted to facts/priorities/goals.
   These two models cannot both be true.

2. **Local intelligence is an always-on workload; "intelligence must not outlive
   consent" says it can't be.** Embedding 80k photos is 7–20 hours of background
   work. Face clustering, OCR and transcription are more. Those hours are
   literally "intelligence running in the dark." The defensible resolution is
   available and should be stated rather than discovered at build time:
   *owner-initiated digestion of the owner's own data on the owner's own
   hardware is consented work* — a job the owner starts, that logs what it
   touched, and that stops when the box powers down. Drop the absolutist "no
   background learning" phrasing, which the local-AI direction has now outgrown.

3. **Multi-node federation quietly kills the kill switch.** N machines means N
   power states, and the photos are readable on any of them. A forgotten NAS in
   the basement is a permanent network surface holding the owner's life — which
   defeats the one claim no competitor can copy. Fix with an **authority-node
   rule**: exactly one node holds the keys and the catalog; every federated drive
   holds only encrypted blobs. Then a lost, stolen or forgotten recycled drive
   reveals nothing, killing the authority node still kills the system, and
   recycled drives become *safe* to distribute — which the current design does
   not make them.

4. **Devices "connect like a cloud account," but there is no identity.**
   `check_access(client, scope)` gates on a caller-supplied string. That's fine
   over a single-process stdio pipe and worthless the moment the door is on the
   LAN — any process that says it is `jefferey` gets the full life preset. There
   is no key material, no pairing, no token, no TLS anywhere. Specify enrolment
   **before** any network surface: each device generates a keypair locally,
   pairing requires a code physically displayed by the box (this is where the
   physical-authority thesis actually earns its keep), grants bind to a device
   public-key fingerprint rather than a name, and reachability is WireGuard or
   mTLS only.

5. **No answer for writes made on a phone while the box is off.** The doc
   promises both "a network that can physically cease to exist" and "devices
   connect similarly to a cloud account," with photos as the wedge. But
   `HANDOFF_TO_SELF_CLOUD.md` §6.6 forbids a sync queue and
   `DIGITAL_CONSCIENCE_ARCHITECTURE.md` §7 says the phone is "a window, not a
   copy." Taken literally, every photo shot while the box is off exists in
   exactly one place — strictly worse than the iCloud it replaces. **Split the
   planes:** the *control* plane (reasoning, digestion, Guardian) genuinely stops
   when the box is off; the *data* plane must not. The phone keeps a durable
   local outbox that flushes on reconnect, flushing is data-only and triggers no
   inference, and the owner can see the queue depth ("7 items not yet on your
   Self-Cloud"). Then pick a conflict rule and write it down — there isn't one
   anywhere.

6. **The MVP success criteria omit the owner's own non-negotiable.** "Two
   independent copies plus a *verified restore* before iCloud is ever cancelled"
   is the founder's standing rule and appears in the JEFFEREY handoff §7. The
   09-12 photo-MVP criteria don't mention it. Add it as a gate, not a footnote.

7. **Smaller, but worth a pass:** the hardware roadmap now contradicts CANON's
   Pi-class line (CANON §9 divergence #6 becomes stale); CANON §7 still lists a
   local reasoning model as CONCEPT ONLY and says the connector "deliberately
   rents one instead"; Guardian and Representative are written in future tense
   though they are built and working; "the intelligence is rented, the conscience
   is owned" is now only half true and should be restated deliberately rather
   than left to erode.

---

## 4. Two edits to make in this file today

- **Spelling.** The doc uses "Jeffrey" (single-e) nine times — lines 11 (×3), 68,
  70, 75, 78, 191, 225. CANON §0 and `AGENTS.md` rule 2 both forbid it by name,
  because JEFFEREY matches filed trademark 2454959 and the domain. This is the
  newest and most-imitated document in the repo. `DIGITAL_CONSCIENCE_ARCHITECTURE.md`
  §6/§8 and `README.md` have the same drift; the connector code gets it right
  throughout. Worth a one-line pre-commit grep, since it keeps recurring.
- **Real names in a public repo.** The multi-user example uses "Marcus" and
  "Charmaine." `AGENTS.md` rule 4 is absolute: no private data in this public
  repo, ever — and a named family member attached to a described "private space"
  is personal data. `CONSTITUTION_TEMPLATE.md` line 35 has the same problem.
  Replace with Owner A / Owner B / shared family space, and retire "Marcus"
  entirely — it belongs to the superseded MVP and reads as a real user.

---

## 5. The positioning is the weakest section, and the fix is already written down

Decompose the category claim — "physically owned personal cloud/network with
persistent memory, local intelligence, user-controlled permissions, reusable
storage, and optional guest access for external AI" — and check each clause
against who ships it today:

| Clause | Already occupied by |
|---|---|
| Physically owned personal cloud | Synology, QNAP, UGREEN, TrueNAS, Unraid, Umbrel, CasaOS, Start9 — 15+ years |
| Persistent portable memory | one of the most crowded categories of 2026 (Mem0, Supermemory, MemoryLake, MemSync…) plus first-party memory in Claude and ChatGPT |
| Local intelligence | Immich does local face recognition and CLIP semantic search on owned hardware; StartOS and UmbrelOS ship self-hosted model catalogs; QNAP sells AI-accelerator NAS |
| User-controlled permissions | every NAS |
| Reusable storage | Unraid's 20-year differentiator is *literally* mismatched second-hand disks |
| Optional guest access for external AI | the one clause nobody occupies |

The kill switch is not the moat either: Purism has shipped hardware kill
switches since the Librem 13 and HP shipped webcam switches from 2018. A
solenoid on a power rail is a BOM change any NAS vendor copies in one product
cycle. Worse, the same portfolio describes an always-on box, a WireGuard tunnel,
a morning brief and a Guardian watching transactions — all of which require the
box to be **on**. The customer with a $10/month iCloud bill already owns a free
kill switch: the wall plug. Demote it to a trust proof point mentioned third.
The genuinely hard version — *the box being off must not break the phone
experience* — is the only part worth engineering, and nobody ships it.

**The uncontested claim is already in your corpus, in the wrong document.**
Every memory product in 2026 optimizes for frictionless automatic capture,
because friction kills adoption. Record → Understanding → Constitution does the
opposite *on purpose*: every derived claim carries an `evidence:` link back to an
immutable source, AI writes only into `proposals/`, the owner accepts or
rejects, corrections are never deleted, the Constitution is owner-authored and
sits upstream of the model rather than inside it, silence is a no, and recall
stays off until enough is accepted that an empty conscience can't hallucinate a
personality. That is coherent, unusual, and defensible: **an AI memory you can
audit and disagree with.**

None of it appears in the 09-12 positioning section, which instead lists
infrastructure attributes anyone can copy. Lead with the memory governance:
*the only personal AI memory where nothing is recorded about you until you accept
it, and every claim shows you its evidence.* Then make the demo show a
**rejection and a correction** — not a storage dashboard. And apply the test to
whatever sentence replaces it: name the three products it threatens. If you
can't, it's too vague to be a category.

Related: the family/multi-user section is four lines long and is probably the
real commercial thesis — one appliance replacing four subscriptions, with
separate consciences, is a household purchase rather than a hobbyist one.

---

## 6. What the next two weeks should contain

Nothing on this list is a document.

1. Encrypt the SSD (§2.1). Today.
2. Thirty lines of atomic-write + lock + refuse-on-corrupt in `conscience.py` (§2.5).
3. The structural access gate: bound client identity, decorator on every tool,
   delete the model-supplied visibility arguments (§2.2).
4. The smallest real door: one egress function, field allowlist, egress log (§2.3).
5. Split the Constitution into charter and boundaries (§2.4).
6. Prove the local-AI claim cheaply: a CLIP/SigLIP embedding pass over a few
   thousand photos plus cosine search, on the laptop that already exists — a few
   hundred lines, no purchase, and it converts the independence guarantee from a
   principle into a demo. **Do not buy hardware until tiers A and B are shipped
   and something is measurably slow.**

And one thing to write into the doc rather than build: the local-model job list
should be re-stated as three tiers with the mechanism named for each —
deterministic (indexing, exact dedup, near-dup pHash, timeline), small-model
(semantic search, classification, OCR, retrieval — embeddings, CPU-fine), and
LLM (summarization, Q&A, reasoning). Bundling all nine jobs under "a local
model" is why the doc concludes a GPU may be needed. Eight of them don't need
one. Also: open weights are only independence if **the weights are archived on
the drive** — and be aware the *embedding* model is not replaceable, because
swapping it invalidates the entire index.
