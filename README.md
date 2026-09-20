<div align="center">

<img width="100%" alt="header" src="https://capsule-render.vercel.app/api?type=waving&height=210&text=MRG%20Miner%20Bot&fontAlign=50&fontAlignY=36&fontSize=56&desc=Mining%20%7C%20KYC%20%7C%20Tasks%20%7C%20Referral%20%7C%20Wallet%20%7C%20Multi-Account&descAlign=50&descAlignY=58"/>

<img alt="typing" src="https://readme-typing-svg.demolab.com?font=Inter&size=18&duration=3000&pause=650&center=true&vCenter=true&width=900&lines=Auto+Mining+Claim+%26+Level+Unlock;Auto+KYC+Submission+%7C+Toggleable;Auto+Complete+Tasks+%7C+Earn+MRG;Auto+Referral+Commission+Claim;Auto+TON+Wallet+Link+%7C+From+data.txt;Per-Account+Device+Profile+Cached"/>

<p>
  <img alt="platform" src="https://img.shields.io/badge/Platform-MRG%20Miner%20Miniapp-111111"/>
  <img alt="multi-account" src="https://img.shields.io/badge/Multi--Account-Supported-111111"/>
  <img alt="proxy" src="https://img.shields.io/badge/Proxy-Supported-111111"/>
  <img alt="author" src="https://img.shields.io/badge/by-Yuurisandesu-111111"/>
</p>

<p>
  <b>MRG Miner Bot</b> is a full automation bot for the MRG Miner Telegram Miniapp.<br/>
  It handles the complete cycle: linking a TON wallet if not yet connected, claiming the mining reward and attempting to unlock the next miner level, completing all pending tasks, claiming referral commissions, and optionally submitting KYC for unverified accounts, all running automatically across multiple accounts with per-account device profile caching, proxy support, and a live countdown between cycles.<br/>
  Built and distributed by <b>Yuurisandesu</b>.
</p>

</div>

---

## Table of Contents

- [Requirements](#requirements)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Bot](#running-the-bot)
- [Features](#features)
- [File Structure](#file-structure)
- [Disclaimer](#disclaimer)

---

## Requirements

- Python `3.12+` (only needed to run the downloader script)

---

## Installation

**Clone the repository:**

```bash
git clone https://github.com/Yuurisan-N1/MRG-Miniapp.git
cd MRG-Miniapp
```

**Install downloader dependencies:**

```bash
pip install requests colorama yuurisan
```

**Download the binary for your platform:**

```bash
python bot.py
```

The script shows a numbered menu:

```
1. MRG Linux ARM64
2. MRG Linux AMD64
3. Windows (PowerShell / CMD)
```

Enter the number for your platform. The binary downloads with a live progress bar and is set to executable automatically on Linux.

Or download manually from the Releases page:
https://github.com/Yuurisan-N1/MRG-Miniapp/releases/latest

| File | Platform |
|---|---|
| `MRG.exe` | Windows x86_64 |
| `MRG-linux-amd64` | Linux x86_64 |
| `MRG-linux-arm64` | Linux ARM64 |

**Linux after manual download:**

```bash
chmod +x MRG-linux-amd64
```

Place the binary in the same folder as your `data.txt`, `proxy.txt`, `config.json`, and `device.json` before running.

---

## Configuration

### 1. Accounts (data.txt)

Fill `data.txt` with one entry per line. Each line can be just `initData`, or `initData` followed by a TON wallet address separated by `|`:

```
user=%7B%22id%22...&hash=abc123
user=%7B%22id%22...&hash=def456|UQAbc...yourwalletaddress
```

A wallet address is required for mining to be enabled. If no wallet is linked and none is provided, mining is skipped for that account. The `tgWebAppData=` prefix is stripped automatically if present.

> `initData` can be obtained from the browser DevTools when opening MRG Miner on Telegram Web.

### 2. Proxy (proxy.txt)

Fill `proxy.txt` with proxies, one per line (optional, leave empty to run without proxy):

```
host:port
host:port:user:pass
http://user:pass@host:port
```

Proxies are assigned to accounts by index in round-robin order.

### 3. Bot Settings (config.json)

`sleep_seconds` controls how many seconds the bot waits between cycles. `kyc` toggles automatic KYC submission for unverified accounts. Set it to `false` to skip KYC entirely.

---

## Running the Bot

**Linux:**

```bash
./MRG-linux-amd64
```

**Linux ARM64:**

```bash
./MRG-linux-arm64
```

**Windows:**

```bash
.\MRG.exe
```

Press `Ctrl+C` at any time to stop the bot cleanly.

---

## Features

### TON Wallet Link
If a wallet address is provided in `data.txt` and the account does not already have a TON wallet connected, the bot links it automatically before running the mining cycle. If no wallet is linked and none is provided, mining is skipped for that account with a notice.

### Auto Mining
The bot claims the pending MRG mining reward and logs the amount credited. After claiming, it attempts to unlock the next miner level. If the current balance is not enough to afford the next level, it is skipped and logged.

### Auto KYC
If `kyc` is enabled in config and the account has not yet passed KYC verification, the bot submits a KYC scan automatically. Accounts already verified or pending admin approval are skipped. If the submission is rejected, the bot retries with alternative approaches before giving up. The feature can be disabled entirely by setting `kyc` to `false` in config.

### Auto Tasks
The bot fetches all tasks and compares them against the account's already-completed task IDs. For each pending task, it submits a claim request and logs the MRG reward. Tasks rejected by the server or rate-limited are logged individually and the next task is tried.

### Referral Commission
The bot claims all available referral commissions for the account and logs the MRG credited. If no commission is pending, it is skipped.

### Per-Account Device Profile
Each account gets a device profile on first run consisting of a Samsung device name, RAM, screen resolution, and CPU core count randomly selected from a pool. The profile is saved to `device.json` keyed by Telegram user ID and reused on all subsequent cycles to keep the device identity consistent.

### Multi Account
All accounts in `data.txt` are processed sequentially within every cycle. Unclaimed MRG balance is logged at sign-in for each account. A fresh balance check is performed at the end of each account cycle. The cycle number is logged at the start and end of each round.

### Proxy Support
Proxies are loaded from `proxy.txt` and assigned to accounts by position in round-robin order. Proxy credentials are masked in log output. Running without proxies is fully supported.

### Auto Countdown
After all accounts complete a cycle, the bot displays a live `HH:MM:SS` countdown until the next cycle starts.

---

## File Structure

```text
MRG-Miniapp/
├── MRG.exe              # Windows binary
├── MRG-linux-amd64      # Linux x86_64 binary
├── MRG-linux-arm64      # Linux ARM64 binary
├── bot.py               # Interactive downloader script
├── config.json          # Sleep duration, feature toggles including KYC
├── data.txt             # Account initData and optional wallet, one per line
├── proxy.txt            # Proxy list, one per line (optional)
├── device.json          # Per-account device profile cache (auto-generated)
├── LICENSE              # License file
└── utils/
    └── banner.py        # Banner using yuurisan module
```

---

## Disclaimer

This tool is built for educational and technical exploration purposes. Use it wisely and at your own responsibility.

---

<div align="center">
<img width="100%" alt="footer" src="https://capsule-render.vercel.app/api?type=waving&height=120&section=footer"/>
</div>