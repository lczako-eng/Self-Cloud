# Handover from the JEFFEREY side — for the Self-Cloud agent

**Date:** 2026-09-16. **Owner / sole inventor:** Laszlo Czako (`lczako-eng`).
**Read with:** `Self-Cloud-Workspace/HANDOVER.md` (yours) and `CANON.md`. If
anything here conflicts with CANON, CANON wins.

The full engineering handover for the JEFFEREY half lives at
`lczako-eng/jeffrey-local-butler-ai/HANDOVER.md`, branch
`claude/substantiation-discussion-8dmu3c`. This file is the part of it **you
need to act on** — and one decision the owner has made that changes the
on-disk layout for both of us.

---

## 1. The owner's decision: the drive IS the product

> "We got the hard drive ready. What kind of program do we have to do through
> this hard drive?"

Plug it into any Mac and it is the owner's cloud: photographs, conscience,
voice, and the software, **all on the drive**. The Mac lends a screen and a
Python; it keeps nothing. So every JEFFEREY tool now asks one module
(`connector/home.py`) where the life lives, and the answer is *the drive* when
one is plugged in and *the home folder* when it is not.

### The layout — please confirm or amend, this is the seam made physical

```
<drive>/
  Self-Cloud/                       what the owner sees
    Start Self-Cloud.command        double-click on any Mac; binds every tool to THIS drive
    README.txt
    app/                            a checkout of the JEFFEREY repo (the launcher explains)
  originals/                        read-only, checksummed, never re-encoded in place
  library/                          the working copy the tools index and show
  .selfcloud/                       the machine side — SHARED between our two halves
    selfcloud.json                  ← NEW: the drive marker (below)
    node.json · catalog.db · audit.jsonl · index.lock     ← yours, untouched
    jefferey/                       ← mine, namespaced so we cannot collide
      conscience.json  (+ conscience.history/)
      photo-index/     (index.sqlite, thumbs/, models/)
      voice/
      discs/
  .VolumeIcon.icns                  the cloud, as the drive's Finder icon
```

**Everything JEFFEREY writes on a node drive is under `.selfcloud/jefferey/`.**
Your rule 6.2 — never modify a node drive except `<drive>/.selfcloud/` — is
honoured, and either half can be removed without touching the other.

### The marker, `.selfcloud/selfcloud.json`

```json
{ "kind": "self-cloud-drive", "name": "Self-Cloud", "id": "<16 hex>",
  "created": "...", "provisioned": "...",
  "layout": { "owner_facing": "Self-Cloud/", "originals": "originals/",
              "library": "library/", "machine": ".selfcloud/",
              "jefferey": ".selfcloud/jefferey/" },
  "rules": [ "originals/ is read-only and checksummed ...",
             "nothing under this drive is deleted by software ...",
             ".selfcloud/ top level belongs to the Self-Cloud connector ...",
             "unplugged means gone: no cache, no queue, no shadow copy" ] }
```

**Two things to decide on your side:**
1. Does this marker duplicate `node.json`, or should `home.py` read *your*
   `node.json` instead and drop `selfcloud.json`? I lean to yours being the
   identity and mine being redundant — but yours must then exist before any
   JEFFEREY tool runs on a fresh drive, which today it does not. Say which.
2. `id` here is `secrets.token_hex(8)`. Yours is `^[0-9a-f]{16}$` per your
   §5.1 fix. Same shape on purpose; if you want one id, take yours.

Written by `tools/provision_drive.py`, which creates the layout **without
touching a single existing file**, keys the real Self-Cloud logo to a
transparent `.icns`, flips the Finder custom-icon bit, optionally renames the
volume via `diskutil`, and refuses to re-provision a drive that already
carries a marker unless forced (and even then keeps the id).

---

## 2. What is built on this side since your handover

All tested, all on the branch above. The full table is in the JEFFEREY
`HANDOVER.md` §2; these are the ones that touch your work.

