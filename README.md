<div align="center">
  <img width="100%" height="auto" alt="3D-spaghetti" src="https://github.com/user-attachments/assets/2c0f3d4d-40cf-47e8-bb76-49a6496d4417" />
</div>

# 3D-Spaghetti

<p align="center">
  Tired of your prints failing and wasting filament while you are away or sleeping? Say goodbye to print anxiety. The <strong>3D-Spaghetti Monitor</strong> watches your printer in real-time, automatically pausing your machine when a failure starts so you can rest easy.
</p>

***

## 📋 Overview

It is not called 3D-Spaghetti because it *makes* spaghetti **it detects it**. Not detecting only, it provides a full remote control panel.

### Why choose 3D-Spaghetti over alternatives?
Unlike systems like OctoPrint or Obico, 3D-Spaghetti controls your machine natively without requiring any physical modding or firmware flashing. This ensures you never risk **voiding your FlashForge factory warranty**.

### ✨ Key Capabilities
- **Spaghetti Detection** - Automatically pauses the print the moment a failure is detected.
- **Global Print Monitoring** - Watch your live print camera stream securely from anywhere in the world.
- **Remote Printer Control** - Access an interactive control panel to manage your machine on the go.
- **Instant Failure Alerts** - Receive immediate notifications with photo evidence. <sub>(Also alerts on print start, end, or pauses)</sub>
- **Time-lapse Recording** - Every finished print becomes a downloadable MP4 with optional HUD overlay.
- **Guided First Visit Tour** - Interactive walkthrough that teaches every setting on first load.

### 📓 Side Notes
- **Hardware Compatibility** - Currently optimized **only** for the FlashForge AD5M <sub>(Adventurer 5M)</sub>. It may function with the AD5X <sub>(Adventurer 5X)</sub>, but it remains officially untested.
- **Project Status** - 3D-Spaghetti is currently in active **Beta** and may contain minor bugs.
- **AI Constraints** - This platform relies heavily on computer vision which <sub>***can*** occasionally make mistakes</sub>.
- **Safety First** - The installation environment is **100% reversible** and will not damage your Raspberry Pi or your 3D printer.
- **Disclaimer** - The Creator is not responsible for any software issues arising from unguided modifications to the codebase.

---

## ⚙️ Requirements <sub>(Hardware check)</sub>

| Minimum Requirements | Recommended Requirements |
| :--- | :--- |
| • Raspberry Pi 3 (4GB RAM)<br>• 720p Pi Camera Module<br>• High-quality power brick & cable <sub>(prevents Pi voltage crashes)</sub><br>• High-speed Micro SD card | • Raspberry Pi 4 (8GB RAM)<br>• 1080p Pi Camera Module<br>• High-quality power brick & cable <sub>(prevents Pi voltage crashes)</sub><br>• High-speed Micro SD card |

**Software:** Raspberry Pi OS (Bullseye/Bookworm), Python 3.11, `rpicam-vid` / `libcamera-apps`, Tailscale.

---

## 🚀 Setup & Installation

> [!NOTE]
> This setup was actively verified on a Raspberry Pi 4. Steps may vary slightly on other boards or PC environments.

### 📍 Step 1: Network & Account Preparation
1. Note your **Raspberry Pi IP** and your **FlashForge Printer IP** (port `8899` is fixed).
2. **Optional Discord Alerts:** Get your **Discord User ID** for pings and create a private server webhook URL.

