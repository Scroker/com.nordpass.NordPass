# com.nordpass.NordPass

Flatpak packaging for [NordPass](https://nordpass.com/), a password manager by Nord Security.

> **Note:** This package is marked end-of-life (`flathub.json`). The upstream application is no longer maintained on Flathub.

## How it works

The Flatpak wraps the official NordPass Snap package rather than building from source:

1. At build time, `unsquashfs` (squashfs-tools 4.7.4) is compiled from the `shared-modules` submodule.
2. At install time, the `apply_extra` script downloads the upstream `.snap`, unpacks it with `unsquashfs`, and drops the result under `/app/extra/nordpass/`.
3. At runtime, a wrapper script launches `nordpass` via `zypak-wrapper` to satisfy Electron's Chrome sandbox requirements inside the Flatpak sandbox.

## Building locally

Install the required runtimes first:

```bash
flatpak install flathub org.freedesktop.Platform//25.08 org.freedesktop.Sdk//25.08
flatpak install flathub org.electronjs.Electron2.BaseApp//25.08
```

Then build and install:

```bash
git submodule update --init
flatpak-builder --user --install --force-clean flatpak-build com.nordpass.NordPass.yaml
```

Run the result:

```bash
flatpak run com.nordpass.NordPass
```

## Updating the upstream version

1. Find the new snap revision from the Snapcraft API:
   ```bash
   curl -s -H "Snap-Device-Series: 16" https://api.snapcraft.io/v2/snaps/info/nordpass | python3 -m json.tool | grep -A5 "stable"
   ```
2. Download the snap and compute SHA256:
   ```bash
   curl -sL https://api.snapcraft.io/api/v1/snaps/download/00CQ2MvSr0Ex7zwdGhCYTa0ZLMw3H6hf_<revision>.snap | sha256sum
   ```
3. Update `url`, `sha256`, and `size` in `com.nordpass.NordPass.yaml`.
4. Add a `<release>` entry in `com.nordpass.NordPass.metainfo.xml`.

## Files

| File | Purpose |
|------|---------|
| `com.nordpass.NordPass.yaml` | Flatpak manifest |
| `com.nordpass.NordPass.metainfo.xml` | AppStream metadata and release history |
| `flathub.json` | Flathub config (arch: x86_64 only, EOL flag) |
| `nordpass.desktop` | Desktop entry |
| `icon.svg` | Application icon |
| `shared-modules/` | Git submodule with shared Flatpak module definitions |
