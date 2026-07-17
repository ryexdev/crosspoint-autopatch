# crosspoint-autopatch

Personal auto-build pipeline for [CrossPoint Reader](https://github.com/crosspoint-reader/crosspoint-reader).

Every 6 hours a GitHub Actions workflow checks the official repo for a new
release. When one appears, it rebuilds that release with the patches
in [`patches/`](patches/) applied and publishes the result here as a
`firmware.bin` asset.

The published version is upstream's version with the **patch number bumped by
one** (upstream `1.4.1` → `1.4.2` here). The on-device OTA updater only compares
`major.minor.patch`, so this bump is what makes it see an autopatch build as
newer than the plain upstream release and offer it. When upstream ships its own
next release, our version bumps again and supersedes it — no manual cleanup.

The firmware built here has its OTA updater pointed at **this repo's**
releases (patch 0002), so a device flashed once with an autopatch build keeps
updating itself from here, always tracking the newest official firmware plus
the patches.

## Patches

| File | What it does |
|---|---|
| `patches/0001-sd-init-retry.patch` | Retries SD card init up to 5× before showing the error screen (fixes spurious "SD card error" on wake with a slow card) |
| `patches/0002-ota-url-autopatch.patch` | Points the OTA update check at this repo instead of the official one |
| `patches/0003-opds-search.patch` | Fixes OPDS library search: follows OpenSearch description documents and expands the URL template correctly (drops unused `{…}` params), so search works on Calibre-Web, Standard Ebooks, and similar servers |

## When a build fails

If upstream changes the code a patch touches, `git apply` fails, the workflow
run fails (GitHub emails the owner), and an issue is opened on this repo.
To fix: look at what upstream changed, decide if the patch is still needed,
regenerate it (`git format-patch`) against the new release tag, commit it to
`patches/`, and re-run the workflow from the Actions tab.

## Escape hatch

To go back to fully official firmware: download the official release,
copy `firmware.bin` to the SD card, and flash via
**Settings → Firmware update** on the device.
