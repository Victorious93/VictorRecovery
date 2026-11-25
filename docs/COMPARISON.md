# Feature comparison: TWRP vs PBRP vs OrangeFox vs VictorRecovery (planned)

Sources: T = https://twrp.me (FAQ/release posts); P = PBRP README https://github.com/PitchBlackRecoveryProject/manifest_pb ; O = OrangeFox wiki https://wiki.orangefox.tech/root and /changelog. Every cell carries a label. VictorRecovery cells are **plans**, not shipped features, and nothing has been built or booted.

| Feature | TWRP 3.7.1 | PBRP 4.0 | OrangeFox R12 | VictorRecovery (planned) |
|---|---|---|---|---|
| Touch UI | Yes [Certain, T] | Yes, Android-like UI [Certain, P] | Yes, MD2 UI [Certain, O] | Yes, dark neon + AMOLED [plan] |
| Hardware-button GUI control | Partial [Guessing] | Unknown [Guessing] | Yes since R11.3 [Certain, O] | Yes (for broken screens) [plan] |
| Zip / img flashing | Yes [Certain, T] | Yes incl. ozip [Certain, P] | Yes [Certain, O] | Yes + pre-flash checks [plan] |
| Backup / restore | Yes [Certain, T] | Yes [Likely] | Yes + quick backup [Certain, O] | Incremental, zstd, checksummed, optional encryption [plan] |
| Backup digests | MD5 optional [Likely] | MD5 [Likely] | Digests after backup [Certain, O] | SHA-256 per chunk + manifest [plan] |
| Backup to PC/Pi over network | No [Likely] | No [Likely] | No [Likely] | Yes, USB-net first, Wi-Fi later [plan] |
| Decryption FBE/FDE | Yes, device-dependent [Certain, T] | Yes [Certain, P] | Yes [Certain, O] | Inherit TWRP + per-device blob work [plan] |
| File manager | Basic [Likely] | New file manager [Certain, P] | Advanced, multi-select, nano [Certain, O] | Advanced + hash/compare [plan] |
| Terminal | Yes [Certain, T] | Yes [Likely] | Yes + from file manager [Certain, O] | Yes + BusyBox 1.36.1 [plan] |
| ADB sideload | Yes [Certain, T] | Yes [Likely] | Yes [Likely] | Yes [plan] |
| fastbootd | Yes on dynamic devices [Likely, T] | Yes [Likely] | Yes [Likely] | Yes [plan] |
| Magisk install | Manual zip [Likely] | Installer/remover [Certain, P] | Built-in [Certain, O] | Magisk/KernelSU/APatch one-tap re-root via RootForge OS [plan] |
| Root module management | No [Likely] | No [Likely] | Magisk/APatch/KernelSU modules [Certain, O] | Yes + fleet module set [plan] |
| Signature/hash check before flash | Zip sig verify optional [Likely] | No [Likely] | Zip signature verify [Certain, O] | Hash + signature + device + partition checks [plan] |
| Auto boot backup before flash | No [Likely] | No [Likely] | No [Likely] | Yes, always [plan] |
| Auto-rollback on failed boot | No [Likely] | No [Likely] | No [Likely] | A/B retry counters; A-only boot counter [plan] |
| Saved flash lists (queued) | OpenRecoveryScript [Certain, T] | OpenRecoveryScript [Likely] | Scripts [Likely] | Named, reusable lists + companion/droidcommand-AI [plan] |
| NetHunter-aware mode | No [Likely] | No [Likely] | No [Likely] | Keep kernel + chroot across ROM updates (7T) [plan] |
| Re-inject recovery after OTA (A/B) | "Flash current TWRP" [Certain, T] | Yes [Likely] | Yes [Likely] | Automatic, in flash lists [plan] |
| App manager / freeze | No [Likely] | System-app remover [Certain, P] | App Manager on some devices [Certain, O] | Per-app wipe, freeze via packages.xml, uninstall [plan] |
| SELinux / permission repair | Fix contexts (older) [Likely] | No [Likely] | No [Likely] | Yes [plan] |
| Bootloop fix without full wipe | No [Likely] | No [Likely] | Magisk module disable [Likely] | Module safe-mode, app/package rollback, cache/dalvik clear [plan] |
| Password protection | No [Likely] | Password recovery tool [Certain, P] | PIN/password/gesture [Certain, O] | PIN, plus wireless locked by default [plan] |
| EDL reboot / handoff | No [Likely] | Unknown [Guessing] | EDL reboot [Certain, O] | EDL reboot + EDL-Commander handoff notes [plan] |
| Heat / charge guard | No [Likely] | No [Likely] | No [Likely] | Thermal throttle, pause jobs, charge cap [plan] |
| Remote control | No [Likely] | No [Likely] | No [Likely] | Optional, off by default, USB-paired [plan] |
| Companion app | Official TWRP app (flash) [Certain, T] | No [Likely] | No [Likely] | VictorRecovery Companion [plan] |
| Themes | No [Likely] | Accents [Certain, P] | Themes/accents/fonts [Certain, O] | Neon + AMOLED [plan] |
| Built-in manual | No [Likely] | Docs site [Certain, P] | Wiki [Certain, O] | On-device manual [plan] |
| Licence | Apache-2.0 + GPL parts [Likely] | Apache-2.0 [Certain, GitHub] | GPL stressed [Certain, O wiki] | Apache-2.0 for our code, GPL parts kept GPL [plan] |
| hotdogb support | Unmaintained [Certain] | None [Certain] | Unsupported since 2021 [Certain] | Target #1 [plan] |
| avicii support | 3.7.1_12 [Certain] | Official 4.0 (2025-10) [Certain] | Not official [Certain] | Target #4 [plan] |
| shamu / angler support | 3.7.0_9 [Certain] | None [Certain] | shamu beta 2020 only [Certain] | Targets #2/#3 [plan] |

Bottom line [Likely]: OrangeFox leads today on UX and root tooling, PBRP on avicii freshness, TWRP on breadth. VictorRecovery's edge would be safety (pre-flash checks, auto boot backup, rollback), workflow (flash lists, NetHunter mode, companion), and backups to PC/Pi, plus being the only maintained recovery for hotdogb.
