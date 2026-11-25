# VictorRecovery — Devices

Labels as in RESEARCH.md. Effort = rough engineer-weeks for a first test build plus decryption (part-time pace). Risk = chance of a blocked feature or a soft-brick during testing. **Nothing here has been built or booted.**

> WARNING: Every device needs an unlocked bootloader. Testing must always start with `fastboot boot victorrecovery.img` (temporary boot, nothing written) where the device supports it, with a full backup and the current boot image saved on a PC first.

## Summary

| # | Device | Codename | A/B | Recovery location | Dynamic (super) | Tree to start from | Decryption | Wireless in recovery | Effort | Risk |
|---|---|---|---|---|---|---|---|---|---|---|
| 1 | OnePlus 7T | hotdogb [Certain] | Yes [Likely] | In boot ramdisk [Likely] | Yes, fastbootd needed [Certain] | TeamWin sm8150-common (android-11) + rabilgic hotdogb [Certain they exist] | FBE; OOS 11 likely, A12+ ROMs hard [Likely] | USB-net easy [Likely]; Wi-Fi hard [Guessing] | 4-6 wk [Guessing] | High [Likely] |
| 2 | Nexus 6 (x2) | shamu [Certain] | No [Likely] | Dedicated partition [Likely] | No [Likely] | TeamWin android_device_moto_shamu, TWRP 3.7.0_9 base [Certain] | FDE, works on official TWRP for stock [Likely]; crDroid unknown [Guessing] | USB-net plausible [Guessing]; Wi-Fi very hard [Guessing] | 2-3 wk [Guessing] | Medium [Likely] |
| 3 | Nexus 6P | angler [Certain] | No [Likely] | Dedicated partition [Certain: `fastboot flash recovery`] | No [Likely] | TeamWin android_device_huawei_angler, 3.7.0_9 base [Certain] | FDE, needs qseecomd blobs [Certain per changelog] | Same as shamu [Guessing] | 1-2 wk after shamu [Guessing] | Medium-High (BLOD/heat) [Likely] |
| 4 | OnePlus Nord (5G) | avicii, verify [Likely] | Yes [Likely] | In boot ramdisk [Certain per twrp.me] | Yes [Likely] | sreeshankark avicii-teamwin (2026-02) / PBRP 4.0 tree [Certain they exist] | FBE, current trees maintained [Likely] | USB-net easy [Likely]; Wi-Fi hard [Guessing] | 2-3 wk [Guessing] | Medium [Likely] |

## 1. OnePlus 7T (hotdogb), first target
- **Why first:** Don's main device; no maintained TWRP, OrangeFox or PBRP exists for it (TWRP "Unmaintained", OrangeFox unsupported since 2021, no PBRP page) [Certain].
- **Partitions:** boot_a/_b, dtbo_a/_b, vbmeta_a/_b, super (system/vendor/product/odm logical) [Likely]. Recovery lives in the boot ramdisk, so every ROM/OTA/kernel flash removes it unless re-injected [Likely].
- **NetHunter constraint:** Don's Kali NetHunter ROM ships a custom kernel in boot and a chroot in /data [Likely]. VictorRecovery must (a) be injected into the NetHunter boot image's ramdisk without replacing the kernel, (b) keep the kernel byte-identical (hash check before/after), (c) never touch /data/local/nhsystem during wipes unless explicitly asked [design requirement]. Exact kernel, ramdisk format and ROM base must be read from Don's phone (getprop, uname -a, dump of boot_a/boot_b) [Certain this is unknown today].
- **Decryption:** depends on the ROM base. OOS 11 base: probably fine with the existing tree [Likely]. Android 12-14 custom base: needs keymint/gatekeeper blobs from that ROM's vendor [Likely]; may fail [Guessing].
- **Rollback:** A/B retry counters usable [Likely].
- **Wireless:** Wi-Fi in recovery means loading the qca_cld3 module plus firmware from vendor [Guessing]; deferred. USB NCM/RNDIS gadget is the practical path [Likely].
- **Risks:** no maintainer to ask; wrong ramdisk injection gives a bootloop (recoverable by flashing the saved boot image via fastboot) [Likely]; MSM download tool is the last resort (EDL-Commander handoff) [Likely].

## 2. Nexus 6 (shamu) x2
- **Partitions:** classic A-only: boot, recovery, system, userdata, cache [Likely]. Recovery survives ROM flashes [Likely].
- **Build base:** keep the Android 9 era base (official builds are `_9-0`) [Certain]; trying twrp-12.1 on a 3.10 kernel is a separate experiment [Guessing].
- **Decryption:** FDE via qseecom/keymaster blobs [Likely].
- **Heat:** both units run hot (per Don). Heat guard and low-CPU mode (cap big cores, dim screen, pause compression at a set temperature) matter most here [Likely].
- **Rollback:** no slots; use a boot-attempt counter plus auto-restore of the saved boot image from recovery [Guessing, needs a design test].
- **Two units:** the spare shamu is the test mule; the daily one only after the mule passes [recommendation].

## 3. Nexus 6P (angler)
- Same pattern as shamu, different SoC (MSM8994). Units with big-core failure ("BLOD") may need a kernel with big cores disabled; the recovery kernel should allow a 4-core mode [Likely].
- Heat guard essential [Likely].

## 4. OnePlus Nord 5G (avicii, verify)
- **Verify codename first:** avicii (Nord) vs billie (Nord N10 5G) vs ebba (Nord CE 5G) [Likely ambiguity].
- If avicii: the best-maintained trees of all four (PBRP official 4.0 Oct 2025, TWRP 3.7.1_12, community TWRP tree pushed Feb 2026) [Certain] means lowest effort once the 7T work exists (shared OnePlus A/B code) [Likely].
- Recovery in boot means the same re-inject logic as the 7T [Certain/Likely].

## Wireless PC mode feasibility
| Device | USB networking (NCM/RNDIS) | Wi-Fi | Verdict |
|---|---|---|---|
| 7T | Likely; configfs gadget on 4.14 kernel [Likely] | Hard: vendor module + firmware [Guessing] | Phase 4 experiment |
| Nexus 6 | Plausible, older gadget driver [Guessing] | Very hard [Guessing] | USB only |
| Nexus 6P | Plausible [Guessing] | Very hard [Guessing] | USB only |
| Nord | Likely [Likely] | Hard [Guessing] | Phase 4 experiment |

Default for all: wireless **off**; pairing only over USB first (PIN or QR) [design rule].
