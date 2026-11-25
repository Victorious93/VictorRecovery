# VictorRecovery Companion — App design

Proposed package id: `com.victorious.recovery` (awaiting Don's yes). Icon: `branding/companion_icon_official.jpg` (approved by Don, Nov 16 2026). Labels: [Certain]/[Likely]/[Guessing]; [plan] = design decision. Nothing built yet.

## 1. Goals
- Works **standalone** on any of the four devices; RootForge OS is an optional add-on that unlocks one-tap re-root and fleet modules [plan].
- Never claims a recovery boots; shows "installed and hash-verified" only [rule].

## 2. Features
| Feature | How | Needs root | Label |
|---|---|---|---|
| Install / update VictorRecovery | Download signed image, verify SHA-256 + signature, then: A-only (Nexus) `dd` to recovery; A/B (7T, Nord) patch the current boot ramdisk and write both slots, saving the old boot first | Yes | [plan]; method [Likely] |
| Schedule backups | WorkManager job: reboot to recovery with a queued backup list at a set time, only when charging and cool | Yes (reboot recovery + queue write) | [plan] |
| Download & verify ROMs/kernels | Source list per device (official ROM pages, Don's NetHunter builds); hash and signature check before it can be added to a list | No | [plan] |
| Build flash lists | Drag-and-order editor (ROM → NetHunter kernel → re-inject recovery → root → modules); exports the signed JSON format from DESIGN.md §5 | No to build, yes to queue | [plan] |
| Reboot to recovery with a list queued | Write list to `/data/victorrecovery/queue/`, then `reboot recovery` | Yes | [plan] |
| Browse & restore backups | Read `manifest.json` of `.vrb` sets on phone, OTG or the PC/Pi receiver; restore queues a list | Partly | [plan] |
| Boot-success heartbeat | On boot complete, clear the A-only boot-attempt counter; on A/B report slot state | Yes | [plan] |
| Heat/charge view | Same thresholds as the recovery heat guard; warn before scheduling jobs on a hot phone (Nexus) | No | [plan] |
| Remote control link | Optional pairing with droidcommand-AI/VictorSuite; off by default | No | [plan] |

## 3. Tech stack
- Kotlin, Jetpack Compose, Material 3 with the neon palette (#0B0F1A / #00E5FF / #FF2BD6) and an AMOLED theme [plan].
- minSdk: Nexus 6/6P run crDroid; the Android version there must be checked, so minSdk 26 is a placeholder [Guessing].
- Root access through libsu (topjohnwu, Apache-2.0) [Likely suitable]; works with Magisk, KernelSU and APatch su [Likely].
- Signature verification: ed25519 (Tink or BouncyCastle) [plan].
- RootForge OS integration via an exported, permission-protected intent/AIDL API, detected at runtime [plan].

## 4. Security
- APK signed with one fixed release key, kept out of the repo (CI secret) [rule].
- No analytics, no account required [plan].
- Only artifacts signed with the VictorRecovery release key can be installed as recovery [plan].

## 5. Build / CI
- GitHub-hosted Actions runner: Gradle build, lint, unit tests, signed release APK as a draft release [plan].
- Merge only on green CI and Don's approval [rule].

## 6. Risks
- Writing recovery into the boot ramdisk from Android on A/B is the riskiest step; always keep the previous boot image and show the exact fastboot command to restore it [Likely].
- crDroid on the Nexus devices may lack some APIs; old Android versions limit Compose features [Guessing].
