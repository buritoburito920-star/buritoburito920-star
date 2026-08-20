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

### 📓 Side Notes
- **Hardware Compatibility** - Currently optimized **only** for the FlashForge AD5M <sub>(Adventurer 5M)</sub>. It may function with the AD5X <sub>(Adventurer 5X)</sub>, but it remains officially untested.
- **Project Status** - 3D-Spaghetti is currently in active **Beta** and may contain minor bugs.
- **AI Constraints** - This platform relies heavily on computer vision models which <sub>***can*** occasionally make mistakes</sub>.
- **Safety First** - The installation environment is **100% reversible** and will not damage your Raspberry Pi or your 3D printer.
- **Disclaimer** - The Creator is not responsible for any software issues arising from unguided modifications to the codebase.

---

## ⚙️ Requirements <sub>(Hardware check)</sub>

| Minimum Requirements | Recommended Requirements |
| :--- | :--- |
| • Raspberry Pi 3 (4GB RAM)<br>• 720p Pi Camera Module<br>• High-quality power brick & cable <sub>(prevents Pi voltage crashes)</sub><br>• High-speed Micro SD card | • Raspberry Pi 4 (8GB RAM)<br>• 1080p Pi Camera Module<br>• High-quality power brick & cable <sub>(prevents Pi voltage crashes)</sub><br>• High-speed Micro SD card |

---

## 🚀 Setup & Installation

> [!NOTE]
> This setup process was actively verified and tested using a Raspberry Pi 4. Performance or configuration steps might slightly deviate on other single-board computers or native PC environments.

### 📍 Step 1: Network & Account Preparation
1. Identify and note down both your **Raspberry Pi's IP address** and your **FlashForge Printer's IP address**.
2. **(Optional Discord Alerts)**: Obtain your unique **Discord Developer ID** if you want personalized ping alerts.
3. **(Optional Discord Alerts)**: Create a private Discord server, generate a new bot integration, and copy its dedicated **Webhook URL**.

### 💻 Step 2: Tooling Configuration
1. Download and install [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) and [FileZilla](https://filezilla-project.org) onto your primary desktop or laptop workspace. *(Alternative SSH/SFTP clients will work exactly the same).*
2. Download the three core repository project scripts onto your local machine.

### 🔌 Step 3: Establish Remote Connectivity
* **Command Line Link (SSH)**: Open **PuTTY**, enter your Raspberry Pi’s IP address into the **Host Name** field, set the connection type to **SSH (Port 22)**, and click **Open**. When the terminal prompts you, type your Pi’s `login as:` username and password.
* **File System Link (SFTP)**: Open **FileZilla**. In the top connection bar, configure the following fields before clicking **Quickconnect**:
  * **Host:** `sftp://[Your_Pi_IP_Address]` *(do not include the brackets)*
  * **Username / Password:** Your native Raspberry Pi login credentials
  * **Port:** `22`

### 📂 Step 4: Code Deployment & Configuration
1. Utilizing the FileZilla interface, drag and drop the folder into the Micro SSD *If you have one.* directly from your PC into your Raspberry Pi. *It is best practice to keep these isolated inside their own dedicated directory on your Micro SD card. Directory likley to be `/media/[your_username]/[the_name_that_your_Pi_named_the_Micro_SSD]*
2. Switch to your active PuTTY terminal, navigate to your newly created project directory using `cd /[your_directory]/3D-Spaghetti`, and Enter key:
   Then Run the Bash script using `bash 3D-Spaghetti_Setup`
   
---
## Like the Project?
give it a ⭐ :).

## Help in Development to get out of beta.
Found a bug? Have a question? Want to Contribute? Join my [Discord](https://discord.gg/kBnARF7EV).
