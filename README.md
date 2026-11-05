# GB Miner

<p>
  <img src="images/dashboard-noid.png" alt="GB Miner dashboard mining NOID" width="49%">
  <img src="images/dashboard-tape.png" alt="GB Miner dashboard mining TAPE" width="49%">
</p>

**GB Miner** is a GPU miner for two coins that share the same proof of work (Poseidon2b): **Parano1d (NOID)** and **Retro Boomer (TAPE)**. It runs on NVIDIA GTX 900/10/16 and RTX 20/30/40/50-series cards, Tesla M40, P100 and P40, Volta (V100, Titan V, Quadro GV100), Hopper (H100) and GA100 (CMP 170HX, A100, A30), on Linux, Windows and HiveOS, with one download per platform. It speaks Stratum and HTTP pools natively.

You pick the coin with one setting, `COIN=noid` or `COIN=tape` (`--coin`). Without it the miner goes by your wallet address: `o1…` is NOID, `tape1…` is TAPE. The dashboard shows which coin you are mining: **GB Miner · NOID** in green, **GB Miner · TAPE** in orange.

GB Miner is the successor of [GB Parano1d Miner](https://github.com/gustlborg/gb-parano1d-miner) 3.0.6. Since 1.0.1 it also runs GTX 900 and GTX 10 cards and is faster on GTX 16, RTX 20 and Volta cards; every other card runs the kernels of 3.0.6 unchanged. Everything applies to both coins.

**Current release: v1.0.1**

- **New: GTX 900 (Maxwell) and GTX 10-series (Pascal) cards are supported**, together with the Tesla M40/M60, Tesla P100/P40/P4 and the P102/P104/P106-100 mining cards.
- **GTX 16 / RTX 20-series (Turing) and Volta cards are faster: +0.6 to +4.6 %** (measured live on rented cards: RTX 2080 Ti +4.6 %, CMP 40HX +4.2 %, CMP 50HX +4.2 %, RTX 2060 +4.0 %, RTX 2060 SUPER +3.0 %, RTX 2080 SUPER +1.2 %, GTX 1660 Ti +0.9 %, Tesla V100 +0.6 %).
- **Older NVIDIA drivers on Linux and HiveOS:** GTX 900/10/16, RTX 20 and Volta cards run on driver 525 or newer. A rig whose driver is too old for some of its cards mines the others.
- **HiveOS on a rig with an integrated GPU:** the panel showed one card's hashrate on the integrated GPU (for example an Intel HD Graphics) and left the last card empty, because the HiveOS script reported shifted bus numbers. It now reports each card's own bus number; mining itself and the total hashrate were never affected.
- RTX 30/40/50, GA100 and Hopper cards run the same kernels as before.

The complete lists of the new and the faster GPUs and the NVIDIA driver per GPU family are in the [release notes](../../releases/latest).

Since 1.0.0:

- **One miner for NOID and TAPE.** Each package has one start script per coin with the coin already set (`start-noid.sh` / `start-tape.sh`, on Windows `start-noid.bat` / `start-tape.bat`): fill in both once and switch by starting the other one. On the command line it is `--coin`, on HiveOS `COIN=` in "Extra config arguments"; otherwise the wallet address decides.
- **Pools for both coins by name:**
  - NOID: the GB Pool and the pool names from 3.0.6, without `ariabrain` (AriaPool paused NOID mining on 18 October 2026).
  - TAPE: the GB Pool (`stratum.tape.pool.army`), suprnova, rplant and pqpool.
- **No pool fee on the GB Pool with this miner (0 %),** on all four ports and for both coins.
- **HiveOS:** The miner is called `gb-miner`. The Pool URL field also takes `host:port`, a bare host name or a pool name.

---

## Download

Get the archive for your platform from [Releases](../../releases/latest), unpack it, and run it from inside the folder.

| Platform | Archive |
|---|---|
| Linux | `gb-miner-1.0.1-linux.tar.gz` |
| Windows | `gb-miner-1.0.1-windows.zip` |
| HiveOS | `gb-miner-1.0.1.tar.gz` (custom-miner package) |

The Linux and Windows archives each contain a `SHA256SUMS` file with the SHA-256 of every file inside. The `SHA256SUMS` on the release page lists all three archives.

You do not need the CUDA Toolkit, only an NVIDIA driver: on Linux and HiveOS 525 or newer for GTX 900/10/16, RTX 20 and Volta cards (GTX 900, GTX 10 and Volta up to the 580 series, NVIDIA's last one for them) and 580 or newer for every other card; on Windows 580 or newer for every card. The Linux and HiveOS binaries run on any 64-bit Linux with glibc 2.31 or newer (Ubuntu 20.04 and later, current HiveOS images).

## Quick start

**Linux and Windows:** Each package holds one start script per coin, with the coin already set: `start-noid.sh` and `start-tape.sh` (Linux), `start-noid.bat` and `start-tape.bat` (Windows). Open the one for your coin in a text editor (on Windows right-click → Edit), fill in the block at the top, save, and start it. Fill in both once, and you switch coins by starting the other one:

| Field | What to put in |
|---|---|
| `WALLET` | your payout address, `o1…` in `start-noid.*`, `tape1…` in `start-tape.*` (required) |
| `WORKER` | the name shown on the pool (optional; blank = the computer's name) |
| `POOL` | `gbpool` (default, the GB Pool of the coin), another pool name of the coin (see [Pools](#pools)), or a pool address such as `stratum+tcp://tape.suprnova.cc:3352` (Suprnova's TAPE port for multi-GPU rigs) |
| `MODE` | optional: `balanced` (default), `performance` (highest hashrate) or `efficiency` (fewest watts per hash) |
| `LOCK_CORE_CLOCK`, `LOCK_MEM_CLOCK`, `CORE_OFFSET`, `POWER_LIMIT` | optional clock and power settings, e.g. `2705`, `810`, `300` on an RTX 5070 Ti; leave them blank to leave the card alone. They are applied at start and put back when the miner exits |

In the `.bat` files each line reads `set "FIELD=value"`, for example `set "WALLET=tape1…"`. Start it with `sh start-tape.sh` (or `sh start-noid.sh`) on Linux (with clock or power settings as root: `sudo sh start-tape.sh`). On Windows, double-click `start-tape.bat` or `start-noid.bat` (with clock or power settings: right-click → Run as administrator).

**Linux:** Or pass the full command line:
```
./gb-miner cuda-mine --coin tape --pool gbpool --coinbase <your-tape1-address> --worker rig0 --search-binary ./gb-miner-engine
./gb-miner cuda-mine --coin noid --pool gbpool --coinbase <your-o1-address> --worker rig0 --search-binary ./gb-miner-engine
```

**Windows:** Or, in PowerShell:
```
./gb-miner.exe cuda-mine --coin tape --pool gbpool --coinbase <your-tape1-address> --worker rig0 --search-binary gb-miner-engine.exe
```

**HiveOS:** Flight Sheet → Custom miner:
- Miner name: `gb-miner`
- Installation URL: `https://github.com/gustlborg/gb-miner/releases/download/v1.0.1/gb-miner-1.0.1.tar.gz`
- Wallet and worker template: `%WAL%.%WORKER_NAME%` (or your address)
- Pool URL: a pool address, `host:port` (taken as plain Stratum; for a TLS port write `stratum+tls://host:port`), or a pool name such as `gbpool`. Empty means the GB Pool of the coin.
- Extra config arguments: `COIN=tape` or `COIN=noid` (without it the wallet decides). Everything else is optional, for example `MODE=performance` or `LOCK_CORE_CLOCK=`, `LOCK_MEM_CLOCK=`, `CORE_OFFSET=` (see the README inside the package).

HiveOS derives the miner name from the archive's file name, so use exactly this URL and `gb-miner`. A flight sheet with the old miner name `parano1d` keeps running GB Parano1d Miner. To switch, create a flight sheet with `gb-miner`. On HiveOS the rig's OC profile owns the clocks: memory-clock autotune stays off there unless you add `AUTOTUNE=on`.

## Pools

`--pool` (`POOL` in the start scripts) takes a pool name of the coin you mine or a pool address. It detects Stratum or HTTP from the address. The default is the GB Pool of the coin. Each GB Pool has four ports, and all of them adjust the difficulty to your hashrate on their own:

| `--pool` | NOID (`stratum.noidexplorer.org`) | TAPE (`stratum.tape.pool.army`) | For |
|---|---|---|---|
| `gbpool` (default) | `:3333` | `:3333` | Recommended for everyone, CPU and GPU. |
| `gbpool-ssl` | `:3334` (TLS) | `:3334` (TLS) | The same, encrypted. |
| `gbpool-farm` | `:3335` | `:3335` | Farms, rentals and proxies; high start difficulty. |
| `gbpool-solo` | `:3336` | `:3336` | Solo: a block you find is yours alone. |

With this miner the GB Pool charges no pool fee (0 %) on all four ports, for both coins.

The other pools:

| Coin | Names |
|---|---|
| NOID | `innovlab`, `luckypool` / `luckypool-us` / `luckypool-asia`, `suprnova`, `alphapool`, `rplant` / `rplant-na` / `rplant-asia` (TLS), `parano1d` (HTTP); `noidexplorer` is another name for `gbpool` |
| TAPE | `suprnova` (`tape.suprnova.cc:3351`), `suprnova-ssl` (`:3357`), `suprnova-solo` (`:3358`), `rplant` / `rplant-na` / `rplant-asia` (TLS, port 17066), `pqpool` (`tape.pqpool.com:5555`), `pqpool-ssl` (`:5556`; for solo on PQPool write the wallet as `solo:tape1…`) |

For TAPE you can also paste a pool's web address (`https://tape.pool.army`, `https://tape.suprnova.cc`, `https://pool.rplant.xyz`, `https://pqpool.com`); the miner uses that pool's main Stratum port. A second pool can be given with `--backup-pool`. The miner switches over on its own if the main pool stops answering, and switches back when it returns.

## Hardware

Supported cards:
- **Native kernels:** GTX 900/10/16 and RTX 20/30/40/50-series (with the Tesla M40/M60, Tesla P100/P40/P4 and P102/P104/P106-100), Volta, Hopper (H100; validated on the H100 NVL) and GA100 (validated on the CMP 170HX and the A100 SXM4).
- **Same chips:** Workstation and data-center cards with the same chips (for example the RTX A-series, A10/A40, L4/L40 and RTX PRO Blackwell) get the same native kernels. Their speed is not measured.
- **Fallback kernel:** Any other NVIDIA GPU with compute capability 8.0 or newer runs a JIT-compiled fallback kernel. It needs a driver with CUDA 13.3 support or newer.
- **Not supported:** The first Maxwell generation (GTX 750, GTX 750 Ti) and older cards. In a mixed rig the miner skips such a card with a note and mines on the others.
- **NVIDIA driver:** see "Download" above. One driver serves all cards of a rig; if it is too old for some of them, the miner mines the others and names the cards it skipped.

One binary covers all of these generations, so a mixed rig needs no separate download.

## Mining modes, autotune, power and clocks

- **Mining modes:** `--mode performance|balanced|efficiency` (`MODE=` in the start scripts and on HiveOS), default `balanced`. They apply to RTX 30, 40 and 50 cards; other GPUs run their one kernel.
- **Autotune:** It measures the lowest memory clock that still gives the full hashrate, once per card, and locks it while mining. This proof of work needs no memory bandwidth, so that saves power at the same hashrate. It is off on HiveOS unless you add `AUTOTUNE=on`.
- **Power and clocks:**
  - `--powerlimit`, `--lock-core-clock`, `--core-clock-offset` and `--lock-mem-clock` set the card for the run. At exit the miner puts the power limit and the offset back and releases the clock locks it set.
  - The power limit and the offset are checked against the card's allowed range first.
  - A locked core clock with a positive offset gives the same hashrate at that clock for much less power.
- **Multi-GPU:** By default the miner uses every supported GPU under one pool connection. `--devices 0,1,2` restricts it to the cards you list.

The README inside each package covers all of this in detail.

## Developer fee

This miner mines a disclosed **2 % developer fee**, for both coins: into a NOID wallet when you mine NOID, into a TAPE wallet when you mine TAPE. On Stratum pools the fee runs as a second session to the same pool under your worker name, interleaved continuously rather than taken as one long block. The fee has no log lines of its own. Its shares are not counted in the miner's share totals, so the hashrate shown is the card's full rate. The fee wallets are never displayed; this README is the disclosure.

## Dashboard

In a terminal window the miner shows a live dashboard:
- **Title:** "GB Miner · NOID" or "GB Miner · TAPE".
- **Top:** pool, wallet, worker, pool latency and block height.
- **Middle:** hashrate with rolling averages, shares, power and efficiency.
- **Per GPU:** one row with hashrate, shares, temperatures, fan, power and clocks.
- **Bottom:** the scrolling log.

Keys: `q` quits cleanly, `p` pauses and resumes, `r` resets the averages, and `PgUp`/`PgDn` page through the log. Redirected output (HiveOS, a log file, a pipe) stays a plain log; `PARANO1D_PLAIN_OUTPUT=1` forces it.

## License

GB Miner is proprietary software, distributed as a binary release; see `LICENSE`. That license prohibits reverse engineering, decompilation, disassembly and analysis by any automated system (including large language models), except where such a prohibition is void under applicable mandatory law. Third-party components and their licenses are listed at the end of the same file.
