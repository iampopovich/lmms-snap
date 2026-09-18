# lmms-snap

Unofficial, community-maintained Snap Store packaging of
[LMMS](https://lmms.io) (Linux MultiMedia Studio) — a free, open-source,
cross-platform digital audio workstation.

**This project is not affiliated with, endorsed by, or published by the
LMMS project or its maintainers.** It repackages the official upstream
Linux AppImage releases from
[LMMS/lmms](https://github.com/LMMS/lmms/releases) into a snap. LMMS is
licensed under the GNU General Public License v2.0 or later — see
[LMMS/lmms/LICENSE.txt](https://github.com/LMMS/lmms/blob/master/LICENSE.txt).

## Snap name

Published under `lmms-unofficial` (deliberately not the bare `lmms` name,
to avoid implying official status and any future naming dispute with
upstream — see Snap Store's
[naming policy](https://documentation.ubuntu.com/snapcraft/stable/how-to/publishing/register-a-snap/)).

## Channels

| Channel | Tracks | Update cadence |
|---|---|---|
| `stable` | Newest **non-prerelease** GitHub Release (e.g. `v1.2.2`) | Rare — only when upstream cuts a real stable tag |
| `edge` | Newest GitHub Release of **any** kind, including alpha/pre-release (e.g. `v1.3.0-alpha.2`) | Whenever upstream publishes a new release |

```
snap install lmms-unofficial            # stable channel
snap install lmms-unofficial --edge     # latest alpha/pre-release
```

This mirrors the same "stable vs pre-release" choice LMMS itself presents
on its own [download page](https://lmms.io/download#linux).

## How it's built

`snapcraft.yaml` downloads the matching Linux AppImage asset
(`lmms-<version>-linux-x86_64.AppImage` / `-linux-arm64.AppImage`) for the
release tag picked by CI, extracts it (`--appimage-extract`), and primes the
resulting `usr/` tree directly — no source build. See
`.github/workflows/build-and-publish.yml` for the upstream-tracking and
publish automation:

1. `check-upstream` — queries `GET /repos/LMMS/lmms/releases`, computes both
   the newest release overall (edge candidate) and the newest
   non-prerelease release (stable candidate), and compares each against
   what's currently published on the corresponding Snap Store channel.
2. `build-and-publish-edge` / `build-and-publish-stable` — build via
   `snapcore/action-build`, publish to `edge` / `candidate` respectively.
3. `promote-stable-to-stable` — promotes `candidate` to `stable` once a
   genuine stable release has been built (small soak gate; safe to remove
   if instant publishing is preferred).

Runs on a daily cron, on `workflow_dispatch`, and on pushes touching
`snapcraft.yaml` / `command-chain/**` / the workflow file itself.

## Local build

```
snapcraft pack
```

Requires `snapcraft` with an LXD (or Multipass) build backend configured
locally. Not required for the CI-driven publish flow above.

## License

This packaging repository (workflow, `snapcraft.yaml`, wrapper scripts) is
MIT-licensed — see `LICENSE`. LMMS itself is GPL-2.0-or-later, licensed
separately by its own copyright holders.
