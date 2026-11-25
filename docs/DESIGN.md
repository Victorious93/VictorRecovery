# VictorRecovery — Design

Planning document. Labels: [Certain]/[Likely]/[Guessing]; [plan] = a design decision, not a fact. Nothing here has been built or booted.

## 1. Architecture
- **Base:** fork of TeamWin `android_bootable_recovery` (android-12.1) on the `twrp-12.1` minimal manifest for the 7T and Nord; an Android 9 era base for shamu/angler unless a newer build proves stable on their 3.10 kernels [Likely]. Upstream stays a git remote so TWRP fixes can be merged regularly [plan].
- **Layering (keeps our changes rebasable):**
  - `bootable/recovery` fork: small patches only (hooks for pre-flash checks, backup engine, theme) [plan].
  - `vendor/victor/`: our own components as separate binaries [plan]:
    - `vrd` (VictorRecovery daemon): job queue, flash lists, backup engine, rollback state, heat guard, PC link.
    - `vrctl`: CLI used by the GUI, terminal, adb shell and the PC tool.
    - BusyBox 1.36.1 built from upstream source with a current NDK [Certain 1.36.1 is the latest stable].
  - Device trees: one repo per device, `android_device_<oem>_<codename>_victorrecovery`, based on the trees in RESEARCH.md with credits kept [plan].
- **IPC:** GUI talks to `vrd` over a local Unix socket (small JSON protocol); the same API is exposed, authenticated, over USB-net/Wi-Fi to the PC tool and droidcommand-AI [plan].

## 2. UI
- Reuse the TWRP XML theme engine, no new renderer [Likely achievable].
- Palette: background #0B0F1A, primary #00E5FF, accent #FF2BD6; AMOLED mode = pure #000000 background with the same accents [plan].
- Home: Flash, Flash Lists, Backup, Restore, Rescue, Files, Terminal, PC Link, Settings, Manual [plan].
- Status bar: battery %, temperature, active slot (A/B), decryption state, link state [plan].
- Every destructive screen: red banner, exact list of partitions that will change, swipe to confirm [plan].
- Built-in manual: Markdown pages rendered as theme text pages [Guessing on effort].
- Hardware-key navigation for broken touchscreens [plan].

## 3. Backup format (`.vrb`)
- A directory, not one blob: `manifest.json` + chunk files [plan].
- Partition images (boot, dtbo, vbmeta, persist, modem/EFS where present) as raw dumps compressed with **zstd** (level 3 default, 19 optional) [plan]. Super logical partitions skipped by default (ROM can be re-flashed) but selectable [plan].
- /data via tar streams split into ~64 MB chunks, zstd per chunk [plan].
- **Checksums:** SHA-256 per chunk and per image, plus a signed manifest hash; verified on restore and on demand [plan]. zstd frame checksums on too [Certain zstd supports them].
- **Incremental:** content-defined chunking + chunk index; unchanged chunks reference earlier backups [plan]. Memory use on 3 GB-RAM Nexus phones is a risk [Guessing].
- **Encryption (optional):** age-style X25519 recipient key or passphrase (scrypt) [plan]; private key never stored unencrypted on the phone [plan].
- **Targets:** internal storage, USB-OTG, PC/Pi over USB-net (stream to `vr-pc` receiver), Wi-Fi later [plan].
- Heat guard pauses compression above a set temperature [plan].

## 4. Safe flashing and rollback
Pre-flash checks; all must pass, or the screen shows exactly which failed [plan]:
1. SHA-256 of the file matches a known hash (flash list, companion download, or `.sha256` sidecar).
2. Signature check where available (zip signature; ed25519/minisign for our own artifacts).
3. Device check: codename vs zip asserts / image header.
4. Partition check: target exists, image fits, slot known.
5. Battery at least 30 % (configurable) and temperature under the limit.

