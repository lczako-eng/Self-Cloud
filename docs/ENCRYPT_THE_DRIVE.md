# Encrypt the Self-Cloud drive — do this before anything else is built

**Status: NOT DONE as of 2026-09-13. This is the highest-probability way this
project hurts its owner in the next twelve months.**

`SESSION_HANDOFF_2026-09-09.md` §5 records ~64.5k files / ~240 GB copied to an
external SSD named "Self Cloud". `CANON.md` §7 lists encryption-at-rest under
CONCEPT ONLY. So the sovereign backup that exists in the world today is a
**plaintext, portable, complete photo archive of a real family**. It leaks by
being picked up — in a car, at a border crossing (a threat CANON §3 names by
name), in a break-in. The kill switch, the gatekeeper and Rosetta are all
irrelevant to that drive.

This is an afternoon of work and no code. Do it before the next architecture
document.

---

## 0. Before you touch anything

**Do not start step 2 until a second copy exists.** Encryption that goes wrong
mid-conversion, on a drive that is the only copy, is exactly the data loss this
whole product exists to prevent. The 09-09 handoff says the source library is
still in iCloud and on the Mac — good. Confirm it is, in writing, before you
begin. Do not cancel iCloud until §5 passes.

## 1. Find out what the drive actually is

```sh
diskutil list                       # find the disk identifier, e.g. disk4s2
diskutil info /Volumes/"Self Cloud" # look at: File System Personality, Encrypted
```

Three cases:

| What it says | What you do |
|---|---|
| **APFS**, Encrypted: No | Step 2a — converts in place, keeps every file |
| **Mac OS Extended (Journaled)** | Step 2a also works (adds CoreStorage encryption) |
| **exFAT** or **FAT32** | Step 2b — these cannot be encrypted in place. Requires an erase, so the second copy is mandatory |

exFAT is the common case for a drive that has ever been plugged into a
Windows machine or bought pre-formatted, and it is also the format that will
later break your checksum manifests (no ownership, second-granularity
timestamps). If it is exFAT, fixing it now solves two problems at once.

## 2a. In-place encryption (APFS or HFS+)

In Finder: right-click the drive → **Encrypt "Self Cloud"…**, set the
passphrase and hint, and let it run. It converts in the background while the
drive stays usable; leave it plugged in until `diskutil info` reports
`Encrypted: Yes` and conversion is complete.

Command line equivalent:

```sh
diskutil apfs encryptVolume /Volumes/"Self Cloud" -user disk
```

## 2b. Erase and recopy (exFAT/FAT32 only — needs the second copy first)

```sh
# 1. Confirm the second copy exists and its checksums verify. Do not skip.
# 2. Erase to encrypted APFS:
diskutil eraseDisk APFS "Self Cloud" GPT disk4        # <- your identifier
diskutil apfs encryptVolume /Volumes/"Self Cloud" -user disk
# 3. Copy back, preserving metadata, and verify:
rsync -aH --progress /path/to/second/copy/ /Volumes/"Self Cloud"/
```

## 3. The passphrase — this is where people actually lose data

- Store it in a password manager (1Password, Bitwarden, Apple Passwords).
- **And on paper, in a safe or a fireproof box.**
- **Not only in the Mac's keychain.** An encrypted drive whose only key lived
  on a laptop that died is not a backup; it is a brick. This is the single
  most common way home encryption turns into data loss.
- If someone else needs to be able to recover this (a spouse, an executor),
  the paper copy is how — and that decision belongs in the Constitution.

## 4. Encrypt the source too

The drive is not the only plaintext copy. Check the Mac itself:

**System Settings → Privacy & Security → FileVault.** If it is off, the same
photos are sitting unencrypted on the laptop, and the laptop leaves the house
far more often than the drive does. Turn it on. Store that recovery key the
same way — manager plus paper.

## 5. Verify, then write it down

Not done until all four pass:

1. Eject the drive, unplug it, plug it back in — macOS must **prompt for the
   passphrase**. If it mounts silently, the key is saved in the keychain and
   the "stolen bag" threat is only half-solved: uncheck "Remember this
   password in my keychain", or accept that the protection is against theft of
   the drive alone, not of the bag containing both.
2. `diskutil info /Volumes/"Self Cloud"` reports `Encrypted: Yes` and
   `FileVault: Yes`.
3. Pull a random hundred files back off it and compare checksums against the
   source — the same verification the 09-09 copy already passed.
4. Record the date here and in `BUILD_JOURNAL.md`, and flip encryption-at-rest
   in `CANON.md` §7 from CONCEPT ONLY to a real, dated capability. It is one of
   the few things on that list that can be true this week.

## 6. What this does and does not protect

- **Does:** a stolen or lost drive is unreadable. A border officer sees noise.
  A drive sold or recycled later carries nothing.
- **Does not:** protect the files while the drive is mounted and unlocked on a
  running Mac. That is what the gatekeeper, the access gate and the kill switch
  are for — and none of them can help a plaintext drive in someone else's hands.

## 7. Then, and only then

The next thing on the list is the **second copy off-site**, per
`CANON_ADDENDA_2026-09-10.md` §A1: two independent copies plus a verified
restore before iCloud is ever cancelled. Encrypt that one too, at the moment
you make it — a mirror is exactly as portable as the original.
