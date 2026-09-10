# Digital Conscience — Architecture (v0 design, 2026-09-10)

> How Self-Cloud becomes a *memory that understands you*, stays yours, and grounds any AI that
> acts on your behalf. Conforms to `CANON.md`. Decisions here are settled unless the owner
> changes them; open items are marked `⚑`.

## 0. Decisions in one glance
| Question | Decision |
|---|---|
| Is the conscience a growing PDF? | **No.** Plain text (Markdown + JSON/JSONL) + local vector index + SQLite catalog, on the owner's drive. PDFs are *generated* from it on demand. |
| Where does it live? | **On the Self-Cloud node drive**, under `<drive>/.selfcloud/conscience/`. Never on a vendor server. |
| Who writes it? | Three tiers: the **Record** is immutable evidence; the **Understanding** is AI-*proposed*, owner-*accepted*; the **Constitution** is owner-authored, AI may only propose edits. |
| How does an AI get grounded? | Constitution always loaded + on-demand local recall of relevant facts (with sources) + end-of-session write-back as proposals. |
| Which AI? | **Any.** The door (gatekeeper) is model-agnostic. Local model by default; cloud model as a guest through the redaction hook. |
| Phones? | The phone is a **window**, not a copy: it connects to the owner's own always-on node over LAN / a self-hosted tunnel. |

---

## 1. The three layers

```
<drive>/.selfcloud/
├── catalog.db                 # connector: what exists (paths, sizes, dates, kinds, hashes)
├── audit.jsonl                # every access through the door
└── conscience/
    ├── constitution/          # TIER 3 — owner-authored, highest authority
    │   ├── identity.md        #   who I am, in my words
    │   ├── values.md          #   ranked PRIORITIES (not preferences) + hard rules
    │   ├── boundaries.md      #   private-forever / share-with-family / public
    │   └── CHANGELOG.md       #   append-only; every edit dated + who proposed/ratified
    ├── understanding/         # TIER 2 — AI-proposed, owner-accepted, source-linked
    │   ├── people/<slug>.md   #   who matters, relationship, timeline, evidence links
    │   ├── places/<slug>.md
    │   ├── eras/<yyyy>-<slug>.md   # chapters of a life (e.g. a property, a business, a move)
    │   ├── projects/<slug>.md
    │   ├── patterns.md        #   habits, preferences, recurring themes — each with evidence
    │   ├── corrections.jsonl  #   every time the owner said "no, that's wrong" — never deleted
    │   └── proposals/         #   inbox: facts awaiting the owner's accept/reject
    ├── index/                 #   local embeddings over understanding/ + selected record text
    └── record/                # TIER 1 — pointers only; the raw files stay where they are
        └── sources.jsonl      #   stable ids → catalog paths (photos, mail, diary, docs)
```

**Tier 1 — Record.** The raw life data (photos, mail, diary, documents, voice memos). Immutable.
Already on the drive; the connector catalogs it. Nothing in the conscience *copies* it — it points.

