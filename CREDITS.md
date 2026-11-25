# Credits and licence handling

VictorRecovery stands on the work of others. Thank you.

## TWRP (TeamWin Recovery Project)
- Base of VictorRecovery. https://twrp.me, https://github.com/TeamWin, minimal manifests: https://github.com/minimal-manifest-twrp
- Our recovery fork is a real GitHub **fork** of `TeamWin/android_bootable_recovery`: https://github.com/Victorious93/android_bootable_recovery. Upstream licence files, headers and history are kept as they are.

## OrangeFox Recovery and PitchBlack Recovery Project (PBRP)
- Inspiration for features only. **No code from OrangeFox or PBRP is used at present.** If any is ever used, its licence, headers and authorship will be kept and listed here first.
- https://orangefox.download · https://github.com/PitchBlackRecoveryProject

## Device trees
- OnePlus 7T (hotdogb): imported with full history from `rabilgic/twrp_device_oneplus_hotdogb` (which credits **lossyx** and **nebrassy**). See https://github.com/Victorious93/android_device_oneplus_hotdogb_victorrecovery. That upstream has no top-level licence file, which is stated in the tree's README.
- Other trees we study: TeamWin / Dees_Troy (shamu, angler, sm8150-common), rany2 and sreeshankark (avicii), mauronofrio (hotdog unified).

## BusyBox (GPL-2.0-only), planned
- We plan to bundle upstream **BusyBox 1.36.x**, built from the unmodified upstream source with a current NDK. Nothing is bundled yet.
- GPL-2.0 handling: every release that ships a BusyBox binary will include (or link to, in the same release) the exact corresponding source, our `.config` and build scripts, and the GPL-2.0 text. BusyBox is run as a separate program and does not change the licence of our own Apache-2.0 code.
- **No code from ObsidianBox's proprietary app** is used.

## Other
- AOSP: Apache-2.0.
- Planned: libsu by topjohnwu (Apache-2.0) in the companion app (not used yet).
