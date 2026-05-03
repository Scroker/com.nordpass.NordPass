# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A [Flathub](https://flathub.org) packaging repository for NordPass. It wraps the upstream NordPass Snap package as a Flatpak — there is no NordPass source code here.

**Note:** This application is marked end-of-life in `flathub.json`. No further updates are expected upstream.

## Build commands

```bash
# Initialise submodule (first time)
git submodule update --init

# Build and install locally
flatpak-builder --user --install --force-clean flatpak-build com.nordpass.NordPass.yaml

# Build only (no install)
flatpak-builder --force-clean flatpak-build com.nordpass.NordPass.yaml

# Run after install
flatpak run com.nordpass.NordPass
```

Required runtimes: `org.freedesktop.Platform//25.08`, `org.freedesktop.Sdk//25.08`, `org.electronjs.Electron2.BaseApp//25.08`.

## Architecture

The packaging pipeline works as follows:

1. **squashfs-tools module** — pulled from `shared-modules/squashfs-tools/squashfs-tools.json` (version 4.7.4); builds `unsquashfs` and installs it to `/app/bin/`
2. **libsecret module** — pulled from `shared-modules/libsecret/libsecret.json`
3. **nordpass module** — uses `extra-data` to download the upstream `.snap` at install time; the `apply_extra` script calls `unsquashfs` to unpack it; a wrapper script launches the result via `zypak-wrapper` (required for Electron's Chrome sandbox inside Flatpak)

The snap download URL and SHA256 are tracked via `x-checker-data` / snapcraft checker. To update the version manually, update `url`, `sha256`, and `size` under the `extra-data` source, then add a `<release>` entry to `com.nordpass.NordPass.metainfo.xml`.

## Key files

| File | Purpose |
|------|---------|
| `com.nordpass.NordPass.yaml` | Main Flatpak manifest — build modules, finish-args, sources |
| `flathub.json` | Flathub config: arch restriction (`x86_64` only), EOL message |
| `com.nordpass.NordPass.metainfo.xml` | AppStream metadata and release history |
| `nordpass.desktop` | Desktop entry (MIME handler for `nordpass://`) |
| `icon.svg` | App icon |
| `shared-modules/` | Git submodule — shared Flatpak module definitions used across Flathub |

## Submodule

`shared-modules` is a git submodule pointing to [flathub/shared-modules](https://github.com/flathub/shared-modules). Update it with:

```bash
git submodule update --remote shared-modules
```

## Notes on patches

`xz_support.patch` and `gcc10.diff` remain in the repo but are no longer used — they were patches for the old inline squashfs-tools 4.4 module, which has been replaced by `shared-modules/squashfs-tools/squashfs-tools.json` (4.7.4). They can be deleted.
