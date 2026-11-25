# VictorRecovery — Research

Researched 2026-11-17 (ET). Labels: **[Certain]** = read directly from the cited page this session; **[Likely]** = strong inference or well-known community fact not re-verified this session; **[Guessing]** = educated guess, must be verified on the device or in source.

> Nothing in this document claims any build boots. No VictorRecovery build exists yet.

## 1. Base sources

### 1.1 TWRP (TeamWin)
- Latest TWRP release announced on twrp.me is **3.7.1** (post dated 2024-02-21) — https://twrp.me/site/update/2024/02/21/3.7.1-released.html **[Certain]**
- Recovery source: https://github.com/TeamWin/android_bootable_recovery — default branch `android-12.1`, last push 2026-11-02 (GitHub API) **[Certain]**. Licence: Apache-2.0 for most of the AOSP-derived code, with GPL components (e.g. bundled tools) **[Likely]**.
- Minimal build manifest: https://github.com/minimal-manifest-twrp/platform_manifest_twrp_aosp — branches `twrp-11`, `twrp-12.1`, `twrp-14`, `twrp-14.1`; default `twrp-12.1`, last push 2025-05-18 **[Certain]**.
- Older branches (twrp-9.0 / omni android-9.0) are not in that repo's branch list **[Certain]**; they live in the legacy omni-based manifest (https://github.com/minimal-manifest-twrp/platform_manifest_twrp_omni) **[Likely]**. This matters for the Nexus devices (see §2.2).
- Baseline TWRP features (touch UI, zip/img flashing, backup/restore, decryption, file manager, terminal, adb sideload, MTP, OpenRecoveryScript, fastbootd on dynamic-partition devices since 3.5.0) — https://twrp.me/faq/ and release posts **[Likely]** (not every FAQ page re-read this session).

### 1.2 PitchBlack Recovery Project (PBRP)
- Manifest: https://github.com/PitchBlackRecoveryProject/manifest_pb — default branch `android-12.1`, last push 2025-09-11 **[Certain]**.
- Recovery source: https://github.com/PitchBlackRecoveryProject/android_bootable_recovery — `android-12.1`, Apache-2.0 per GitHub, last push 2025-11-20 **[Certain]**.
- Build: `repo init -u https://github.com/PitchBlackRecoveryProject/manifest_pb -b android-12.1`, `lunch pb_<device>-eng`, `mka pbrp` (README) **[Certain]**.
- README key features: native-Android-like UI, new file manager, universal package flasher (zip/ozip/img), MIUI OTA, encryption support, AVB 2.0 disabler, Treble-checker disabler, easy logger, Magisk installer/remover, Magisk recovery, SuperSU installer, system-app remover, password recovery **[Certain]** (README text; README version badge still says 3.1.0 while device builds are 4.0 — README is stale **[Certain]**).
- Credits TWRP (minimal-manifest-twrp) **[Certain]**.

### 1.3 OrangeFox Recovery
- Sources on GitLab: https://gitlab.com/OrangeFox (sync script repo: https://gitlab.com/OrangeFox/sync) **[Certain]** (wiki says sources are on the OrangeFox GitLab).
- Build branches: `fox_12.1` (Android 12+ ROMs, ≥45 GB) and `fox_14.1` (only devices launched with Android 14+, ≥85 GB) — https://wiki.orangefox.tech/en/dev/building **[Certain]**.
- Licence: GPL obligations stressed by the wiki — any patched unofficial build must publish its modified source and trees **[Certain]** (same page). We will honour this even though our base is TWRP.
- Latest release seen via the public API: **R12.0** (beta builds Nov 2026; stable `R12.0_2` for e.g. `infiniti`) — https://api.orangefox.download/v3/releases/ **[Certain]**.
- Feature list (wiki root): Material Design 2 UI, hardware-button GUI (R11.3+), advanced file manager (multi-select, compress/decompress, nano editing, text preview), password protection, themes/accent/fonts/splash, built-in Magisk install, module management for Magisk/APatch/KernelSU, Fox Addons, gesture nav, AromaFM, App Manager, MIUI/Flyme OTA and incremental block OTA in custom ROMs, flashlight, init.d, self-update — https://wiki.orangefox.tech/root **[Certain]** (via search snippet of that page). Changelog also lists "generate digests after backup", checksum checker, EDL reboot, ADB/MTP blocked at boot on some devices — https://wiki.orangefox.tech/changelog **[Certain]**.

### 1.4 BusyBox
- busybox.net: **1.36.1 (stable, 2023-05-19)**; 1.37.0 (2024-09-27) and 1.38.0 (2026-05-13) are labelled *unstable* — https://busybox.net/ **[Certain]**. Plan stays on 1.36.1 as asked; revisit if 1.38.x gets a stable tag.
- GPL-2.0-only: we must ship the exact source + our `.config` + build script for every binary we distribute **[Certain]** (GPL-2.0 §3). No ObsidianBox code is used anywhere **[Certain]** (design rule).

## 2. Per-device availability

