---
name: Project context
description: Overview of what this repo is, its current state, and key decisions made
type: project
---

This is a Flathub packaging repository for NordPass (com.nordpass.NordPass). It wraps the upstream NordPass Snap package as a Flatpak — no NordPass source code is present.

**Why:** The app is marked end-of-life in `flathub.json` ("This application is no longer maintained."), so no further version updates are expected on Flathub.

**How to apply:** When asked about updating, building, or contributing, keep in mind this package is EOL and Flathub will not accept new submissions or updates for it.

---

## Key decisions

**squashfs-tools switched from inline 4.4 to shared-modules 4.7.4 (2026-05-03)**
The old inline squashfs module used version 4.4 with `xz_support.patch` and `gcc10.diff`. The Flatpak SDK 25.08 ships GCC 14/15 which treats empty signal handler declarations `void handler()` as errors (`-Wincompatible-pointer-types`). Fix: replaced the inline module with `shared-modules/squashfs-tools/squashfs-tools.json` (version 4.7.4), which has correct C99 declarations and built-in XZ/LZO/ZSTD support. The old patch files remain in the repo but are unused and can be deleted.

**NordPass updated to 7.6.18 / snap revision 221 (2026-05-03)**
- URL: `https://api.snapcraft.io/api/v1/snaps/download/00CQ2MvSr0Ex7zwdGhCYTa0ZLMw3H6hf_221.snap`
- sha256: `28815d530ca88c061301a057c8b020b07c4c2007d2dab2b86f6c6db898f01fc0`
- size: `111046656` bytes