Automatic actions:
- Before touching boot/dtbo/vbmeta/vendor_boot, save the current images (both slots on A/B) with hashes [plan].
- **A/B (7T, Nord):** flash the inactive slot where possible, mark it active with a limited retry count; if Android never marks the boot successful, the bootloader falls back to the old slot [Likely, AOSP boot_control: https://source.android.com/docs/core/ota/ab]. VictorRecovery reports what happened on next start [plan].
- **A-only (Nexus):** boot-attempt counter stored in a small reserved area (misc partition offset or a file on /cache or /persist) [Guessing]; the companion clears it after a good boot; past N failed attempts recovery offers (or, if enabled, performs) a restore of the saved boot image [plan]. Limitation: the phone has to reach recovery; a hang before that still needs the key combo [Certain].
- Result screens say "flashed and verified on storage", never "boots" [rule].

## 5. Flash lists
- JSON file of ordered steps (`flash_zip`, `flash_image`, `wipe`, `reinject_recovery`, `install_root`, `install_modules`, `reboot`), each with hash, target and conditions; signed when created by the companion or droidcommand-AI [plan].
- Canonical 7T list: ROM → NetHunter kernel → re-inject VictorRecovery → root (Magisk/KernelSU/APatch via RootForge OS) → fleet modules → reboot [plan].
- Queued by dropping the list into `/data/victorrecovery/queue/` (or `/cache/recovery/` with an OpenRecoveryScript shim for compatibility) [plan].
- Dry-run mode prints every check and action without writing [plan].

## 6. NetHunter mode (7T)
- Record the NetHunter kernel: hash of kernel + dtb from the boot image, plus any modules [Likely needed].
- ROM update flow: flash ROM → unpack the new boot → repack with the **saved NetHunter kernel** + new ramdisk (magiskboot-style unpack/repack) → re-inject recovery → re-root [plan]. Old kernel + new ramdisk may break across Android major versions; warn on version jumps [Likely].
- Wipes exclude `/data/local/nhsystem` (chroot) unless "include chroot" is ticked; separate chroot backup option [plan].

## 7. Rescue tools
- Encryption fixes: retry decryption with alternate blob sets; format-data assistant that backs up first [plan].
- Permission/SELinux repair: `restorecon` on /data subtrees with the ROM's file_contexts; fix ownership of one app's data dir [plan].
- Per-app wipe and app freeze by editing `packages.xml`; Android 12+ stores it in binary ABX format, so we need an ABX reader/writer [Likely]. Uninstall system-app updates [plan].
- Bootloop fix without a full wipe: disable all root modules, clear dalvik/cache, roll back the last flash list, freeze the last-installed app [plan].
- EDL-Commander handoff: show the device's EDL entry method and write a handoff file for the PC tool; `reboot edl` only where the bootloader supports it [Guessing per device].

## 8. Heat and charge guard
- Read thermal zones and battery temperature; cap CPU frequency / offline big cores, dim the screen, pause long jobs over a threshold, resume below a lower one [plan].
- Charge limit in recovery through the device's charge-control sysfs where it exists [Guessing per device].

## 9. PC connectivity and remote control
- USB: adb, sideload, shell, MTP, backup pull, inherited from TWRP [Likely].
- USB-net (NCM/RNDIS gadget) for fast backup streams to PC/Pi [Likely].
- Wireless (optional, **off by default**): only after USB pairing that exchanges ed25519 keys, confirmed with a 6-digit PIN or QR code shown in recovery; mutual auth each session; auto-off after a timeout [plan].
- droidcommand-AI/VictorSuite use the same authenticated API; destructive actions still need on-screen confirmation unless Don enables a trusted mode [plan].

## 10. Security
- **One fixed signing key per artifact:** recovery images, companion APK, flash-list/manifest signing, release checksums; each separate and stable [rule].
- Keys kept offline or in GitHub Actions encrypted secrets; never in repos; `.gitignore` keystores; secret scanning on [rule].
- Optional recovery PIN; ADB/MTP can stay off until the PIN is entered [plan].

## 11. Build plan (GitHub Actions)
- Recovery trees need roughly 45 GB+ of disk [Likely; OrangeFox wiki says 45 GB for fox_12.1], which is tight on GitHub-hosted runners [Likely]. Recommended: a self-hosted runner for recovery builds, GitHub-hosted runners for the companion app [plan].
- Per-device workflow: sync manifest → apply patches → build → sign → SHA-256 + signature → upload as a **draft** release (published only after Don approves) [plan].
- CI gates: build OK, image-size limit per device, flash-list schema lint, host unit tests for `vrd` and the backup engine [plan]. Merge only on green CI and Don's approval [rule].
- Release template has a manual "tested on device" checkbox; without it the release is labelled untested [rule].

## 12. Licensing and credits
- TWRP code: Apache-2.0 with some GPL parts [Likely]; keep headers, NOTICE files and authorship [rule].
- Device trees: keep original authors in history and README (TeamWin, Dees_Troy, rany2, rabilgic, mauronofrio, sreeshankark and others) [plan].
- Kernels are GPL-2.0: publish the exact source used for any kernel we ship [rule].
- BusyBox 1.36.1 (GPL-2.0-only): ship the source tarball, `.config`, NDK version and build script with each release [rule]. No ObsidianBox code or binaries [rule].
- No OrangeFox/PBRP code is planned; if any is ever used, follow its licence and credit it [plan].
- Our new code: Apache-2.0 to match TWRP (recommended; open question for Don).
