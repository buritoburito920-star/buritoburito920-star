<div align="center">
  <img width="100%" height="auto" alt="3D-spaghetti" src="https://github.com/user-attachments/assets/2c0f3d4d-40cf-47e8-bb76-49a6496d4417" />
</div>

# 3D-Spaghetti
### Your Printer's New Guardian Angel

<p align="center">
  <b>Ever woke up to a bird's nest of wasted filament?</b><br>
  That late night print you trusted just turned into a plastic tumbleweed. <b>3D-Spaghetti Monitor</b> never blinks. It watches your print in real time and slams the brakes the second spaghetti starts, so you can sleep, work, or leave the house with zero anxiety.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Status-Beta-orange?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Made%20for-FlashForge%20AD5M-ff6b00?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Powered%20by-Computer%20Vision-blue?style=for-the-badge" />
  <img src="https://img.shields.io/badge/100%25-Reversible-green?style=for-the-badge" />
</p>

***

## 📋 Overview

It is not called 3D-Spaghetti because it *makes* spaghetti, it is called that because it *hunts* it. This is not just a detector, it is a full remote command center for your printer.

### Why 3D-Spaghetti Crushes The Alternatives?
OctoPrint and Obico are powerful, but they ask you to mod, flash, and risk your machine. 3D-Spaghetti plugs in natively. No hardware mods. No firmware flashing. Zero risk of **voiding your FlashForge factory warranty**. Pure plug and protect.

### ✨ What You Get
- **🍝 Instant Spaghetti Detection** - Catches failures in seconds and auto pauses before your spool is gone
- **🌍 Watch From Anywhere** - Crystal clear live camera stream, secure Tailscale tunnel, no port forwarding
- **🎮 Full Remote Control** - Pause, resume, cancel, move axes, set temps, send GCode from your phone
- **⚡ Photo Proof Alerts** - Discord pings with a snapshot the moment it pauses, plus start, finish, and manual events
- **🎬 Auto Time-lapse** - Every print becomes a shareable MP4, with optional HUD overlay
- **⏱ Smart Time Tracking** - Live ETA, Elapsed, and Total Time estimates after the first few layers

> [!Warning]
> - **Hardware Compatibility** - Tuned **only** for FlashForge AD5M <sub>(Adventurer 5M)</sub>. AD5X <sub>(Adventurer 5X)</sub> may work but is untested
> - **Project Status** - Active **Beta**, expect polish updates, minor bugs possible
> - **AI Honesty** - Computer vision is powerful but ***can*** be wrong, use Pause or Cancel at your own risk
> - **Safety First** - 100% reversible install, will not damage your Pi or printer
> - **Disclaimer** - Creator is not liable for issues from unguided code changes

---

## ⚙️ Requirements <sub>(Hardware check)</sub>

| Minimum | Recommended For Best Experience |
| :--- | :--- |
| • Raspberry Pi 3 (4GB RAM)<br>• 720p Pi Camera Module<br>• Quality power brick and cable <sub>(prevents voltage crashes)</sub><br>• Fast Micro SD card | • Raspberry Pi 4 (8GB RAM)<br>• 1080p Pi Camera Module<br>• Quality power brick and cable<br>• Fast Micro SD card |


**Software:** Raspberry Pi OS (Bullseye or Bookworm), Python 3.11, `rpicam-vid` / `libcamera-apps`, Tailscale

---

## 🚀 Setup & Installation

> [!NOTE]
> Verified live on Raspberry Pi 4. Other boards or PCs work with tiny tweaks.

### 📍 Step 1: Prep Your Network
1. Grab your **Raspberry Pi IP** and your **FlashForge Printer IP** (port `8899` is fixed)
2. Optional but awesome: Create a Discord server, make a webhook, copy your **Webhook URL** and your **Discord User ID** for personal pings