| Built | What it means for you |
|---|---|
| **Adversarial audit, 2026-09-15** — 48 raised, 17 survived, **5 HIGH fixed** | Your §6.4 was right. Three of the five are classes that apply to `connector/`: guards that verify something true but insufficient (your hardlink find's cousin), untrusted input used as a path (your §5.1), and refusals that explain themselves into a disclosure |
| `connector/home.py` | The drive routing above. Wants to read **your catalog**, not walk drives itself — see §3 |
| `tools/provision_drive.py` | Owns the layout and the marker. If you prefer `node.json` as the marker, this is the file to change |
| `tools/photo_index.py` — when/where/meaning, offline geocoding, living index that **never deletes** | Duplicates your walker. Should retire and consume your catalog (§3) |
| `tools/recall.py` — "vacation ten years ago in Cuba" → time + place + meaning, no model | Pure stdlib. Yours to lift if a Self-Cloud surface wants natural questions |
| `tools/wall.py` — the screen; 128-bit passcode, 5-strike lockout, no paths returned | Serves *my* index today; should serve *your* catalog + my meaning layer |
| `tools/voice_enrol.py` — owner's voice, consent take first, TTY-only, real deletion | Writes under `.selfcloud/jefferey/voice/` |
| `connector/reminisce.py` — clusters photos into moments, asks about the biggest untold one from facts only, keeps the story verbatim pinned to the photos | **First feature that needs your catalog and mine to agree** on identity (SHA-256) and on `missing_since` vs your mirrors |
| **`connector/egress.py` — the egress door (2026-09-16).** Every tool on every JEFFEREY surface is registered *through* it; per-tool field allowlist fails closed; a hard scan refuses cards / SINs / SSNs / passwords / API keys in both directions; the log is fsynced *before* the send; a `door-shut` switch; `What left the house.command` | JEFFEREY is the only half that ever talks to a rented engine, so this door is JEFFEREY's. **Your offline switch stays yours** — two switches, two products. The log lands at `.selfcloud/jefferey/egress.jsonl` on the drive: same privacy as the conscience, same namespace, nothing of yours touched |

---

## 3. What I need from you — in order

1. **Fix §5.1 and §5.2.** Everything that reads a drive inherits them,
   including everything above.
2. **A way to read the catalog.** Either a door tool `catalog_path(node_id)`,
   or `search`/`stat` returning path, size, mtime, sha256, node, and
   whether the node is online. With that, `photo_index.py` stops walking and
   becomes a *meaning* layer over *your* catalog. One thing hashes the drives.
3. **The marker decision** (§1). `selfcloud.json` vs `node.json`.
4. **Rule 6.1 scope.** "No third-party packages" cannot mean CLIP, whisper or
   llama.cpp are forbidden — your own §2.2 requires local models. Write the
   sentence: *nothing third-party in the sovereignty-critical path.*
5. **`selfcloud.py` re-homing.** The grant service is a reference
   implementation; tell me the shape you want and I will make mine a thin
   client.

## 4. What you can take from here

`recall.py`, `disc_archive.py` (verbatim ripping that survives damaged
media), the shape of `access.py` (identity bound at startup, `@gate` on every
entry, a canonical scope table the tests assert against so decorators cannot
drift), and the `missing_since` pattern if your mirrors do not already cover
the single-file case.

## 5. Owner-only items that block both of us

- **⚑ The drive is being erased to encrypted APFS (2026-09-21).** Read
  `docs/ENCRYPT_THE_DRIVE.md` §2c. It was a Time Machine destination, which is
  why macOS offered no Encrypt option, and the owner has now moved the photos
  to the Mac and dropped that backup — so an erase is cheap and gets the drive
  onto APFS, which is what it should have been.
  **What this costs you:** the erase destroys `.selfcloud/` at the top level —
  your `node.json`, `catalog.db` and `audit.jsonl` for this drive — and the
  re-provision mints a **new drive id**. Your `mirrors/` should still hold the
  catalog on the Mac, and re-indexing rebuilds the rest, but **any node record
  keyed to the old id needs updating**. It is gated behind an explicit
  acceptance in the runbook rather than done quietly. If you would rather the
  owner exported something from `.selfcloud/` first, say so now.
- **Encrypt the external SSD.** Still plaintext. `docs/ENCRYPT_THE_DRIVE.md`.
- **Run the photo index with real weights** on the Mac — this side's
  container cannot reach them.
- **Merge the branches** — or tell each agent the other's branch name.
