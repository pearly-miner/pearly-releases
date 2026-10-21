# Pearly

Pearly is a native Mac app that mines Pearl (PRL) and Ycash (YEC) on the GPU of Apple silicon Macs.

**[Download the latest release](https://github.com/pearly-miner/pearly-releases/releases/latest)** · [pearly.dev](https://pearly.dev)

- **Requirements:** macOS 26 or later on an Apple silicon Mac. Designed for the M5 generation. Pearly hasn't been tested on M1 to M4: Ycash should work, but those chips lack the M5's GPU matrix hardware, so Pearl, if it runs, will be much slower.
- **Install:** open the disk image and drag Pearly onto Applications. Pearly is signed and notarized by Apple.
- **Fee:** Pearly is free, with a 2% developer fee for each coin, mined on the pool you choose.
- **Verify:** each release lists the SHA-256 of its disk image in a `.SHA256SUMS` file. Check a download with `shasum -a 256 -c Pearly-<version>.SHA256SUMS`.

This repository holds binary releases only. Pearly is proprietary software under its End User Licence Agreement, which ships inside the app with the third-party notices.

**Updating from an earlier version:** quit Pearly, then drag the new version onto Applications and replace the old one. Your addresses, pools and settings are kept.

## Install

1. Download the DMG file.
2. Open it and drag **Pearly** onto **Applications**.
3. Open Pearly from Applications, choose a coin, and enter your wallet address, or choose **Use built-in address** if you don't have a wallet yet.

Pearly is signed with a Developer ID and notarized by Apple. The first time you open it, macOS asks you to confirm opening an app downloaded from the internet.

## Requirements

- macOS 26 or later. Pearly uses Metal 4 tensor operations.
- An Apple silicon Mac. Pearly was designed for the M5 generation and tested on an M5 Max. It hasn't been tested on M1 to M4: Ycash should work, but those chips lack the M5's GPU matrix hardware, so Pearl, if it runs, will be much slower. Intel Macs aren't supported.
- A wallet address for the coin you mine, or Pearly's built-in one, and a pool. Pearly starts with Kryptex for Pearl and daPool for Ycash; you can change either.

## Notes

- **Two coins, one switch.** Mine Pearl or Ycash and switch at any time. Each coin keeps its own address, pool and worker name.
- **Built-in payout address.** No wallet yet? Choose **Use built-in address** beside Payout address. Pearly makes an address and the recovery phrase that holds it: 12 words for Pearl Wallet, 24 for Ywallet. The phrase stays in your Mac's Keychain, and Pearly shows it only after Touch ID or your password. Write it down, then import it into the wallet whenever you're ready to see and spend what you've mined. If you already use Pearl Wallet, restore the phrase as a second wallet. For Ywallet, Pearly also shows the address's private key, which Ywallet's Sweep takes.
- **Pearl on the GPU's matrix units.** Pearl's proof of work runs as int8 tensor operations on the M5 GPU's neural accelerators. Every share is checked on your Mac with Pearl's own verifier before it's sent.
- **Live dashboard.** The window shows hashrate with a graph, power draw (GPU and memory), efficiency (TH/s per watt and joules per TH, or Sol/s per watt and joules per Sol), Pearly's share of the GPU, accepted shares and an estimate of daily earnings. The menu bar shows a live readout.
- **GPU share.** If another app is using the GPU, Pearly shows how much of it Pearly gets and, on hover, which apps take the rest.
- **Settings take effect on restart.** After you edit an address, pool or worker, a banner offers to restart the miner with the new settings.
- **Update notices.** Pearly checks this repository for new releases and shows a banner you can dismiss.
- **Stays awake while mining.** On the power adapter, Pearly keeps the Mac from idle-sleeping while it mines. The display still turns off. It never starts mining by itself: mining begins when you open Pearly.

## On an M5 Max

| | High Power | Automatic | Low Power |
|---|---|---|---|
| Pearl | 46 TH/s, about 50 W | 42 TH/s, about 40 W | 23 TH/s, about 13 W |
| Ycash | 57 Sol/s, about 49 W | 47 Sol/s, about 29 W | 23 Sol/s, about 11 W |

These were measured by the developer on a MacBook Pro plugged in, with the Energy Mode set in System Settings → Battery. Power is the GPU and its memory, as Pearly reports it. Other Macs will differ.

## Developer fee

Pearly is free and carries a 2% developer fee for each coin. The fee is mined on the pool you choose, over a second connection:

- **Pearl:** one GPU launch in fifty.
- **Ycash:** about 72 seconds of each hour.

The fee's work is shown in the Activity log as it's mined.

## Known limitations

- **Pearl's proposed FP8 change.** Pearl has a proposed hard fork, [PIP-3](https://github.com/pearl-research-labs/pips/blob/main/PIPS/pip-0003.md), that would move mining to FP8 floating-point arithmetic, which must match specific NVIDIA GPUs bit for bit. If it activates, this version of Pearly will no longer be able to mine Pearl, and current Apple silicon is unlikely to mine it effectively. As of this release, PIP-3 is a draft with no activation date. Ycash is unaffected.
- **Display-on slowdown.** While the screen is on, macOS and other apps share the GPU, so hashrate is lower than with the display off. Pearly's GPU share shows how much of the GPU it is getting. 
