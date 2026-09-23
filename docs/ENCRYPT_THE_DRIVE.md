# Encrypt the Self-Cloud drive — do this before anything else is built

**Status: ENCRYPTED 2026-09-23 — two verification checks still open.**
Erased to APFS (Encrypted) per §2c; `diskutil info` reports
`Case-sensitive APFS` and `FileVault: Yes`. Still open from §5: the passphrase
prompt on re-plug (§5.1) and the checksum sample once the photographs are back
on the drive (§5.3). The Mac's own FileVault (§4) is unconfirmed. Recorded as
CANON addendum A6.

*(History: NOT DONE as of 2026-09-16, when this was the highest-probability
way the project could hurt its owner.)*

> **2026-09-16 — the drive has been identified, so most of §1 is now settled.**
> It was provisioned as a Self-Cloud drive that day and **renamed from
> `Self Cloud` to `Self-Cloud`**; every path below uses the new name. `diskutil
> info` on the owner's Mac reported:
>
> ```
> File System Personality:   Case-sensitive Journaled HFS+
> ```
>
> with **no `FileVault:` or `Encrypted:` line at all**, which is how a plain
> HFS+ volume that is not CoreStorage presents. So: **not encrypted, and
> encryptable in place.** Take **§2a**. §2b (erase and recopy) does not apply —
> the drive was never exFAT.
>
> **Case-sensitive** is unusual on a Mac but is the better of the two here: it
> cannot silently collide two files whose names differ only in case. Keep it.
> Note it if anything is ever copied *from* this drive to a case-insensitive
> one, where such a pair would collide on arrival.
>
> **HFS+ versus APFS — deferred on 09-16, then reversed on 09-21.** See the
> next block. The 09-16 reasoning was: APFS is better for an SSD and its
> snapshots would give the conscience real point-in-time recovery, but
> converting is a separate operation with its own failure mode, and once a
> volume is CoreStorage-encrypted, converting later means decrypting first.
> With the drive holding the only copy of the photographs, that risk was not
> worth taking for a format. **The premise changed.**

> **2026-09-21 — why Finder offered no Encrypt, and the plan that replaces §2a.**
>
> The owner right-clicked the drive and there was **no "Encrypt…" item** in the
> menu. That is not a fault and it is worth writing down, because the runbook
> sent him to a button that was not there.
>
> `diskutil list disk2` settles what it was *not*:
>
> ```
> /dev/disk2 (external, physical):
>    0:      GUID_partition_scheme      *512.1 GB   disk2
>    1:                        EFI EFI   209.7 MB   disk2s1
>    2:                  Apple_HFS Self-Cloud  511.7 GB   disk2s2
> ```
>
> **GUID, journaled HFS+, writable.** Every precondition for CoreStorage
> encryption is met, so the partition scheme was never the problem.
>
> **The cause: the volume is a Time Machine destination.** It carries
> `Backups.backupdb` and its context menu offers *Back Up Now*. macOS does not
> offer Finder encryption on a volume Time Machine has claimed; it expects you
> to encrypt through Time Machine's own settings instead. **Add this to the
> checks in §1:** if Encrypt is missing from a GUID HFS+ volume, look for
> `Backups.backupdb` before looking for anything else.
>
> **The premise that justified deferring APFS is gone.** On 09-21 the owner
> moved the photo folders (`Petting Zoo`, `Uncle Bill`, `Zavitz`) onto the Mac
> and said he no longer wants the Time Machine backup on this drive. So a
> second copy exists, the drive is nearly empty, and it is about to stop being
> a backup target. **Decision: erase to APFS (Encrypted) — §2c — rather than
> CoreStorage-encrypt HFS+ in place.** A near-empty erase costs minutes and
> buys the format this drive should have had as the product drive.
>
> **One drive, one job.** Before this, a single physical drive was the Mac's
> Time Machine target, the family photo archive, and the Self-Cloud. That is
> not a second copy of anything — if it dies, the backup dies with the thing it
> was backing up. Time Machine gets its own cheap drive. This one is the
> Self-Cloud and nothing else.

`SESSION_HANDOFF_2026-09-09.md` §5 records ~64.5k files / ~240 GB copied to an
external SSD named "Self Cloud" (renamed `Self-Cloud` on 2026-09-16).
`CANON.md` §7 lists encryption-at-rest under
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
diskutil list                   # find the disk identifier, e.g. disk4s2
diskutil info /Volumes/Self-Cloud | grep -i "personality\|encrypted\|filevault"
```

Three cases:

| What it says | What you do |
|---|---|
| **APFS**, Encrypted: No | Step 2a — converts in place, keeps every file |
| **Mac OS Extended / Journaled HFS+** ← *this drive* | Step 2a also works, via CoreStorage. **The command differs — see 2a** |
| **exFAT** or **FAT32** | Step 2b — these cannot be encrypted in place. Requires an erase, so the second copy is mandatory |

If neither an `Encrypted:` nor a `FileVault:` line comes back at all, that is a
plain volume with no CoreStorage: **not encrypted.** Absence is the answer.

**Check the partition scheme separately** — the format line does not carry it,
and `diskutil list external` is useless on a Mac with Xcode installed because
dozens of simulator disk images drown the real drive. Name the disk:

```sh
diskutil list disk2                 # <- the identifier from Device Node
```

Row 0 must say `GUID_partition_scheme`. `Apple_partition_scheme` or
`FDisk_partition_scheme` (MBR) cannot take CoreStorage and force §2b.

**If Finder shows no "Encrypt…" item on a volume that passes both checks, it
is claimed by something.** Look for `Backups.backupdb` and a *Back Up Now*
entry in the same context menu: macOS hides Finder encryption on a Time
Machine destination. Release it first (§2c step 1).

exFAT is the common case for a drive that has ever been plugged into a
Windows machine or bought pre-formatted, and it is also the format that will
later break your checksum manifests (no ownership, second-granularity
timestamps). If it is exFAT, fixing it now solves two problems at once.

## 2a. In-place encryption (APFS or HFS+)

**The Finder route is the same for both formats and is the one to use.**
Right-click the drive → **Encrypt "Self-Cloud"…**, set the passphrase and
hint, and let it run. It converts in the background while the drive stays
usable; leave it plugged in until conversion is complete. On ~240 GB expect
hours, not minutes.

**The command-line equivalent is NOT the same for both**, and an earlier
version of this file got that wrong by offering the APFS command for both
rows. `diskutil apfs` subcommands fail on an HFS+ volume.

```sh
# APFS volumes:
diskutil apfs encryptVolume /Volumes/Self-Cloud -user disk