### 💻 Step 2: Get Your Tools
1. Install [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) and [FileZilla](https://filezilla-project.org) on your PC. Any SSH or SFTP client works.
2. Download the project bundle: `3D-Spaghetti` folder

### 🔌 Step 3: Connect To Your Pi
* **SSH:** PuTTY > Host Name = Pi IP, Port `22`, Connection type `SSH` > Open > login
* **SFTP:** FileZilla > Host `sftp://[Pi_IP]` > Username and Password > Port `22` > Quickconnect

### 📂 Step 4: Deploy and Go
1. In FileZilla drag the `3D-Spaghetti` folder to your Pi. Best spots are `/media/[user]/[MicroSD]/3D-Spaghetti` or `~/3D-Spaghetti`
2. In PuTTY:
   ```bash
   cd /path/to/3D-Spaghetti
   bash 3D-Spaghetti_Setup --check   # safe dry run, changes nothing
   bash 3D-Spaghetti_Setup           # real install
   ```
3. Open `http://[Pi_IP]:5000`, set your dashboard password, enter printer IP and Discord webhook. Your public link `https://[name].ts.net` appears after `config.json` is created.

### Installer Flags
```bash
bash 3D-Spaghetti_Setup --check      # audit only, perfect for testing
bash 3D-Spaghetti_Setup --uninstall  # remove services and funnel, keep your config
bash 3D-Spaghetti_Setup --reset      # full wipe to fresh wizard
```
Run it again anytime, it is idempotent and safe.

---

## 🖥️ The Dashboard

Open `http://[Pi_IP]:5000` at home or `https://[name].ts.net` anywhere. One password you set, that is it.

### Layout At A Glance
- **Top Bar:** Live status badge, progress, elapsed and ETA, hotend and bed temps, `?` to replay the tour, `AD5M` brand with your logo and favicon
- **Left:** Print Status with progress bar, layer `cur/tot`, AI fail percent, elapsed and ETA, plus Pause, Resume, Cancel and a full GCode Console with Tab autocomplete
- **Center:** Live Feed `640x480` MJPEG, green nozzle box, orange focus zone, `FAIL%` pill, 3m 05s setup timer, baseline and active states
- **Right:** Temps with SET and OFF, Settings panel, Detector stats (Status, Certainty, Hits, Cycle), AI Confidence, Temp chart, Time-Lapses

First launch triggers an interactive tour that spotlights every panel and setting. Skip anytime, hit `?` to replay.

---

## ⚙️ Settings

Hit `OPEN` in the right column.

### ALERTS Tab
Toggle each Discord alert independently:
- Print Started
- Print Finished
- Paused (Manual)
- Paused (Auto)
- Canceled (Manual)
- Canceled (Auto)

### AI SETTINGS Tab
- **Failure Action:** `Pause` or `Cancel` (heads up, AI may be wrong) or `Nothing (Alert Only)`
- **AI Confidence %:** 10 to 100, default 100. How sure the AI must be before it counts
- **Consecutive Hits:** 1 to 30, default `10`. How many hits in a row before it acts
- **HUD Bounding Boxes:** Show the nozzle detection, default `True`
- **Failure Overlay (Red):** Show the red change heatmap, logo stays even when off
- **Timelapse:** On or off
- **Timelapse HUD Overlay:** Burn boxes and status bar into video, off gives clean video, default `True`
- **Timelapse Interval:** 3 to 300 seconds between frames, higher is faster video, default `10`
- **Advanced Tuning:** Pixel Diff Threshold, Change Fraction, BG Adapt Alpha, Baseline Build Seconds, Baseline Delay Seconds

### SYSTEM Tab
- **Printer IP:** Your FlashForge on LAN
- **Discord Webhook:** Optional webhook URL
- **Discord User ID:** Optional ping ID

### Panels
- **Detector:** `Status`, `Certainty`, `Hits`, `Cycle`
- **Time-Lapses:** Finished videos with size, date, and download link. Auto refreshes every 60s

---

## 🔬 How It Actually Works

### Time-lapse
- Saves raw frames to `timelapses_raw/<timestamp>/` every `timelapse_interval` seconds while printing
- On print end, stitches to `timelapses/timelapse_<timestamp>.mp4` (falls back to `.avi` MJPG at 5 fps). Too few frames are auto discarded
- If `Timelapse HUD Overlay` is on, each frame gets `PRINTING | Layer x/y | AI n% | Hit a/b | MM-DD HH:MM:SS`. If off, clean video plus always on logo
- Get them from the Time-Lapses panel or `GET /timelapse/<name>`, list via `GET /api/timelapses`

### Detection
- One focus zone `ChangeDetector` against a BGR baseline. It checks the strongest BGR channel per pixel versus `PIXEL_DIFF_THRESHOLD = 90`, not grayscale, so same color spaghetti still pops
- Flow: 185s setup timer, then baseline build, then active
- Only fires after `failure_certainty` and `consecutive_hits` are met, with motion blur skip, top strip masking, and adaptive baseline learning
- Logo is baked into every HUD frame and every timelapse frame, no file needed

### What The Installer Does
1. **Python:** Prefers `~/miniforge3/bin/python3`, `~/mambaforge`, or `/opt/miniforge3`, else creates `env/` via micromamba or `python3-venv` on armv6, then installs `requirements.txt`
2. **Camera:** Installs `libcamera-apps` if `rpicam-vid` is missing
3. **Tailscale:** Installs via `tailscale.com/install.sh` if missing, runs `tailscale up` and shows `https://login.tailscale.com/a/...` if needed, adds passwordless `sudoers.d/ad5m-funnel`, enables `funnel --bg --https=443 5000` only after `config.json` exists
4. **Services:** Creates `printer_watchdog.service` (`python -u Printer_watchdog.py`) and `camera_feed.service` (`python web_feed.py`) if not already active
5. **Summary:** Prints `Local dashboard: http://[Pi_IP]:5000` and `Public dashboard: https://[name].ts.net` with fallback via `tailscale status --json`

---

## 🌐 Remote Access

- **Local:** `http://[Pi_IP]:5000`
- **Public:** `https://[name].ts.net` via `tailscale funnel --bg --https=443 5000`. Only goes live after setup wizard. Check with `sudo tailscale funnel status`

---

## 🔧 Services and Files

```bash
sudo systemctl status printer_watchdog.service camera_feed.service
sudo journalctl -u printer_watchdog.service -n 100 --no-pager
sudo journalctl -u camera_feed.service -n 100 --no-pager
```

| File | Purpose |
| :--- | :--- |
| `Printer_watchdog.py` | Watchdog brain, detection, timelapse, alerts |
| `web_feed.py` | Flask dashboard, auth, camera MJPEG, settings API |
| `3D-Spaghetti_Setup` | Installer with check, uninstall, reset |
| `requirements.txt` | `numpy`, `opencv-python-headless`, `flask`, `requests` |
| `settings.json` | Created, your AI and alert choices |
| `config.json` | Created, printer IP, webhook, user ID |
| `timelapses/` | Finished videos |
| `timelapses_raw/` | Raw frames per print, auto removed after stitch |
| `web_secret_key.txt` | Created, Flask secret |

---

## 🐛 Troubleshooting

- **Installer stuck after `Tailscale connected`:** Old builds blocked on `funnel --https=443 5000`. Current uses `timeout 15 ... --bg`. Fix: `Ctrl-C`, `sudo pkill -9 -f "tailscale funnel"`, then `sed -i 's/funnel --https=443 5000/funnel --https=443 --bg 5000/g' 3D-Spaghetti_Setup` and re-run
- **Funnel says `No serve config`:** Run `sudo tailscale funnel --bg --https=443 5000` or `sudo tailscale funnel --bg 5000`, then `sudo tailscale funnel status`
- **`inactive` services after uninstall:** Expected, run `sudo systemctl enable --now printer_watchdog.service camera_feed.service` or just rerun setup
- **White favicon or logo missing:** Put `logo.png` next to code and make sure `/logo.png` is exempt from auth, built in logo works even without file
- **Camera stutter:** Fixed to re-encode only when `current_snap.jpg` changes, JPEG quality 70
- **ETA not moving:** Fixed, needs a genuine start, power cycle is handled
- **No Discord alerts:** Check webhook URL and hit Test Discord in System tab

---

## 📁 Project Structure

```
3D-Spaghetti/
  Printer_watchdog.py # Installed.
  web_feed.py         # Installed.
  3D-Spaghetti_Setup  # Installed.
  requirements.txt    # Installed.
  index.html          # Installed.
  logo.png            # Installed.
  settings.json       # Created automatically.
  config.json         # Created automatically.
  timelapses/         # Created automatically.
  timelapses_raw/     # Created automatically.
```

---

## Like This Project?
Drop a ⭐ and share your prints!

## Want To Help Get It Out Of Beta?
Found a bug? Got an idea? Want to contribute? Join the [Discord](https://discord.gg/kBnARF7EV).
