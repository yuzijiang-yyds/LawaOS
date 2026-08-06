# LawaOS

You can install this project from 'https://github.com/yuzijiang-yyds/LawaOS/releases/tag/LawaOS-code'

This is a system expansion project. It can run on all platforms (iOS/iPadOS, macOS X, Windows, Android...). On iOS, it can be deeply linked with shortcuts, so I call it RootPretend Jailbreak.

⚠️Notice⚠️：LawaOS default password is 'LawaOS'

📖 Table of Contents

· Introduction
· Cross-Platform Support
  · iOS (iSH / a‑shell)
  · Docker (Any Platform)
  · Other Environments
· Unique Advantages on iOS
· Quick Start
· Command-Line Package Manager lawa
· Development & Extension
· FAQ

---

Introduction

LawaOS is a lightweight web desktop environment built on Flask, designed to run inside Alpine Linux emulation (iSH / a‑shell) or Docker containers. It offers a file manager, web terminal, app store, software package manager, and allows users to install third-party modules via .zip packages.

Thanks to the RootPretend design, LawaOS grants near‑root operational capabilities on iOS without jailbreaking (install packages, modify files, run services), while also deeply integrating with iOS Shortcuts to create a “pseudo‑jailbreak” experience.

---

Cross-Platform Support

iOS (iSH / a‑shell)

· iSH: Full Alpine Linux emulation, supports apk package management, runs all LawaOS features.
· a‑shell: Lightweight Python environment suitable for quickly starting the web service. Note that apk is unavailable, so APK installation does not work, but ZIP module installation, file manager, terminal, etc. work perfectly.

LawaOS automatically detects the runtime environment and gives appropriate hints.

Docker (Any Platform)

Docker is the recommended deployment method, running on Linux, macOS, Windows, and cloud servers:

```bash
docker run -d -p 5000:5000 --name lawaos lawaos/lawaos:latest
```

· No iSH emulation overhead, near‑native performance.
· Supports external volume mounting for persistent configuration and database.
· Can be launched with docker-compose.

Other Environments

· Standard Linux: Directly run python3 app.py after extracting the source code.
· Windows (WSL2): Same experience as Linux.
· macOS (native Python): Supported, but Docker is recommended.

---

Unique Advantages on iOS

1. RootPretend Sandbox Breakthrough
   Inside iSH, the user automatically gets root permissions, allowing apk add, modifying /etc, binding low ports, etc. This is equivalent to simulating a “jailbroken” device inside the iOS sandbox.
2. Deep Shortcuts Integration
   LawaOS exposes HTTP APIs (e.g. /api/command) that can be used by iOS Shortcuts to:
   · Start/stop the service with one tap
   · Periodically back up the database
   · Send system notifications (e.g., “New app installed”)
   · Create home screen icons (via “Add to Home Screen”)
     Example shortcut actions: get LawaOS status, remotely install a zip package, run a custom shell command.
3. Background Keep‑Alive Solutions
   Using iOS techniques like “playing silent audio” or “location updates” (user must configure manually), iSH / a‑shell can be kept running in the background for a long time, turning an iPhone into a portable server.
4. Fully Offline Capable
   All core functionalities of LawaOS do not require internet access (except for downloading from the App Store). Data is stored locally on the device, ideal for privacy‑sensitive scenarios.

---

Quick Start

Install on iSH / a‑shell

1. Download the appropriate zip file from the official website:
   · For a‑shell: download a-Shell-LawaOS.zip and extract it into ~/Documents (a‑shell’s root directory).
   · For iSH: download iSH&docker-LawaOS.zip and extract it into the iSH Alpine Linux filesystem (e.g., /root/LawaExtend).
2. Install Python dependencies (if not already present):
   ```bash
   pip3 install flask --user
   ```
3. Start the service:
   ```bash
   cd ~/Documents/LawaExtend   # or wherever you extracted
   python3 app.py
   ```

Run with Docker

```bash
# Pull the image (replace with actual image name)
docker pull lawaos/lawaos:latest

# Run the container
docker run -d \
  --name lawaos \
  -p 5000:5000 \
  -v lawaos_data:/LawaExtend \
  lawaos/lawaos:latest
```

Open http://localhost:5000 in your browser to see the launch screen.

---

Command-Line Package Manager lawa

lawa is a Python script located at bin/lawa that manages software packages. It supports remote installation, local zip installation, uninstallation, and searching.

Basic Usage

```bash
# List installed apps
./bin/lawa list

# Search remote sources
./bin/lawa search deepseek

# Install a remote package (auto‑download and deploy)
./bin/lawa install com_LawaOS_deepseek

# Install a local zip package
./bin/lawa install /path/to/app.zip

# Uninstall an app
./bin/lawa uninstall deepseek
```

Configure Software Sources

Edit etc/lawa.conf, one URL per line (supports HTTP directory listing):

```
http://lawaos.yuzijiang.space/share/
https://mirror.example.com/lawaos/
```

---

Development & Extension

Module Specification

· Module folder name must start with com_LawaOS_ (e.g. com_LawaOS_myapp).
· Inside the module, include an __init__.py that defines a Blueprint variable named <shortname>_bp (e.g. myapp_bp).
· Optional templates folder – any .html files will be copied to the global templates directory during installation.
· Optional static folder for static assets (CSS, JS, images).

Packaging & Distribution

Zip the module folder (and optionally a templates folder at the same level) into a .zip file, place it on any HTTP server that supports directory listing. Users can then install it via the built‑in App Store or the lawa install command.

Adding Custom Routes in LawaOS

No need to modify app.py. Just create a new module following the blueprint convention, and LawaOS will automatically scan and register it on startup.

---

FAQ

Q: a‑shell on iOS cannot use the apk command, what should I do?
A: Use ZIP module installation (pure Python modules), or switch to iSH.

Q: How to persist data in Docker?
A: Mount a volume to /LawaExtend. The database, uploaded wallpapers, and installed applications will all be stored inside that volume.

Q: How to customize desktop icons?
A: Create a .desktop file under com_LawaOS_desktop/desktop/. The first line is the URL to open, the second line is a Font Awesome 6 icon class (e.g., fas fa-cube).

Q: How can Shortcuts call LawaOS API?
A: Use the “Get Contents of URL” action, request http://127.0.0.1:5000/api/command, method POST, body being {"command":"your command"}.

Q: How to keep iSH running in the background on iOS?
A: Search for “iSH background keep‑alive” techniques. Common methods include playing silent audio, enabling location updates, or using Shortcuts to periodically wake the app.

Issues and pull requests are welcome. Official website: lawaos.yuzijiang.space

04/25 update：
The new version can make Shortcuts connect to LawaOS, and it can contorl iOS System(WiFi,Blueteeth and so on)