### 2.1 OnePlus 7T — `hotdogb`
- Codename **hotdogb** confirmed on twrp.me ("OnePlus 7T (hotdogb)"); `hotdog` is the 7T Pro — https://twrp.me/oneplus/oneplus7t.html, https://twrp.me/Devices/OnePlus/ **[Certain]**.
- Official TWRP status: **"Unmaintained"**, no maintainer — same page **[Certain]**. `https://dl.twrp.me/hotdogb/` returns Not Found **[Certain]**.
- The twrp.me page says individual partitions must be flashed from **fastbootd** → dynamic partitions (super) **[Certain]**.
- Trees: TeamWin `android_device_oneplus_sm8150-common` (branch `android-11`, last push 2023-06-09, has `bootctrl/`, `twrp.dependencies`) — https://github.com/TeamWin/android_device_oneplus_sm8150-common **[Certain]**; community `rabilgic/twrp_device_oneplus_hotdogb` (branch Android-11, 2023-03) **[Certain]**; `mauronofrio/android_device_oneplus_hotdog_unified_TWRP` (2020) **[Certain]**.
- OrangeFox: device entry `hotdog` "7T (Pro)", **supported: false**, last release R11.1_0 beta 2021-07-12 — https://orangefox.download/device/60ec711d4447b54af15acc5b **[Certain]**.
- PBRP: no device page (`/hotdogb/` and `/hotdog/` 404) **[Certain]**.
- Layout: A/B, no dedicated recovery partition (recovery in boot ramdisk), launched Android 10, not Virtual A/B **[Likely]**. SoC SM8150, kernel 4.14 **[Likely]**.
- Decryption: FBE. Old trees decrypt OxygenOS 10/11; Android 12+ custom ROMs often fail without updated keymaster/keymint blobs **[Likely]**.
- Conclusion: no maintained recovery exists for hotdogb today — a real gap VictorRecovery can fill **[Certain]** (from the three sources above).

### 2.2 Motorola Nexus 6 — `shamu` (x2)
- Official TWRP, maintainer Dees_Troy, latest **3.7.0_9-0** — https://twrp.me/motorola/motorolanexus6.html, https://dl.twrp.me/shamu/ **[Certain]**.
- Tree: https://github.com/TeamWin/android_device_moto_shamu (branch `android-5.0`, last push 2016-12) **[Certain]**.
- OrangeFox: `shamu` unsupported, R11.0 beta 2020-11-07 **[Certain]**. PBRP: none **[Certain]**.
- Layout: A-only, dedicated `recovery` partition, no dynamic partitions **[Likely]**. Kernel 3.10, SoC APQ8084 **[Likely]**.
- Decryption: FDE **[Likely]**; crDroid (Android 10+ ports) behaviour unknown **[Guessing]**.
- Builds are on an Android 9 base (`_9-0`) **[Certain]**; moving shamu to twrp-11/12.1 may hit 3.10-kernel incompatibilities **[Guessing]**.

### 2.3 Huawei Nexus 6P — `angler`
- Official TWRP, maintainer Dees_Troy, latest **3.7.0_9-0**, install via `fastboot flash recovery` — https://twrp.me/huawei/huaweinexus6p.html, https://dl.twrp.me/angler/ **[Certain]**.
- Tree: https://github.com/TeamWin/android_device_huawei_angler (`android-6.0`, last push 2022-03) **[Certain]**; changelog mentions decrypt needing a newer qseecomd **[Certain]**.
- OrangeFox: none. PBRP: none **[Certain]**.
- Layout: A-only, dedicated recovery partition, no dynamic partitions **[Likely]**. MSM8994, kernel 3.10, known big-core bootloop issue ("BLOD") and heat **[Likely]**.

### 2.4 OnePlus Nord (5G) — `avicii` (to verify with Don's phone)
- twrp.me lists "OnePlus Nord (avicii)", maintainer rany2, latest **3.7.1_12-0** — https://twrp.me/oneplus/oneplusnord.html, https://dl.twrp.me/avicii/ **[Certain]**.
- Install path is "Install Recovery Ramdisk" + "Fix Recovery Bootloop" → **recovery in boot** (no recovery partition) **[Certain]** (page text).
- PBRP **official 4.0**, 2025-10-15, maintainer Sreeshankar K — https://pitchblackrecovery.com/avicii/ **[Certain]**; tree https://github.com/PitchBlackRecoveryProject/android_device_oneplus_avicii-pbrp **[Certain]**.
- OrangeFox: not in the official device list **[Certain]**; community trees `sreeshankark/android_device_oneplus_avicii-orangefox` (2025-07) and `OrangeFoxRecovery/device_oneplus_avicii-old` (2022) **[Certain]**.
- Latest maintained TWRP-family tree: `sreeshankark/android_device_oneplus_avicii-teamwin` (last push 2026-02-17) **[Certain]**; TeamWin `android_device_oneplus_avicii` (`android-12.1`, 2022-07) **[Certain]**.
- Ambiguity: "Nord 5G" is the original Nord (avicii) in most markets, but the **Nord N10 5G is `billie`** and the **Nord CE 5G is `ebba`** **[Likely]**. Must confirm from Settings > About or `getprop ro.product.device`.
- Layout: A/B, dynamic partitions, launched Android 10, not Virtual A/B; SM7250, kernel 4.19 **[Likely]**. FBE **[Likely]**.

## 3. Feature-relevant facts
- A/B rollback: Android's `boot_control` HAL keeps per-slot `successful`/`retry-count`; the bootloader falls back to the other slot when retries run out without `markBootSuccessful` **[Likely]** (https://source.android.com/docs/core/ota/ab). Only usable on 7T and Nord.
- A-only rollback (Nexus): no slot fallback; needs our own scheme (boot counter in `/misc` or `/persist` + recovery restoring the saved boot image) **[Guessing]** — design in DESIGN.md.
- Wi-Fi in recovery requires the WLAN firmware, kernel module and vendor services in the recovery ramdisk; rarely done in TWRP forks **[Likely]**. USB networking (RNDIS/NCM gadget via configfs) is far easier and gives the PC/Pi a real IP link over the cable **[Likely]**.
- Kali NetHunter on 7T: NetHunter kernel lives in `boot` and the chroot in `/data/local/nhsystem` **[Likely]** (https://www.kali.org/docs/nethunter/). A ROM update overwrites `boot`, so we must re-flash the saved NetHunter kernel after the ROM **[Likely]**.
