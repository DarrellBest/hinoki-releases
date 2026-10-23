# Updates and `latest.json`

Hinoki updates itself from a **release channel**: a `latest.json` file describing the newest version. By default,
that's the copy attached to this repository's newest release:

```
https://github.com/DarrellBest/hinoki-releases/releases/latest/download/latest.json
```

GitHub's `releases/latest` always points at the newest full release (not drafts or pre-releases), so this URL never
changes.

## How Hinoki updates

1. At startup (at most once a day), or when you run **Hinoki: Check for Updates**, Hinoki reads `latest.json` and
   compares `version` with its own.
2. If it's newer, Hinoki offers **Update**, **Release Notes**, **Skip This Version** or **Later**.
3. **Update** downloads the right file for your machine into `~/.hinoki/updates/<version>/` and checks its SHA-256
   against `latest.json`. A mismatch deletes the file and stops.
4. Then it depends on how Hinoki is installed:

   | Install | What Hinoki does |
   |---|---|
   | macOS app (`Hinoki.app`) | Unpacks the `.zip`, saves your open files, quits, swaps in the new app (the old one is kept until the copy succeeds) and starts it. |
   | Linux AppImage | Saves your files, quits, replaces the AppImage file and starts it. |
   | Linux deb / rpm | Opens the downloaded package in your system installer, which asks for your password. |
   | Windows | Runs the downloaded installer. |
   | Development build | Doesn't self-update. |

Hinoki has to be able to write to the folder it's installed in. If it can't (for example, an app bundle owned by
another user), it shows where the download is so you can install it yourself.

## Settings

| Setting | Default | |
|---|---|---|
| `hinoki.updates.checkAutomatically` | `true` | Check at startup (at most once a day). |
| `hinoki.updates.url` | the URL above | The channel: an `https://` URL to a `latest.json`, a local `latest.json`, or a folder holding one. In a local `latest.json`, file URLs can be relative to it, which is useful for testing or offline installs. |

## `latest.json` format

```json
{
  "version": "0.1.3",
  "date": "2026-10-23",
  "notes": "Short release notes (markdown).",
  "notesUrl": "https://github.com/DarrellBest/hinoki-releases/releases/tag/v0.1.3",
  "platforms": {
    "darwin-arm64": [
      { "kind": "zip", "name": "Hinoki-0.1.3-arm64-mac.zip", "url": "https://…", "sha256": "…", "size": 329394281 },
      { "kind": "dmg", "name": "Hinoki-0.1.3-arm64.dmg",     "url": "https://…", "sha256": "…", "size": 324115333 }
    ],
    "linux-x64": [
      { "kind": "appimage", "name": "Hinoki-0.1.3-x86_64.AppImage", "url": "…", "sha256": "…", "size": 0 },
      { "kind": "deb",      "name": "Hinoki-0.1.3-amd64.deb",       "url": "…", "sha256": "…", "size": 0 },
      { "kind": "rpm",      "name": "Hinoki-0.1.3-x86_64.rpm",      "url": "…", "sha256": "…", "size": 0 }
    ],
    "linux-arm64": [ "…the same three kinds…" ],
    "win32-x64":   [ { "kind": "exe", "name": "Hinoki-Setup-0.1.3-x64.exe", "url": "…", "sha256": "…", "size": 0 } ]
  }
}
```

- **Platform keys** are `<os>-<arch>`, the same as Node's `process.platform` and `process.arch`: `darwin-arm64`,
  `darwin-x64`, `linux-x64`, `linux-arm64` and `win32-x64`.
- **Kinds:**
  - `zip`: the macOS app, used for self-update.
  - `dmg`: for people installing by hand.
  - `appimage`, `deb`, `rpm`: Linux.
  - `exe`: the Windows installer.
- **`sha256` is required** for self-update, so every asset is checked before it's installed.

Hinoki's maintainers generate this file with `scripts/publish-release.js` when they publish a release.
