# THE JEFFEREY / SELF-CLOUD CONCEPT CANON

**Status:** Authoritative reconciled specification — single source of truth
**Owner / sole inventor:** Laszlo Czako (Ontario, Canada; GitHub `lczako-eng`; jeffereyai.com)
**Canon date:** 2026-09-09
**Supersedes:** all prior concept PDFs, whitepapers, pitch/valuation docs, and inter-repo handoffs, which remain source material but not the spec.

This document resolves the contradictions across five source bodies (the Self-Cloud repo, the Jeffrey-AI-Butler repo, the jeffrey-local-butler-ai repo, the iCloud "Jefferey" pitch/IP pile, and the iCloud "Digital conscience" + "Anchor box" pile). Where sources conflict, the **most developed and most recent** version is stated as canon and the divergence is flagged inline with `⚑`.

---

## 0. Canon decisions (naming, spelling, deprecated terms)

These are settled. Everything downstream conforms.

| Item | Canonical form | Notes / divergence |
|---|---|---|
| Product-family spelling | **JEFFEREY** (double-e) | `⚑` Correct English "Jeffrey" appears in older code (`jeffrey-local-butler-ai`, `Jeffrey-AI-Butler`, `src/jeffrey.py`, patent spec); the **filed trademark is "JEFFEREY AI" (CIPO 2454959)** and the domain is jeffereyai.com, so the misspelling is the legally-anchored brand and wins. Fix new material to JEFFEREY; do not rename filed IP. |
| Short forms | none — never "Jeff" or "Jeffrey" in new material | `⚑` HANDOFF §8 (2026-09-08, most recent) mandates "JEFFEREY (never Jeff)". |
| "Butler" | **deprecated** | `⚑` HANDOFF §8 mandates "No 'butler' anywhere." The British-butler persona (modeled on Geoffrey, *Fresh Prince*) is legacy 2025 framing; retained only as optional tone, never as category. |
| Category noun | **Personal AI Shadow™** (a.k.a. "Shadow AI", "Sovereign Personal AI") | Trademark filed (CIPO 2454960). |
| Owned-identity layer | **Digital Conscience** | Founder asserts he coined it "years ago"; treat as a product name. |
| Sovereign hardware | **Self-Cloud™** | Trademark filed (CIPO 2454961). |
| Legal entity | none | Copyright/authorship vests in the natural person **Laszlo Czako**; JSON-LD "JEFFEREY AI" is a brand, not an incorporated company. |

**Canonical taglines:** "The intelligence is rented. The conscience is owned." · "Own Your Life. Pass It On." (inheritance framing) · "Governed by physics, not policy." · "One human. One shadow."

---

## 1. The portfolio in one frame

Three products, one thesis. They nest:

```
┌───────────────────────────────────────────────────────────────┐
│  SELF-CLOUD  — owned hardware substrate (storage + power +      │
│                network authority). Owns the data.               │
│   ┌─────────────────────────────────────────────────────────┐  │
│   │ DIGITAL CONSCIENCE — owned identity + decision logic      │ │
│   │   (priorities, values, constitution, life/journal,        │ │
│   │    audit). Portable across engines. NOT the AI.           │ │
│   │   ┌───────────────────────────────────────────────────┐  │ │
│   │   │ JEFFEREY (the connector) — a caretaker agent that   │ │ │
│   │   │  BORROWS a rented LLM (Claude / GPT) to reason over │ │ │
│   │   │  the conscience, then is dismissed. Holds a         │ │ │
│   │   │  revocable key; owns nothing.                        │ │ │
│   │   └───────────────────────────────────────────────────┘  │ │
│   └─────────────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────────────┘
```

**One-line definitions**
- **Self-Cloud** = the person's own drive-plus-computer "box" with a physical kill switch; owns memory; when powered off it has no network presence and "ceases to exist on the network."
- **Digital Conscience** = the owned, model-agnostic layer that permanently remembers and *rationalizes* the person — their ranked value **priorities** (not preferences), constitution rules, life/journal, and audit trail.
- **JEFFEREY (Personal AI Shadow)** = a private, identity-bound agent that plugs a rented reasoning engine into the owned conscience to observe, recommend, and (when permitted) act on the person's behalf. The intelligence is disposable; the conscience persists.

---

## 2. Self-Cloud — the sovereign substrate

**What it is:** a personally owned server-and-storage system that replaces rented cloud (iCloud/Drive) with permanent ownership of hardware, encryption keys, network presence, and system lifespan. `⚑` Category label drifts across docs ("digital ownership framework" vs "personal server and storage system" vs "a hard drive that creates its own network environment"); **canon: a self-owned storage-and-compute box that is the private custody layer for the Digital Conscience.**

