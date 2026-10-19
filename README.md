# Pearly

Pearly is a native Mac app that mines Pearl (PRL) and Ycash (YEC) on the GPU of Apple silicon Macs.

**[Download the latest release](https://github.com/pearly-miner/pearly-releases/releases/latest)** · [pearly.dev](https://pearly.dev)

- **Requirements:** macOS 26 or later on an Apple silicon Mac. Designed for the M5 generation. Pearly hasn't been tested on M1 to M4: Ycash should work, but those chips lack the M5's GPU matrix hardware, so Pearl, if it runs, will be much slower.
- **Install:** open the disk image and drag Pearly onto Applications. Pearly is signed and notarized by Apple.
- **Fee:** Pearly is free, with a 2% developer fee for each coin, mined on the pool you choose.
- **Verify:** each release lists the SHA-256 of its disk image in a `.SHA256SUMS` file. Check a download with `shasum -a 256 -c Pearly-<version>.SHA256SUMS`.

This repository holds binary releases only. Pearly is proprietary software under its End User Licence Agreement, which ships inside the app with the third-party notices.

> [!IMPORTANT]
> Pearl has a proposed hard fork, [PIP-3](https://github.com/pearl-research-labs/pips/blob/main/PIPS/pip-0003.md), that would move mining to floating-point (FP8) arithmetic. If it activates, Pearly 1.0.0 will no longer be able to mine Pearl, and current Apple silicon is unlikely to mine Pearl effectively. Ycash mining is unaffected. As of October 2026, PIP-3 is a draft with no activation date.