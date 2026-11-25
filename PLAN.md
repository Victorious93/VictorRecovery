# VictorRecovery — Plan

**Status: early scaffolding, no working build.** The repos were created on Nov 24 2026 (Don's yes via Chief of Staff). Only an untested CI skeleton exists. Nothing here claims any build boots. Labels: [Certain] / [Likely] / [Guessing]; [plan] = proposal.
Details: docs/RESEARCH.md, docs/COMPARISON.md, docs/DEVICES.md, docs/DESIGN.md, docs/COMPANION.md.

## Why this can beat TWRP / PBRP / OrangeFox
- hotdogb (7T) has **no maintained recovery**: TWRP lists it as "Unmaintained", OrangeFox marks it unsupported (last beta 2021), and PBRP has no build [Certain].
- None of the three has pre-flash hash, device and partition checks together with automatic boot backup and auto-rollback, saved flash lists, a NetHunter-aware update flow, or backups to a PC or Pi [Likely; see COMPARISON.md].

## Phases

| Phase | Scope | Devices | Effort | Risk |
|---|---|---|---|---|
| 0. Prep | Don says yes to the repos. Confirm the 7T ROM base, NetHunter kernel and boot image (`getprop`, `uname -a`, dump boot_a/boot_b to a PC). Confirm the Nord codename. Set up a self-hosted build runner and signing keys kept offline. | All | 1 wk [Guessing] | Low [Likely] |
| 1. Baseline 7T | TWRP parity on hotdogb: touch UI, flashing, backup/restore, FBE decryption, file manager, terminal, adb sideload, fastbootd. Neon/AMOLED theme. BusyBox 1.36.1. Safe-flash checks, automatic boot backup, A/B rollback. Recovery re-inject. NetHunter-preserving ramdisk injection. Tested first with `fastboot boot`. | 7T | 4–6 wk [Guessing] | **High**: no maintainer, decryption on newer ROM bases [Likely] |
| 2. Workflow | Flash lists and dry run, NetHunter mode (keep kernel and chroot through ROM updates), RootForge OS re-root (Magisk/KernelSU/APatch) and fleet modules, `.vrb` backups (zstd, SHA-256, incremental, optional encryption), USB-net backups to PC/Pi. Companion app v1 (install/update, flash lists, queue and reboot, browse backups). | 7T + Companion | 4–6 wk [Guessing] | Medium [Likely] |
| 3. Nexus | shamu (spare unit first, then daily unit), then angler. Android 9 era base, FDE decryption, heat guard, A-only boot counter and restore. | shamu ×2, angler | 3–5 wk [Guessing] | Medium; angler Medium-High (BLOD, heat) [Likely] |
| 4. Rescue + extras | SELinux and permission repair, per-app wipe, app freeze (packages.xml / ABX), bootloop fix without wipe, EDL-Commander handoff, charge guard, built-in manual, optional wireless PC mode (paired over USB first, off by default) on 7T, plus optional droidcommand-AI/VictorSuite remote. | 7T first, then others | 4–6 wk [Guessing] | Medium; Wi-Fi in recovery is High [Guessing] |
| 5. Nord 5G | Port to avicii (after the codename is confirmed) using the 2026 community tree and PBRP 4.0 tree as references. Shares the OnePlus A/B code from phase 1. | Nord | 2–3 wk [Guessing] | Medium [Likely] |

Release rule: every build goes out as a **draft** release, and is marked "untested" until it has been tested on that device. Merge only when CI is green and Don approves [rule].

## Per-device effort and risk
| Device | Effort | Risk | Main risk |
|---|---|---|---|
| OnePlus 7T (hotdogb) | 4–6 wk + NetHunter work [Guessing] | High [Likely] | FBE on Android 12+ ROM bases; injecting into the NetHunter boot image |
| Nexus 6 (shamu) ×2 | 2–3 wk [Guessing] | Medium [Likely] | Old 3.10 kernel limits the build base; heat |
| Nexus 6P (angler) | 1–2 wk after shamu [Guessing] | Medium-High [Likely] | BLOD/big cores, heat, qseecomd decrypt blobs |
| Nord 5G (avicii?) | 2–3 wk [Guessing] | Medium [Likely] | Codename unconfirmed (avicii vs billie vs ebba) |

## Repos (created Nov 24 2026, all public)
1. `Victorious93/VictorRecovery`: manifest, `vendor/victor` (vrd, vrctl, theme, BusyBox build), docs, CI.
2. `Victorious93/VictorRecovery-Companion`: Android app `com.victorious.recovery`.
3. `Victorious93/android_device_oneplus_hotdogb_victorrecovery`: first device tree. Later `android_device_moto_shamu_victorrecovery`, `android_device_huawei_angler_victorrecovery`, `android_device_oneplus_avicii_victorrecovery`.
4. The recovery fork (`Victorious93/android_bootable_recovery`) would be a **fork** of TeamWin's repo, so it also needs Don's yes.

## Open questions for Don (recommended option first)
1. **Create the repos as proposed?** (a) Yes, all four, private until the first tested build *(recommended)*; (b) yes, public from day one; (c) only VictorRecovery for now; (d) not yet.
2. **Package id `com.victorious.recovery`?** (a) Yes *(recommended)*; (b) `com.victorious93.recovery`; (c) something else.
3. **7T ROM base and NetHunter build:** (a) I read it from the phone over adb (`getprop`, `uname -a`, boot dump) *(recommended)*; (b) Don tells me the ROM name, version and kernel; (c) skip for now and build against OxygenOS 11.
4. **Nord 5G codename:** (a) I check it over adb (`getprop ro.product.device`) *(recommended)*; (b) Don checks Settings → About; (c) assume avicii.
5. **Build runner:** (a) self-hosted runner on Don's PC or a server (about 45 GB+ disk) *(recommended)*; (b) GitHub-hosted runners with cleanup tricks (slow, may fail); (c) build by hand locally.
6. **Licence for our new code:** (a) Apache-2.0, matching TWRP *(recommended)*; (b) GPL-3.0; (c) GPL-2.0.
7. **First test unit for the Nexus phase:** (a) the spare Nexus 6 *(recommended)*; (b) the Nexus 6P; (c) the daily Nexus 6.
8. **Wireless PC mode:** (a) USB-net now, Wi-Fi as a later experiment on 7T/Nord only *(recommended)*; (b) push for Wi-Fi in phase 2; (c) USB only, no wireless.
9. **Auto-rollback behaviour:** (a) automatic on A/B, ask first on Nexus *(recommended)*; (b) always ask; (c) always automatic.

## Decisions recorded (Don, via Chief of Staff, Nov 24 2026)
- Licence: Apache-2.0 for our code (upstream licences kept on forks/imports). [Certain]
- Builds: GitHub-hosted runners. [Certain]
- Nord 5G codename assumed `avicii` (verify on device). [Likely]
- OnePlus 7T ROM/kernel details: to be read over adb; needs the phone on USB, not yet possible from the box. OPEN.
- First Nexus test phone: the spare Nexus 6 (shamu). [Certain]
- PC connectivity: USB networking now; Wi-Fi as a later experiment (off by default, paired over USB first). [Certain]
- Rollback after a failed boot: always automatic. [Certain]
- Package id com.victorious.recovery; repos public: VictorRecovery, VictorRecovery-Companion, android_device_oneplus_hotdogb_victorrecovery, and a fork of TeamWin android_bootable_recovery.
- Repos created on Nov 24 2026 under the GitHub **user** account Victorious93 (not an org): VictorRecovery, VictorRecovery-Companion, android_device_oneplus_hotdogb_victorrecovery (import of rabilgic's tree with history), android_bootable_recovery (fork of TeamWin).
- Phase 0 note: the earlier "self-hosted runner" idea is replaced by GitHub-hosted runners. A full AOSP-based TWRP build needs a lot of disk and may fail on them; see .github/workflows/build-hotdogb.yml.
- Status: nothing is tested on a device; no build is claimed to boot.