**Four-layer build architecture** (Self-Cloud, distinct from the Digital Conscience layers in §4):
1. **Storage Core** — the local SSD holding originals, library, and conscience.
2. **Power Authority Layer** — mechanical/solenoid power control; the seat of physical authority.
3. **Network Presence Layer** — governs whether the box is discoverable/reachable at all.
4. **Owner Control Interface** — a phone or trusted device that summons and dismisses.

`⚑` The High-Level Build Overview says "four layers" but numbers only two in body text; the four above are the reconciled canon.

**Hardware roadmap** (canon = most recent, HANDOFF/SELF_CLOUD.md 2026-09-08):
- **v0:** existing laptop + external SSD (SanDisk-class). Prototype ran on an iMac Late-2013 / Catalina in the earliest MVP.
- **v1:** a **Raspberry Pi 5-class mini-computer + drive**, ~$150–200 above the drive cost, with a Tailscale-class tunnel for optional remote access.
- **v2:** a designed enclosure with a **front-mounted physical switch**.
- `⚑` Early prospectus figures ($600–1,200, 2–8 TB, 50-year lifespan, 5"×3"×1") are aspirational marketing; the Pi-class spec is the engineering line of record.

**On-drive layout (canon):** `/originals` (read-only, checksummed — "originals are sacred") · `/library` · `/conscience`. Storage roughly doubles to keep originals immutable.

**Configurable connectivity posture** (present as a *choice*, not a contradiction):
- **Cloud-like mode:** permanently online, always reachable, convenient.
- **Sovereign mode:** zero-surface, offline, undiscoverable.
`⚑` Some concept PDFs frame it as *never* permanently online while the Engine Network Whitepaper says it *can* be always-on; canon is that both are supported and the owner sets the posture.

---

## 3. The sovereignty / physical-authority model

The defining philosophical and IP claim: **authority is enforced at the physical layer, not by software permissions, policy, or contract.**

- **Physical kill switch:** a mechanical solenoid physically interrupts power to the network interface. Off = no radio signal, no IP address, no listening services, no remote accessibility — "the strongest security posture is non-existence."
- **Not software-bypassable:** cannot be defeated by prompt injection, remote command, or "ignore previous instructions."
- **Named threat model:** border crossings, travel, legal disputes/subpoena exposure, and hostile networks — the box travels dark and is summoned only by deliberate action.
- **Five core principles** (README canon set): existence is a controlled state · ownership replaces rental · memory is hazardous if exposed · authority must be physical, not contractual · **intelligence must not outlive consent**.
- **Consent-bound intelligence:** JEFFEREY operates *only* while Self-Cloud is active — no background learning, no passive observation, no persistence without consent.

---

## 4. Digital Conscience — the owned identity & decision layer

**What it is:** "Personal Cognitive Infrastructure" — a persistent, sovereign, **model-agnostic** layer that permanently remembers and rationalizes an individual across time, devices, and reasoning engines. Explicitly *not* cloud memory, session context, or personalization. **"The reasoning engine is replaceable. The identity layer is not."**

**Four conceptual layers** (Digital Conscience model — do not conflate with Self-Cloud's four layers in §2):
1. **Sovereign Storage Substrate** — local-first vault (physically, this is Self-Cloud).
2. **Identity Archive** — decisions, corrections, preferences, behavioral patterns, trade-offs, declared + inferred values.
3. **Conscience Layer** — the *Rational Hierarchy Engine*; the defining innovation.
4. **Replaceable Reasoning Engine** — the rented LLM (Claude/GPT).

**Core doctrine — priorities, not preferences:** the conscience stores ranked value hierarchies with a context (e.g. *Navigation:* time 0.91 > safety 0.78 > cost 0.43; *Family > Career*; *Safety > Cost*), never "likes blue." **Corrections are the curriculum.**

**Enforceable override (the novelty claim):** the Conscience Layer is an *override control plane (COCP)* that sits **upstream of and superior to** the base LLM. "The base model proposes; the Conscience governs." Loop: classify context → build constraints (**Constitution Rules always dominate**; Priority Graph gives ranked prefs) → generate K candidates → score alignment → reject violations → mutate constraints and re-query up to a retry cap → else safe fallback. Every accept/reject/fallback is logged.

**Four data structures:** Priority Graph (context/value/rank/weight/confidence/evidence/version) · Constitution Rules (id/scope/condition/action/priority-level) · Override Log · Decision Trace.

**Confidence learning math (canon = shipping connector `conscience.py`):**
- reinforce: `conf += (1 − conf) × 0.15`
- contradict: `conf ×= (1 − 0.30)`
- hierarchy flips and resets to `0.5` when confidence drops below `0.25`

**Decision hierarchy (constitution-as-law):** Constitution > explicit session instruction > identity profile > memory/overrides > model default. Cannot be overridden by prompt injection.

**Life layer / journal:** who the person *is* — people, moments, media **by path reference (never copied)** — walled by visibility `private / family / legacy`, so the conscience can "tell your story down the road." Founder's definition: the Digital Conscience is, at root, **the person's journal**, from which priorities, story, and protection grow.

**Plural conscience (v2, most recent architecture):** multiple separately-permissioned, human-derived consciences (owner / family / advisors) with no autonomous cross-blending — enabling intergenerational reasoning continuity ("What would Dad have advised?"). `⚑` Absent from v1; v2 is canon.

`⚑` Two working conscience codebases exist: the older **Digital Conscience MVP** (`jefferey.py`, SQLite + JSONL, OpenAI `gpt-4o-mini`, demo persona "Marcus") and the newer **connector `conscience.py`** (Anthropic-based, the confidence math above). The **connector is canon** for operational behavior; the MVP remains the patent-mapped reference.

---

## 5. JEFFEREY — the Personal AI Shadow (the connector)

**Canonical architecture (2026 pivot, most recent and shipping):** JEFFEREY is **not a model**. It is an **agent/connector** that rides a rented engine. The host LLM "becomes" JEFFEREY by adopting a directive pack; the same owned conscience is served to any engine.

- **Delivery surfaces (all real, in `connector/`):**
  - **MCP server** (`jefferey_mcp.py`) for Claude Desktop / Claude Code.
  - **FastAPI + OpenAPI HTTP server** (`jefferey_http.py`, bearer auth, port 8377) for a Custom GPT via Actions.
  - **Standalone chat** (`jefferey_chat.py`) on the Anthropic SDK.
- **"One conscience, many engines":** override GPT-JEFFEREY on Monday and Claude-JEFFEREY knows why on Tuesday. The conscience is portable; the engine is disposable.
- **Not a competitor** to Claude/ChatGPT/Gemini — a permanent personal layer *above* whichever model is best.
- `⚑` **Legacy architecture (2025):** a standalone, 100%-local British-butler running its own model with an Inner/Outer split. This is superseded — the connector explicitly has "no model to build, host, or train." Retain "Inner/Outer" only as a privacy boundary concept.

**Operational capabilities (the caretaker's job), gated by a three-tier permission model:**

| Tier | Meaning |
|---|---|
| **Observe** | watch, log, learn — default |
| **Recommend** | surface an opportunity or draft; no side effects |
| **Act** | execute — behind per-category spending caps + audit log; **JEFFEREY can never widen his own authority** |

- **Representative:** scam / predatory-billing triage (weighted regex signal catalogues), plain-words tactic naming, HTML/PDF form-filling that **refuses sensitive identifiers** and fills them only by vault reference. Mission-built for the elderly / isolated / overwhelmed.
- **Guardian ("the GoDaddy problem"):** catches money that leaves without asking — auto-renewals, silent price hikes, zombie subs, charge-after-cancel — normalizes merchants, builds dispute packs. **JEFFEREY never moves money.**
- **Opportunity Engine:** proactively scores ideas and *earns the right to interrupt*. Score = `0.20 base + 0.25 (aligns) + 0.20 (goal) + 0.15 (risk) + 0.20 × urgency`; thresholds **≥0.75 interrupt, ≥0.40 brief, else hold.**
- **Orb state:** the display's mood *is* the engine's real internal state — six moods (protective / charged / curious / happy / thinking / calm). "The face and the mind are the same thing."
- **Interview:** the conscience is "drawn out, not filled in" — a 21-question, 4-depth ladder (warm → shape → values → legacy); depth is *earned* by sharing, one question at a time, declines are final.
- **Rules & disclosure:** intake gate plus a precedence engine — a rule naming a specific audience beats "anyone," **deny beats allow, and with no rule at all say nothing ("silence is a no")**. Two permission layers by design: **Self-Cloud KEYS** (which client may touch which storage — owned by Self-Cloud) vs **Conscience RULES** (who may hear what / how to represent — owned by JEFFEREY).

**Vault principle:** JEFFEREY doesn't hold secrets — the OS does. Secrets live in the OS keychain (`keyring`, service `jefferey-vault`), used by reference `vault:<name>`, resolved **locally at write time** (e.g. PDF fill), and **never entering the AI's context**.

**The ownership boundary (canon):** JEFFEREY is **a caretaker holding a revocable key, not the owner** of the data. Self-Cloud owns storage; the conscience owns decision logic. Per-client access presets (deny-by-default): `claude-raw` / `gpt-raw` (facts + priorities + goals only) · `jefferey` (life layer + money, never secret *values*) · `family` (family + media) · `executor` (legacy only).

---

## 6. Adjacent concepts (canon-adjacent, retained)

- **Emotional Orb** — the "living visual organism," JEFFEREY's body/soul (not a logo): morphogenic shape language, tri-accent spectrum (violet/teal/warm-red), behavioral motion engine, an Adaptive Emotional Intensity Slider (1–10), and accessibility modes (elder-safe / no-flashing / medical). The public web orb is engine-driven and carries a "patent-pending" notice. This is treated as the portfolio's single strongest built asset.
- **Digital Inheritance / "digital next-of-kin"** — heir designation + authentication, inactivity/dead-man switch, read-only "conscience preservation mode" so heirs can query a deceased person's values. Integrates the future **AnchorBox** ("Own Your Life. Pass It On.") — the earlier commercial framing of a self-owned inheritance vault with blockchain audit log and remote power toggle. Public build priority **07**.
- **DRACS** (Dialectal Resonance Adaptive Communication System) — adapts delivery (lexicon, pacing, tone) to the user's "identity dialect" while preserving meaning. Bundled into the IP portfolio; has a separate live MVP.
- **Owner-voice direction** — the conscience should eventually speak in the owner's *cloned* voice, on-Self-Cloud only, owner-only playback, consented enrolment, fully logged (five anti-fraud safeguards against the grandparent-scam vector).

---

## 7. Build state — real vs concept (the honest ledger)

**REAL and working (code exists, runs):**
- **The connector** (`jeffrey-local-butler-ai/connector/`): `conscience.py`, `representative.py`, `guardian.py`, `vault.py`, `life.py`, `selfcloud.py`, `interview.py`, `rules.py`; three runnable surfaces (MCP, FastAPI/HTTP, standalone chat); ~52 tools over one store; a **16-section offline self-test** that exercises the full learning loop with no API key. `⚑` `jefferey_chat.py` hard-codes `MODEL="claude-opus-5"` — a placeholder/aspirational string (no such model at knowledge cutoff).
- **The Digital Conscience MVP** (`jefferey.py` + SQLite/JSONL + `constitution.md` + `identity.json`, OpenAI `gpt-4o-mini`): runnable, constitution-governed, explicit-approval memory. `⚑` Uses `gpt-4o-mini`/`gpt-4o` — unrelated to the Rooted project's `gpt-5.4-mini` rule.
- **The marketing website** (jeffereyai.com): full multi-page static site, JSON-LD/SEO, FAQ, privacy page, and the interactive canvas **Neural Orb** (8 selectable moods, poke-to-react).
- **Government IP filings** (see §8): actually submitted and paid.
- **DRACS MVP**: live, external.

**CONCEPT ONLY (specified, not built):**
- Self-Cloud **hardware** and the **mechanical solenoid kill switch** — "design of record, not yet built."
- A from-scratch **local reasoning model** (the connector deliberately rents one instead).
- Production-grade financial execution, autonomous negotiation, car/home control.
- The full digital-inheritance / dead-man-switch protocol; plural-conscience runtime; AnchorBox; owner-voice cloning; encryption-at-rest.
- `⚑` `selfcloud.py` in the connector is a *reference* grant service acknowledged to belong in the (unbuilt) Self-Cloud product — "on the wrong side of the line," to be re-homed.

**Self-admitted overall status:** pre-product — no shipped hardware, no production users, no revenue. The only external traction is LinkedIn name-search volume (~5,449–6,000/week; `⚑` figure varies by doc).

**Public build order 01–07:** Conscience → Priority Learning → Plug-in Layer → Operational AI → Orb → Self-Cloud → Digital Inheritance. Docs claim **01–05 have working code; 06–07 are concept.**

**Business model (staged):** now — connector on the user's own computer, **free** → v0 phone → **Pro** (hosted, always-on sync, paid) → Self-Cloud hardware → on-device models. "Free first. Mission over money."

---

## 8. IP & legal status — reality vs claims

**Actually on file (Canada / CIPO):**
- **Patent application No. 3,301,233** — "Physically Sovereign Artificial Intelligence System with Digital Conscience Architecture." Fee **$241.24 CAD paid 2026-02-06.** `⚑` **A CIPO Commissioner's Notice dated 2026-02-26 flags the required *description* document as MISSING; no filing date is secured, and the application is "deemed never to have been filed" unless cured by 2026-04-27.** Treat patent status as **incomplete/at-risk**, not "granted" or even securely "pending."
- **Three trademarks, all filed 2026-02-07, Nice Class 42 (SaaS featuring AI), standard characters:** **2454959 JEFFEREY AI · 2454960 PERSONAL AI SHADOW · 2454961 SELF-CLOUD.**

**Claimed but NOT corroborated / overstated:**
- `⚑` Patent **count** is stated as 3 / 5 / 8 / "58 claims across 5 inventions" across documents — reality is **one CIPO application** (with the missing description above); "58 claims / 5 inventions" is the *content of the spec*, not five granted patents.
- `⚑` Nearly all strategy/valuation/action-plan docs assume **USPTO** (US fees, §101, Madrid) — the actual filings are **Canadian CIPO**.
- `⚑` "Trademarks filed/pending" was asserted before filing and lists marks (DRACS, THE ORB, taglines) that were **planned, not filed**; only the three above exist.
- `⚑` Copyright year splits **© 2025** (concept corpus, LICENSE, AUTHORS, README) vs **© 2026** (2026 spec + live website). Canon conception year is **2025**, earliest *provable* artifact **2025-11-29** (repo init).
- `⚑` Valuation and deal-close targets vary wildly across same-week docs ($5M–40M vs $50M–150M vs "$1B–10B+"; close "April 1" vs "March 31" vs "Q1 2026") — treat all as unverified founder projections, not canon.
- `⚑` "World's first" / "100% local" marketing conflicts with the connector's "rides Claude and GPT" reality; the site-spec itself flags this as the top credibility risk. **Canon: drop "100% local" and "world's first"** — the accurate claim is *owned conscience + rented intelligence + physical sovereignty.*

**Licensing:** all-rights-reserved custom license; personal non-commercial use only; no commercial use, derivatives, or ownership claims without written consent. The Self-Cloud repo is an explicit **defensive-publication / prior-art instrument**. An **Orb prior-art log** maintains commit-hash timelines and the CA trademark numbers for counsel.

---

## 9. Divergence register (quick reference)

1. **Spelling** — "Jeffrey" (older code/patent) vs **"JEFFEREY"** (canon; matches filed TM + domain).
2. **Category** — butler (deprecated) vs **Personal AI Shadow** (canon).
3. **Architecture** — standalone local model (2025) vs **connector renting Claude/GPT** (canon).
4. **Connectivity** — "never online" vs "always-on capable" → **configurable posture** (canon).
5. **Self-Cloud category** — framework / server / hard-drive → **owned box = custody layer** (canon).
6. **Hardware** — $600–1,200 prospectus vs **Raspberry Pi 5-class ~$150–200-over-drive** (canon).
7. **Conscience code** — Digital Conscience MVP (OpenAI, patent-mapped) vs **connector `conscience.py`** (Anthropic, operational canon).
8. **Patents** — "3/5/8/58 filed" vs **one CIPO app, description missing, cure by 2026-04-27** (reality).
9. **Jurisdiction** — USPTO assumed in strategy docs vs **CIPO** actual.
10. **Copyright year** — 2025 vs 2026 → conception **2025**, provable **2025-11-29** (canon).
11. **Privacy claim** — "100% local" vs "rides Claude/GPT" → **owned conscience + rented intelligence** (canon).
12. **Model string** — `claude-opus-5` / `gpt-4o-mini` are placeholders, not load-bearing.

---

## 10. Canonical one-paragraph statement

**JEFFEREY is a Personal AI Shadow: a private, lifelong agent bonded to one person that plugs a rented reasoning engine (Claude or GPT) into an owned Digital Conscience — the person's permanently-remembered priorities, values, constitution, and life-journal — and acts as their caretaker across observe/recommend/act permissions, never holding their secrets and never widening its own authority. The conscience is portable across engines and outlives any one model; it lives on Self-Cloud, the person's own hardware box whose physical kill switch enforces sovereignty at the power layer, so that when it is off the system has no network presence at all. The intelligence is rented and disposable; the conscience, the memory, and the authority are owned — governed by physics, not policy — and designed to pass to the next generation.**