# HFS+ volumes (this drive) — CoreStorage, and it prompts for the passphrase:
diskutil coreStorage convert /Volumes/Self-Cloud -stdinpassphrase
```

Either way, watch it finish rather than assuming:

```sh
diskutil info /Volumes/Self-Cloud | grep -i "filevault\|encrypt\|conversion"
diskutil coreStorage list            # HFS+: shows conversion progress %
```

## 2b. Erase and recopy (exFAT/FAT32 only — needs the second copy first)

Not applicable to this drive; kept for a future one.

```sh
# 1. Confirm the second copy exists and its checksums verify. Do not skip.
# 2. Erase to encrypted APFS:
diskutil eraseDisk APFS "Self-Cloud" GPT disk4        # <- your identifier
diskutil apfs encryptVolume /Volumes/Self-Cloud -user disk
# 3. Copy back, preserving metadata, and verify:
rsync -aH --progress /path/to/second/copy/ /Volumes/Self-Cloud/
```

**If you erase, you also erase the Self-Cloud provisioning** — the marker,
`.selfcloud/`, the folders and the icon. Re-run `Make this drive a
Self-Cloud.command` from the JEFFEREY repo afterwards. It will mint a **new
drive id**, so any node record keyed to the old one needs updating.

## 2c. Erase to encrypted APFS — the chosen path for this drive (2026-09-21)

Use this when a second copy exists and the drive is empty enough that an erase
is cheaper than a conversion. It is the right end state for an SSD: APFS
snapshots give the conscience store point-in-time recovery, which CoreStorage
over HFS+ does not.

**Do not start until all three are true.** Each one has bitten somebody.

1. The photographs exist somewhere that is not this drive, and you have
   *opened a few of them* there. "I moved them" is not verification.
2. Nothing else on the drive is wanted. `ls -la /Volumes/Self-Cloud` — look at
   every line, including the dot-directories.
3. You accept losing `.selfcloud/` at the top level, which is the **Self-Cloud
   connector's** node record, catalog and audit log for this drive. It is
   rebuildable by re-indexing and the connector mirrors catalogs on the Mac,
   but it is the other half's data and its agent should be told.

**Step 1 — release it from Time Machine.** System Settings → General → Time
Machine → select the drive → **−** → *Forget This Backup Disk*. Confirm:

```sh
tmutil destinationinfo        # must no longer list this drive
```

**Step 2 — erase.** Disk Utility, not the command line: the format dropdown
offers *APFS (Encrypted)* directly, and the CLI needs two steps to get there.

1. Open **Disk Utility**.
2. **View → Show All Devices.** Without this you cannot set the scheme.
3. Select the **top-level physical disk** — the `512.1 GB` row named after the
   manufacturer — **not** the `Self-Cloud` volume indented under it.
4. **Erase**, then set:
   - Name: `Self-Cloud`
   - Format: **APFS (Encrypted)**
   - Scheme: **GUID Partition Map**
5. Set the passphrase and hint. **Now read §3 before clicking Erase** — this
   is the passphrase you cannot lose.
6. Erase. Minutes, not hours, on an empty drive.

**Step 3 — re-provision.** Double-click `Make this drive a
Self-Cloud.command` in the JEFFEREY repo. Folders, marker, cloud icon and the
`Start Self-Cloud.command` launcher come back. **The drive id is new.**

**Step 4 — copy the photographs back** into `library/` on the drive, and
verify a sample opens from the drive before deleting anything anywhere else.

**Step 5 — verify**, per §5. On APFS the check is simple:

```sh
diskutil info /Volumes/Self-Cloud | grep -i "filevault\|encrypted\|personality"
```

`FileVault: Yes` is the line that matters on APFS. Current macOS may print it
**without** an `Encrypted:` line (that is exactly what the owner's Mac showed on
2026-09-23), so do not treat a missing `Encrypted:` as a failure here. Then
eject, unplug, replug: the Mac must **ask for the passphrase**.

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
2. `diskutil info /Volumes/Self-Cloud` reports `FileVault: Yes` (and, on APFS,
   `Encrypted: Yes`). On HFS+, `diskutil coreStorage list` must show
   conversion **complete**, not a percentage.
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