### 💻 Step 2: Tooling Configuration
1. Install [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) and [FileZilla](https://filezilla-project.org) on your PC (any SSH/SFTP client works).
2. Download the three core files: `Printer_watchdog.py`, `web_feed.py`, `3D-Spaghetti_Setup` (+ `requirements.txt` and `logo.png` optional).

### 🔌 Step 3: Establish Remote Connectivity
* **SSH:** PuTTY -> Host Name = Pi IP, Port `22`, SSH -> Open -> login.
* **SFTP:** FileZilla -> Host `sftp://[Pi_IP]` -> Username/Password -> Port `22` -> Quickconnect.

### 📂 Step 4: Code Deployment & Configuration
1. In FileZilla drag the project folder to your Pi, recommended path `/media/[user]/[MicroSD]/3D-Spaghetti` or `~/3D-Spaghetti`.
2. In PuTTY:
   ```bash
   cd /path/to/3D-Spaghetti
   bash 3D-Spaghetti_Setup --check   # read-only check, changes nothing
   bash 3D-Spaghetti_Setup           # full install
   ```
3. On first install open the dashboard `http://[Pi_IP]:5000` and complete the setup wizard (printer IP, webhook). The public Tailscale URL appears after `config.json` is created.

### What the installer does
1. **Python:** Uses `~/miniforge3/bin/python3`, `~/mambaforge`, or `/opt/miniforge3` if present, otherwise creates `env/` via micromamba (or `python3-venv` on armv6). Installs `requirements.txt` if needed.
2. **Camera:** Installs `libcamera-apps` if `rpicam-vid` is missing.
3. **Tailscale:** Installs via `tailscale.com/install.sh` if missing, runs `tailscale up` and shows `https://login.tailscale.com/a/...` if not logged in, adds passwordless `sudoers.d/ad5m-funnel`, enables `funnel --bg --https=443 5000` only after `config.json` exists.
4. **Services:** Creates `printer_watchdog.service` (`python -u Printer_watchdog.py`) and `camera_feed.service` (`python web_feed.py`) if not already active. Skips if both are running.
5. **Summary:** Prints `Local dashboard: http://[Pi_IP]:5000` and `Public dashboard: https://[name].ts.net` (fallback via `tailscale status --json` DNSName).

### Installer flags
```bash
bash 3D-Spaghetti_Setup --check      # verify only
bash 3D-Spaghetti_Setup --uninstall  # remove services + funnel, keep config/password
bash 3D-Spaghetti_Setup --reset      # as above plus delete config.json, settings, password (fresh wizard)
```

Re-running without flags is idempotent and safe.

---

## 🖥️ Dashboard

Open `http://[Pi_IP]:5000` locally or `https://[name].ts.net` remotely. Login uses the password you set in the wizard.

### Layout
- **Top bar:** Printer state badge, progress, elapsed/ETA, hotend and bed temps, help `?` to replay tour, `AD5M` brand with `logo.png` favicon.
- **Left column:** Print Status (progress bar, layer `cur/tot`, AI failure %, elapsed/ETA, Pause/Resume/Cancel) + G-code Console (Tab autocomplete, Enter to send).
- **Center column:** Live Feed `640x480` MJPEG, green nozzle box, orange focus zone, `FAIL%` pill, 3m 05s setup timer, baseline and active states.
- **Right column:** Temps and Controls (hotend/bed SET/OFF), Settings panel, Detector (Status/Certainty/Hits/Cycle), AI Confidence, Temps chart, Time-Lapses panel.

### First visit tour
Auto starts once via `localStorage`. 27 steps: top bar, print status, console, live feed, temps and settings, detector, time-lapses, then every setting tab and row. Each step highlights the target row, auto opens the settings panel and switches tabs. Skip at any time, replay with `?`.

---

## ⚙️ Settings

Open via `OPEN` in the right column.

### ALERTS tab
Six Discord toggles, each independent, no Save needed:
- Print Started, Print Finished, Paused (Manual), Paused (Auto), Canceled (Manual), Canceled (Auto)

### AI SETTINGS tab (needs Save Settings)
- **Failure Action:** `Pause` or `Cancel` (note: AI may be wrong) or `Nothing (Alert Only)`. Warning text below explains auto actions can be incorrect.
- **AI Confidence %:** 10 to 100, default 75. Minimum certainty before a detection counts. Higher means fewer false hits.
- **Consecutive Hits:** 1 to 30, default 6. How many back to back detections before acting.
- **HUD Bounding Boxes:** Show or hide green nozzle and orange zone on live feed.
- **Timelapse:** Enable or disable recording.
- **Timelapse HUD Overlay:** When on, burns boxes and status bar into timelapse frames. When off, saves clean video.
- **Timelapse Interval s:** 3 to 300, default 10. Seconds between captured frames.

### SYSTEM tab (needs Save System)
- **Printer IP:** FlashForge host on LAN.
- **Discord Webhook:** Webhook URL, leave empty to disable Discord.
- **Discord User ID:** Optional mention ID.
- Buttons: Save System, Test Discord.

Detector panel shows `Status`, `Certainty`, `Hits`, `Cycle`. Time-Lapses panel lists finished videos with size and date and download links. Refreshes every 60s.

---

## 🎞️ Time-lapse

- Captures raw frames to `timelapses_raw/<timestamp>/` while printing, every `timelapse_interval` seconds.
- Finalizes on print end to `timelapses/timelapse_<timestamp>.mp4` (fallback `.avi` MJPG, 5 fps). Too few frames are discarded.
- If `Timelapse HUD Overlay` is on, each frame includes the live HUD boxes and a top bar `PRINTING | Layer x/y | AI n% | Hit a/b | MM-DD HH:MM:SS`. If off, frames are clean.
- Download via dashboard Time-Lapses panel or `GET /timelapse/<name>`, list via `GET /api/timelapses`.

---

## 🔍 How detection works

- Single focus zone `ChangeDetector` against a stored baseline (color BGR). Diff is per pixel strongest BGR channel vs `PIXEL_DIFF_THRESHOLD = 90` (not grayscale, catches same color spaghetti).
- Flow: 185s setup timer (counts down even if nozzle is hidden) then baseline build `BASELINE_BUILD_CYCLES`, then active. Supports brief nozzle lost grace via `last_good_focus_rect` so boxes stay visible.
- Triggers only after `failure_certainty` and `consecutive_hits` are met. Requires 3 consecutive idle polls to declare finished, with `FINISH_ALERTED` reset on real print start, prevents end spam.
- Printer unreachable handling: idle/off shows `IDLE (SLEEP MODE)` and suppresses alerts, mid print unreachable keeps `PRINTING` so ETA stays live.
- ETA and elapsed fixed to start clock on genuine print start (`PRINT_START_TIME is None` or idle big enough), so power cycling does not freeze timers.

---

## 🌐 Remote access

- **Local:** `http://[Pi_IP]:5000`
- **Public:** `https://[name].ts.net` via `tailscale funnel --bg --https=443 5000`. Enabled only after setup wizard. Status via `sudo tailscale funnel status`, fallback `tailscale status --json` DNSName.
- Installer and `web_feed.py` both use `--bg` so the command never blocks. Passwordless sudo allowed via `/etc/sudoers.d/ad5m-funnel`.
- Custom `logo.png` placed next to code is served unauthenticated at `/logo.png` and used as top bar logo and favicon.

---

## 🔧 Services and files

```bash
sudo systemctl status printer_watchdog.service camera_feed.service
sudo journalctl -u printer_watchdog.service -n 100 --no-pager
sudo journalctl -u camera_feed.service -n 100 --no-pager
```

| File | Purpose |
| :--- | :--- |
| `Printer_watchdog.py` | Main watchdog, detection, timelapse, Discord alerts |
| `web_feed.py` | Flask dashboard, auth, camera MJPEG, settings API |
| `3D-Spaghetti_Setup` | Installer with --check/--uninstall/--reset |
| `requirements.txt` | `numpy`, `opencv-python-headless`, `flask`, `requests` |
| `settings.json` | AI and alert settings |
| `config.json` | Printer IP, webhook, user ID |
| `timelapses/` | Finished videos |
| `timelapses_raw/` | Per print raw frames, removed after finalize |
| `web_secret_key.txt` | Flask secret |

---

## 🐛 Troubleshooting

- **Installer appears stuck after `Tailscale connected`:** Older builds blocked on `funnel --https=443 5000`. Current build uses `timeout 15 ... --bg`. If stuck, `Ctrl-C`, `sudo pkill -9 -f "tailscale funnel"`, patch with `sed -i 's/funnel --https=443 5000/funnel --https=443 --bg 5000/g' 3D-Spaghetti_Setup`, re-run.
- **Funnel shows `No serve config`:** Run `sudo tailscale funnel --bg --https=443 5000` or `sudo tailscale funnel --bg 5000`, then `sudo tailscale funnel status`.
- **`inactive` services after --uninstall:** Expected. Re-run setup or `sudo systemctl enable --now printer_watchdog.service camera_feed.service`.
- **White favicon / logo not showing:** Ensure `logo.png` is next to code and `/logo.png` is exempt from auth (fixed).
- **Camera re-encoding stutter:** Fixed to re-encode only when `current_snap.jpg` mtime changes, JPEG quality 70.
- **ETA or elapsed not moving:** Fixed in current build, requires genuine start condition.
- **No Discord alerts:** Check webhook URL and use Test Discord in System tab.

---

## 📁 Project structure

```
3D-Spaghetti/
  Printer_watchdog.py
  web_feed.py
  3D-Spaghetti_Setup
  requirements.txt
  index.html
  logo.png            # optional, your branding
  settings.json       # created
  config.json         # created
  timelapses/
  timelapses_raw/
```

---

## Like the Project?
Give it a ⭐ :)

## Help in Development to get out of beta.
Found a bug? Have a question? Want to Contribute? Join my [Discord](https://discord.gg/kBnARF7EV).
