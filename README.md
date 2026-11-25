# Pearly

Pearly mines **Pearl (PRL)** and **Ycash (YEC)** on Apple silicon Macs and NVIDIA GPUs. Use the Mac app or the command-line miner for Linux and Windows.

**[Download the latest release](https://github.com/pearly-miner/pearly-releases/releases/latest)** · [pearly.dev](https://pearly.dev)

Pearly is free to download, with a **2% developer fee** for each coin. This repository holds binary releases only. Pearly is proprietary software; the licence and third-party notices are included in each package.

## Choose a download

| Platform | Pearly 2.0.1 download | Interface |
|---|---|---|
| macOS, Apple silicon | [Pearly-2.0.1.dmg](https://github.com/pearly-miner/pearly-releases/releases/download/v2.0.1/Pearly-2.0.1.dmg) | Mac app |
| Linux, x86-64, NVIDIA | [pearly-2.0.1-linux-x86_64.tar.gz](https://github.com/pearly-miner/pearly-releases/releases/download/v2.0.1/pearly-2.0.1-linux-x86_64.tar.gz) | Command line |
| Windows, x86-64, NVIDIA | [pearly-2.0.1-windows-x86_64.zip](https://github.com/pearly-miner/pearly-releases/releases/download/v2.0.1/pearly-2.0.1-windows-x86_64.zip) | Command line |
| HiveOS, NVIDIA | [pearly-2.0.1.tar.gz](https://github.com/pearly-miner/pearly-releases/releases/download/v2.0.1/pearly-2.0.1.tar.gz) | Custom miner |




## Linux and Windows

Install an NVIDIA driver before running Pearly. The CUDA development toolkit is not required.

- **Linux:** x86-64 with glibc 2.34 or later; tested on Ubuntu 22.04.
- **Windows:** x86-64; tested on Windows 11 Enterprise LTSC 2024. The C++ runtime is included. The executable is not Authenticode-signed; verify the download using its checksum.
- **GPUs:** both engines were tested on RTX 3090, RTX 4090, RTX 5060 Ti, RTX 5070 Ti and RTX 5090. Pearl automatically selects an optimized kernel for these cards. Other included NVIDIA architectures use fallback kernels, but have not all been tested. Each package's manifest lists the tested cards and drivers. Final NVIDIA qualification used Linux driver 620.73.04 and Windows driver 621.40.

The command-line examples below use one GPU and one pool. Use `devices` to list GPUs and `--help` for available options.

### Linux quick start

Extract the archive and enter its directory:

```sh
tar -xzf pearly-2.0.1-linux-x86_64.tar.gz
cd pearly-2.0.1-linux-x86_64
./pearly --version
./pearly devices
./pearly selftest --zk
```

Mine Pearl, using Kryptex as an example:

```sh
./pearly pool --url stratum+ssl://prl.kryptex.network:8048 --wallet YOUR_PEARL_ADDRESS.WORKER
```

Mine Ycash, using zpool as an example:

```sh
./pearly ycash pool --url stratum+ssl://equihash192.mine.zpool.ca:52192 --user YOUR_YCASH_ADDRESS.WORKER --password 'c=YEC,zap=YEC'
```

Replace the address and worker placeholders with your own. Use a payout address for the coin you are mining.

### Windows quick start

Extract the ZIP, open PowerShell in the extracted directory containing `pearly.exe`, then run:

```powershell
.\pearly.exe --version
.\pearly.exe devices
.\pearly.exe selftest --zk
```

Pearl:

```powershell
.\pearly.exe pool --url stratum+ssl://prl.kryptex.network:8048 --wallet YOUR_PEARL_ADDRESS.WORKER
```

Ycash:

```powershell
.\pearly.exe ycash pool --url stratum+ssl://equihash192.mine.zpool.ca:52192 --user YOUR_YCASH_ADDRESS.WORKER --password 'c=YEC,zap=YEC'
```

Use **Ctrl-C in the miner's console** to stop cleanly. An independent recovery process also handles console-close and miner-only forced termination. An OS crash, a hung driver or killing the recovery process can still prevent restoration; follow any recovery instructions printed by Pearly.

### Pearl GPU settings

Core offset defaults to zero. Clock and power changes require permission from the NVIDIA driver.

| Option | Effect |
|---|---|
| `--device N` | Select the CUDA device listed by `pearly devices` |
| `--power-limit WATTS` | Request a power cap for the selected GPU |
| `--core-offset MHZ` | Request a core-clock offset |
| `--mem-clock auto` | Default: attempt an 810 MHz memory lock only when the automatic policy permits it |
| `--mem-clock 810` | Explicitly request an 810 MHz memory lock |
| `--mem-clock 0` | Leave memory clocks untouched, including any existing lock |

On Windows, desktop processes can cause automatic mode to leave memory clocks with the driver. You can explicitly request `--mem-clock 810`, but it can improve efficiency on some cards and reduce hashrate on others. It is not a universal tuning recommendation.

A clean exit clears the core offset before releasing memory/core clock locks set by Pearly and restores the previous power cap. If the offset cannot be cleared, Pearly retains the core lock and prints recovery instructions; do not release that lock while the offset remains applied. Releasing a clock lock returns control to the driver; it does not reconstruct a lock previously set by another tool. If another tool manages memory clocks, use `--mem-clock 0`.

### SRBMiner-style Pearl launches

Common SRBMiner PearlHash arguments are accepted:

```sh
./pearly --algorithm pearlhash --disable-cpu --pool prl.kryptex.network:8048 --tls true --wallet YOUR_PEARL_ADDRESS.WORKER --gpu-id 0
```

On Windows, replace `./pearly` with `.\pearly.exe`. Use `--gpu-id 0,1` to select multiple GPUs, or omit it to select all visible NVIDIA GPUs. A scalar setting applies to all selected GPUs; comma-separated settings require an explicit device list and follow that list's order. For example, `--gpu-id 2,0 --gpu-coffset 100,0` requests +100 MHz on GPU 2 and zero on GPU 0. This is a syntax example, not an overclock recommendation.

Pearly uses one worker and pool connection per GPU, with a shared wallet and pool. Native syntax is also available:

```sh
./pearly rig --devices all --url stratum+ssl://prl.kryptex.network:8048 --wallet YOUR_PEARL_ADDRESS --worker rig --dashboard on
```

`--dashboard auto` prints periodic tables among the logs. `--dashboard on` pins the dashboard above scrolling logs in a terminal; `--dashboard off` uses plain logs. Redirected output remains plain text.

Unsupported options produce an error, including fan controls, the management API, failover lists and SRB tuner/cache options. `--disable-cpu` disables CPU hashing, not Pearl's required CPU proof generation. The standalone Ycash command uses one GPU; the HiveOS adapter can manage multiple Ycash workers.

## Linux efficiency controls

New voltage and efficiency controls are opt-in and require appropriate driver permissions, a fixed core clock and loaded verification on the selected GPU. They are not available on Windows or Mac. Final Linux qualification used driver 620.73.04; support is not inferred from a successful driver call or from another driver's results.

- `--efficiency-tuner` measures and confirms a setting on the current card. With `--core-offset 0`, it searches the voltage curve; a nonzero manual core offset selects the supported offset path. It can take up to 15 minutes, may keep the original settings when no candidate qualifies, and does not guarantee a gain on every card. Automatic curve tuning was checked on the 3090, 4090, 5070 Ti and 5090.
- `--vcore-target MILLIVOLTS` requests a manual curve target and requires an explicit zero core offset. Select a target appropriate to the card's measured voltage and fixed clock; an unsafe or ineffective request is refused.
- `--gpu-vddc-offset MILLIVOLTS` accepts the SRB flag spelling. Physical support is card- and driver-dependent: a -30 mV request passed on a 4090, while -60 mV failed enforcement and was restored. The accepted command-line range is not a promise that every value works.
- `--power-save 1` requests a separate auxiliary-rail efficiency search on supported 50-series cards. It retains a setting only after measured verification; savings vary and a run may restore the original setting.

Do not combine automatic tuning with a manual voltage request. Use a fixed clock already suitable for your card. These controls are available in Pearl pool, benchmark, solo and rig modes; they do not tune Ycash. A fault or unresolved restoration stops further changes and retains recovery information. `pearly efficiency-info` reports available interfaces, and `pearly efficiency-recover GPU-...` handles a retained transaction by GPU UUID. Follow the miner's diagnostic instructions rather than resetting clocks out of order.

## HiveOS

Use the separate custom-miner download in the table above. It requires an NVIDIA rig, HiveOS based on Ubuntu 22.04 or newer (glibc 2.34 or newer), Python 3 and jq. The same package supports Pearl and Ycash, per-GPU statistics and miner restart.

In your flight sheet, choose **Configure in miner → Custom → Setup Miner Config**. Set the miner name to `pearly` and the installation URL to:

```text
https://github.com/pearly-miner/pearly-releases/releases/download/v2.0.1/pearly-2.0.1.tar.gz
```

Use `pearlhash` for Pearl or `equihash192` for Ycash, your wallet and worker template, and your pool's URL and password. The package README has the complete flight-sheet fields and supported extra arguments. All visible NVIDIA GPUs are selected by default. Configure each clock or voltage setting in one place to avoid competing HiveOS and Pearly controls.

## Pools

Use the host, port, payout-address format and password specified by your pool. A `stratum+ssl://` URL enables TLS with certificate and hostname verification by default. A `stratum+tcp://` URL uses plain TCP.

Pearl 2.0.0 was checked with Kryptex, Peryn, SkyPool, 2Miners, JetskiPool, Suprnova, PearlFortune, LuckyPool, HeroMiners, AlphaPool, k1pool, pooly.ca and BaikalMine. Ycash was checked with daPool, zpool and pooly.ca. These checks establish accepted shares at the tested endpoints, not a guarantee for every regional endpoint or future pool configuration.

Pearl negotiates proof compression with the pool. Pools that do not support compressed proofs receive smaller workloads, which can reduce hashrate. Compression adapts to supported expansion-ratio limits, including HeroMiners' limit.

## Mac

Requires **macOS 26 or later** on an **Apple silicon Mac**. Pearly was designed for the M5 generation and tested on an M5 Max. M1–M4 Macs have not been tested: Ycash may work, but those chips lack the M5's GPU matrix hardware used for Pearl. Intel Macs are not supported.

1. Open the DMG and drag **Pearly** onto **Applications**.
2. Open Pearly, choose a coin, and enter your payout address and pool.
3. If you do not have an address, choose **Use built-in address**.

The app is signed with a Developer ID and notarized by Apple. Its default pools are Kryptex for Pearl and daPool for Ycash; you can change either.

**Updating:** quit the old app, then replace it in Applications. Your saved addresses, pools, settings and recovery phrases are preserved.

### Mac app features

- **Separate settings for each coin.** Switch between Pearl and Ycash; each keeps its address, pool and worker name. Changes to these settings take effect when mining restarts.
- **Built-in payout addresses.** Pearly can generate an address and recovery phrase: 12 words for Pearl Wallet or 24 for Ywallet. The phrase stays in your Mac's Keychain and is shown only after Touch ID or your password. Write it down and import it into the corresponding wallet to access your mined coins. Ycash also offers the private key for Ywallet's Sweep.
- **Live dashboard.** See hashrate, GPU power, efficiency, accepted shares and estimated earnings. The menu bar provides a compact readout.
- **GPU sharing.** The dashboard shows Pearly's share of the GPU. Display activity and other apps can reduce mining speed.
- **Update notices.** Pearly checks for new releases and offers an update when one is available.
- **Keeps the Mac awake.** While mining on the power adapter, Pearly prevents idle sleep; the display can still turn off.


## Developer fee

There is a 2% dev fee. Pearly reserves **one GPU operation in fifty** for the developer: one launch for Pearl or one solve for Ycash. The 2% schedule applies to pool and solo mining. It is a share of scheduled work, not a guaranteed fraction of elapsed time, accepted shares or payouts.

## Verify your download

Download `SHA256SUMS` from the **same release** as your package. Compute the archive's SHA-256 and compare it with the matching line in that file:

macOS:

```sh
shasum -a 256 Pearly-2.0.1.dmg
```

Linux:

```sh
sha256sum pearly-2.0.1-linux-x86_64.tar.gz
```

Windows PowerShell:

```powershell
Get-FileHash .\pearly-2.0.1-windows-x86_64.zip -Algorithm SHA256
```

## Help and reports

For command-line options, run `./pearly --help`, `./pearly pool --help` or `./pearly ycash pool --help` (use `.\pearly.exe` on Windows).

When reporting a problem in [Issues](https://github.com/pearly-miner/pearly-releases/issues), include the Pearly version, operating system, GPU model, NVIDIA driver version if applicable, command line and relevant log lines. Remove wallet addresses and passwords from anything you post. Never post a recovery phrase or private key.