**Tier 2 — Understanding.** Derived knowledge. Every file is dated, and every claim carries an
`evidence:` link to a Record source id. Written as *proposals* by digestion agents; becomes
canonical only when the owner accepts (auto-accept is allowed for low-risk classes — see §3).
Maturity grows with accepted entries: nascent (<10) → developing → established → mature → rich
(the REVOLV Self Cloud spec's ladder, reused).

**Tier 3 — Constitution.** Authored by the owner in their own words. Loaded into *every*
session of *every* agent. AI may file a proposal against it; only the owner ratifies.
Append-only changelog. This is the moral sense of "conscience": it governs behaviour, not just memory.

## 2. Digestion — how AI "pulls off" the data
A local pipeline that turns Record → Understanding, in passes, incrementally and idempotently:

| Source | Local extractor | Produces (as proposals) |
|---|---|---|
| Photos | Curator (Vision: people/pets/places/dates; OCR of receipts) | eras, people co-occurrence, places, receipts→projects |
| Email | local parser (headers + bodies) | people, projects, timeline events, commitments |
| Diary / notes | local text pass | values signals, patterns, eras |
| Voice memos | local speech-to-text | same as diary |
| Documents / receipts | local OCR + keyword rules | projects, finances (owner-gated) |

Rules: **local by default** (a small on-box model or Apple on-device frameworks); the cloud
model is only consulted for hard reasoning over an already-extracted, redacted slice; every
proposal names its evidence; contradictions with existing understanding are flagged, never
silently overwritten; re-running never duplicates.

## 3. Proposal gating (AI proposes, human disposes)
| Class | Examples | Default |
|---|---|---|
| Low-risk factual | a place, a date, a project name, a tag | **auto-accept**, still logged, revocable |
| People & relationships | who someone is, how you relate | **owner review** |
| Health · money · legal · beliefs | anything sensitive | **owner review, never auto** |
| Constitution edits | values, rules, boundaries | **owner ratifies only** |

## 4. The grounding loop — how AI gets pulled back to the foundations
1. **Always-on context:** `constitution/*` (kept to ~1–3 pages) is injected at the start of every
   session for every agent. It is the owner's system prompt, not the vendor's.
2. **Recall on demand:** the gatekeeper exposes `conscience.recall(query, k)` — local semantic
   search over `understanding/` (+ selected Record text) → returns the top facts *with evidence
   links*. Only that slice is injected. If the consuming model is off-box, the slice passes
   through the redaction hook (Rosetta) first.
3. **Write-back:** at session end (or on "remember this"), the agent calls
   `conscience.propose(fact, evidence, class)`; corrections call
   `conscience.correct(entry_id, note)`. Nothing is written canonical by an agent.
4. **Maturity gate:** recall stays off until ≥3 accepted entries exist, so an empty conscience
   never hallucinates a personality.

## 5. Door tools (gatekeeper additions to Connector v0)
Read-only unless stated. All audited. All refused when offline.
- `conscience.constitution()` → the constitution files (always allowed to registered agents)
- `conscience.recall(query, k, scope?)` → facts + evidence
- `conscience.propose(fact, evidence[], class)` → writes to `proposals/` only
- `conscience.correct(entry_id, note)` → appends to `corrections.jsonl`
- `conscience.status()` → maturity, counts, pending proposals
- Owner-only (CLI, not through the door): `accept`, `reject`, `ratify`, `export --pdf`

## 6. Agents & scopes (multi-agent by design)
Every agent has a key and a **scope**: which nodes/paths it may read, whether it may *propose*,
whether it may see `boundaries: private`. Examples: `curator` (system, read photos, propose
low-risk), `dev-assistant` (read code dirs only), `guest-cloud-model` (recall through redaction,
no raw Record), `jeffrey` (optional personality layer, later — separate project, same door).
**Plural consciences:** each family member is a separate `conscience/` root with separate keys;
no blending without explicit authorization (inheritance/transfer per CANON).

## 7. "A drive that creates its own network" — the phone as a window
- **v0 (Mac):** connector + door over a local pipe (stdio). *(Building now.)*
- **v1 (home service):** connector runs always-on on a home box (Mac mini / Raspberry Pi) with
  drives attached; door exposed **only on the owner's LAN** with per-device keys; files reachable
  by protocols the phone already speaks (SMB/WebDAV → Files app); a small local web view for
  search + "ask." The phone streams and caches; the **master lives at home** → the paid cloud
  tier can be dropped.
- **Away:** self-hosted WireGuard tunnel to home. No third party in the path.
- **Kill switch:** power authority on the box → the Self-Cloud disappears from every device at once.
- `⚑` Native iOS client (later): browse nodes, search, ask the conscience; Photos becomes a cache.

## 8. Intelligence layer on the box (optional, local)
A local model (Ollama-class or Apple on-device) performs digestion, tagging, recall and simple
Q&A. Frontier cloud models are guests for hard reasoning over redacted slices. The organizing
intelligence is pluggable; **Jeffrey is one personality that may sit on top — a separate project.**

## 9. Why plain text and not a database-only or PDF store
Readable in 30 years with any editor (inheritance) · diffable and append-only (auditability) ·
model-agnostic (any AI reads text) · portable across drives and decades · PDFs and reports are
generated views, never the source of truth.

## 10. Build sequence
1. Connector v0 — catalog + door (in progress)
2. Curator v0 — first digestion module, photos (in progress)
3. **Conscience v0** — folder scaffold, Constitution template + owner seed, `recall/propose/correct`
   door tools, local embeddings, first digestion pass (photos → eras/people; diary; email)
4. Home service + phone window (v1)
5. Raspberry Pi box + physical kill switch (hardware MVP)
