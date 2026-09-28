# 🚀 Installation and Update Guide: Gemini CLI & Google Antigravity

> A comprehensive, step-by-step setup walkthrough for installing and maintaining **Gemini CLI** and **Google Antigravity IDE** on **Ubuntu 24.04 LTS**, highlighting pitfalls to avoid (permissions, Node.js runtimes, Electron SUID sandboxing, authentication protocols) and safe update procedures.

[![OS: Ubuntu 24.04](https://img.shields.io/badge/Ubuntu-24.04%20LTS-E95420?logo=ubuntu&logoColor=white)](#)
[![Node: 20+ (LTS)](https://img.shields.io/badge/Node.js-20%2B%20LTS-339933?logo=node.js&logoColor=white)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## 📋 Table of Contents

- [Part 1: Step-by-Step Installation Guide](#part-1-step-by-step-installation-guide)
  - [1. Prerequisites & Node.js Management via NVM](#1-prerequisites--nodejs-management-via-nvm)
  - [2. Gemini CLI Installation & Authentication](#2-gemini-cli-installation--authentication)
  - [3. Google Antigravity IDE Installation & Setup](#3-google-antigravity-ide-installation--setup)
- [Part 2: Update Procedures](#part-2-update-procedures)
  - [1. Updating Gemini CLI](#1-updating-gemini-cli)
  - [2. Updating Google Antigravity IDE](#2-updating-google-antigravity-ide)
    - [Method A: Manual Update (Tarball Archive)](#method-a-manual-update-tarball-archive)
    - [Method B: Update via Official Setup Script](#method-b-updating-via-official-antigravity-cli-script)
- [Part 3: Pro Tips & Troubleshooting](#part-3-pro-tips--troubleshooting)
- [License](#license)

---

## Part 1: Step-by-Step Installation Guide

### 1. Prerequisites & Node.js Management via NVM

> [!WARNING]
> **Pitfall to avoid:** Do **not** use Ubuntu's default `nodejs` package via `apt` (which is often outdated, e.g., v18.x). The `@google/gemini-cli` package requires **Node.js 20+** at minimum.
>
> Furthermore, running `npm install -g` with system-packaged Node leads to permission errors (`EACCES: permission denied` on `/usr/local/lib/node_modules`). Using **NVM** (*Node Version Manager*) isolates Node.js in user-space and completely resolves permission issues without needing `sudo`.

#### Installation steps:

1. **Install NVM:**
   ```bash
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
   ```

2. **Load NVM into your current shell session:**
   ```bash
   export NVM_DIR="$HOME/.nvm"
   [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
   ```

3. **Make configuration persistent:**
   - *For Zsh users (`~/.zshrc`):*
     ```bash
     echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.zshrc
     echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ~/.zshrc
     ```
   - *For Bash users (`~/.bashrc`):*
     ```bash
     echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.bashrc
     echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ~/.bashrc
     ```

4. **Install and activate the latest Node.js LTS version (v20+):**
   ```bash
   nvm install --lts
   nvm use --lts
   ```

5. **Verify the installation:**
   ```bash
   node -v  # Should output >= v20.x.x
   npm -v
   ```

---

### 2. Gemini CLI Installation & Authentication

1. **Install Gemini CLI globally:**
   ```bash
   npm install -g @google/gemini-cli
   ```

> [!CAUTION]
> **Pitfall to avoid (Authentication):**
> Selecting option `1. Sign in with Google` returns an error for personal accounts:
> *"This client is no longer supported for Gemini Code Assist for individuals."*
>
> **Solution:** You must authenticate using an **API Key**.

2. **Generate your API Key:**
   - Head over to [Google AI Studio](https://aistudio.google.com/?utm_source=gemini).
   - Generate a new Gemini API Key.

3. **Persist the API key in your shell configuration:**
   - *For Zsh:*
     ```bash
     echo 'export GEMINI_API_KEY="YOUR_API_KEY_HERE"' >> ~/.zshrc
     source ~/.zshrc
     ```
   - *For Bash:*
     ```bash
     echo 'export GEMINI_API_KEY="YOUR_API_KEY_HERE"' >> ~/.bashrc
     source ~/.bashrc
     ```

4. **Launch Gemini CLI:**
   ```bash
   gemini
   ```

---

### 3. Google Antigravity IDE Installation & Setup

> [!IMPORTANT]
> **Pitfalls to avoid:**
> 1. **SUID Sandbox Error (`FATAL:setuid_sandbox_host.cc`):** Running the executable directly from your user directory fails because `chrome-sandbox` is not owned by `root` and lacks the required SUID permission (`4755`).
> 2. **Browser Redirection Popup (`antigravity://`):** Ubuntu may display a *"Failed to launch Software"* dialog because the custom protocol scheme `antigravity://` is not registered with `gnome-software`. **You can safely ignore this alert**: authentication completes automatically in the background through the local Electron server.

#### Clean Installation Procedure in `/opt`:

1. **Extract and relocate application directory to `/opt`:**
   ```bash
   sudo mv ~/Downloads/Antigravity-x64 /opt/antigravity
   ```

2. **Fix ownership and permissions for the Electron sandbox:**
   ```bash
   sudo chown root:root /opt/antigravity/chrome-sandbox
   sudo chmod 4755 /opt/antigravity/chrome-sandbox
   ```

3. **Create a symlink in the system `PATH`:**
   ```bash
   sudo ln -s /opt/antigravity/antigravity /usr/local/bin/antigravity
   ```

#### Launching in Background (Silent Terminal Output):

To start the IDE in the background without locking your terminal session or flooding it with DevTools/Electron runtime logs:
```bash
nohup antigravity > /dev/null 2>&1 &
```

*(Optional)* You can also use `disown`:
```bash
antigravity > /dev/null 2>&1 & disown
```

---

## Part 2: Update Procedures

### 1. Updating Gemini CLI

The npm package can be upgraded directly via `npm`:

```bash
# Check the currently installed version
gemini --version

# Update to the latest global release
npm update -g @google/gemini-cli
```

---

### 2. Updating Google Antigravity IDE

> [!WARNING]
> **Pitfall to avoid (In-App UI Updates):**
> Built-in updates through the IDE interface (*Antigravity > Check for Updates*) will fail or be unable to overwrite application binaries because `/opt/antigravity` is owned by `root`. Updates must be performed via the terminal.

#### Method A: Manual Update (Tarball Archive)

When a new version release archive is downloaded:

1. **Navigate to the downloads directory and extract the archive:**
   ```bash
   cd ~/Downloads
   tar -zxf Antigravity.tar.gz
   ```

2. **Remove the outdated installation and replace it:**
   ```bash
   sudo rm -rf /opt/antigravity
   sudo mv Antigravity-x64 /opt/antigravity
   ```

3. **Re-apply SUID permissions to `chrome-sandbox`:**
   ```bash
   sudo chown root:root /opt/antigravity/chrome-sandbox
   sudo chmod 4755 /opt/antigravity/chrome-sandbox
   ```

#### Method B: Updating via Official Antigravity CLI Script

If you installed and manage Antigravity using the official setup script, re-running it upgrades to the latest release automatically:

```bash
curl -fsSL https://antigravity.google/cli/install.sh | bash
```

---

## Part 3: Pro Tips & Troubleshooting

### Desktop Launcher (.desktop file)
To integrate Google Antigravity into your Ubuntu GNOME application launcher (Dash / Super key):

```bash
mkdir -p ~/.local/share/applications
cat << 'EOF' > ~/.local/share/applications/antigravity.desktop
[Desktop Entry]
Name=Google Antigravity
Comment=Next-generation Agentic AI IDE
Exec=/opt/antigravity/antigravity %F
Icon=/opt/antigravity/resources/app/resources/icons/antigravity.png
Terminal=false
Type=Application
Categories=Development;IDE;
StartupWMClass=Antigravity
EOF
update-desktop-database ~/.local/share/applications/
```

### Checking Sandbox Status
If you ever encounter Chromium/Electron sandbox issues after an OS upgrade:
```bash
ls -l /opt/antigravity/chrome-sandbox
# Must show: -rwsr-xr-x 1 root root
```

---

## 📄 License

This repository is licensed under the [MIT License](LICENSE).
