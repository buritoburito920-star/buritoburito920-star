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
Unlike systems, 3D-Spaghetti controls your machine natively without requiring any physical modding or firmware flashing. This ensures you never risk **voiding your FlashForge factory warranty**.

### ✨ Key Capabilities
- **Spaghetti Detection** - Automatically pauses the print the moment a failure is detected.
- **Global Print Monitoring** - Watch your live print camera stream securely from anywhere in the world.
- **Remote Printer Control** - Access an interactive control panel to manage your machine on the go.
- **Instant Failure Alerts** - Receive immediate notifications with photo evidence. <sub>(Also alerts on print start, end, or pauses)</sub>
- **Time-lapse Recording** - Every finished print becomes a downloadable MP4.
- **~ETA, Elapsed, and ~Total Time** - Every print after a few layers you will get a estimated time left <sub>(**ETA**)</sub>, you will also get how long it has been printing <sub>(**Elapsed**)</sub>, and you will get an estimation on the Total Time left <sub>(**Total Time**)</sub>.

> [!Warning]
> - **Hardware Compatibility** - Currently optimized **only** for the FlashForge AD5M <sub>(Adventurer 5M)</sub>. It may function with the AD5X <sub>(Adventurer 5X)</sub>, but it remains officially untested.
> - **Project Status** - 3D-Spaghetti is currently in active **Beta** and may contain minor bugs.
> - **AI Constraints** - This platform relies heavily on computer vision which ***can*** occasionally make mistakes.
> - **Safety First** - The installation environment is **100% reversible** and will not damage your Raspberry Pi or your 3D printer.
> - **Disclaimer** - The Creator is not responsible for any software issues arising from unguided modifications to the codebase.

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

---

## ⚙️ Settings

Open via `OPEN` in the right column.

### ALERTS tab:
- Print Started.
- Print Finished.
- Paused (Manual).
- Paused (Auto).
- Canceled (Manual).
- Canceled (Auto)

### AI Settings Tab:
- **Failure Action:** `Pause` or `Cancel` **Note AI May Be False,** or `Nothing (Alert Only)`
- **AI Confidence %:** 10% to 100%, default 100%. Minimum certainty before a detection counts.
- **Consecutive Hits:** 1 to 30. How many back to back detections before counting as a **Failure Action**: Default `10`.
- **HUD Bounding Boxes:** Nozzle Bounding box detection: Default `True`.
- **Timelapse:** Enable or disable Timelapse recording.
- **Timelapse HUD Overlay:** When on, **Hud Bounding Boxes** and **Status Bar** into timelapse frames. When off, saves clean video: Default `True`.
- **Timelapse Interval:** 3 to 300. Seconds between captured frames. The bigger the number is the faster the video is, the lower the slower the timelapse is: Default `True`.

### System Tab:
- **Printer IP:** FlashForge host on LAN.
- **Discord Webhook:** Webhook URL, ***Optional***.
- **Discord User ID:** Mention ID, ***Optional***.

### Panels:
- Detector panel shows `Status`, `Certainty`, `Hits`, `Cycle`.
- Timelapses panel lists finished videos with size and date and download links. Refreshes every 60s.

---
> # How Stuff Works
>
> ## Time-lapse
>
> - Captures raw frames to `timelapses_raw/<timestamp>/` while printing, every `timelapse_interval` seconds.
> - Finalizes on print end to `timelapses/timelapse_<timestamp>.mp4` (fallback `.avi` MJPG, 5 fps). Too few frames are discarded.
> - If `Timelapse HUD Overlay` is on, each frame includes the live HUD boxes and a top bar `PRINTING | Layer x/y | AI n% | Hit a/b | MM-DD HH:MM:SS`. If off, frames are clean.
> - Download via dashboard Time-Lapses panel or `GET /timelapse/<name>`, list via `GET /api/timelapses`.
>
>
> ## Detection
>
> - Single focus zone `ChangeDetector` against a stored baseline (color BGR). Diff is per pixel strongest BGR channel vs `PIXEL_DIFF_THRESHOLD = 90` (not grayscale, catches same color spaghetti).
> - Setup: 185s setup timer then baseline build `BASELINE_BUILD_CYCLES`, then detection will be active.
> - Failure Action: Triggers only after `failure_certainty` and `consecutive_hits` are met.


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
