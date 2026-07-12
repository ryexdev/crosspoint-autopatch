# crosspoint-autopatch

Personal auto-build pipeline for [CrossPoint Reader](https://github.com/crosspoint-reader/crosspoint-reader).

Every 6 hours a GitHub Actions workflow checks the official repo for a new
release. When one appears, it rebuilds that exact release tag with the patches
in [`patches/`](patches/) applied and publishes the result here — same version
tag, `firmware.bin` asset.

The firmware built here has its OTA updater pointed at **this repo's**
releases (patch 0002), so a device flashed once with an autopatch build keeps
updating itself from here, always tracking the newest official firmware plus
the patches.

## Patches

| File | What it does |
|---|---|
| `patches/0001-sd-init-retry.patch` | Retries SD card init up to 5× before showing the error screen (fixes spurious "SD card error" on wake with a slow card) |
| `patches/0002-ota-url-autopatch.patch` | Points the OTA update check at this repo instead of the official one |

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
