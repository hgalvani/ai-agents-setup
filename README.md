# 🚀 Installation and Update Guide: Gemini CLI & Google Antigravity

> A comprehensive, step-by-step setup walkthrough for installing, configuring, and maintaining **Gemini CLI** and **Google Antigravity IDE** on **Ubuntu 24.04 LTS**. Covers permissions, Node.js runtimes, Electron SUID sandboxing, authentication protocols, automated permissions (Auto-Mode), vendor-agnostic rules, and Model Context Protocol (MCP) integrations.

[![OS: Ubuntu 24.04](https://img.shields.io/badge/Ubuntu-24.04%20LTS-E95420?logo=ubuntu&logoColor=white)](#)
[![Node: 20+ (LTS)](https://img.shields.io/badge/Node.js-20%2B%20LTS-339933?logo=node.js&logoColor=white)](#)
[![Protocol: MCP](https://img.shields.io/badge/Protocol-MCP%20Ready-blueviolet)](#)
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
- [Part 3: Antigravity Configuration & Permissions (Auto-Mode)](#part-3-antigravity-configuration--permissions-auto-mode)
  - [1. Configuration File Locations](#1-configuration-file-locations)
  - [2. Enabling Auto-Mode & Tool Approvals](#2-enabling-auto-mode--tool-approvals)
  - [3. Cloud & DevOps Guardrails](#3-cloud--devops-guardrails)
- [Part 4: Project Instructions & Vendor-Agnostic Rules](#part-4-project-instructions--vendor-agnostic-rules)
  - [1. Supported Instruction Formats](#1-supported-instruction-formats)
  - [2. The Universal Symlink Pattern](#2-the-universal-symlink-pattern)
  - [3. The "Second Brain" Architecture (Obsidian Vault Integration)](#3-the-second-brain-architecture-obsidian-vault-integration)
- [Part 5: Context Window Management, Token Usage & Costs](#part-5-context-window-management-token-usage--costs)
  - [1. Gemini Native Context Window Sizes](#1-gemini-native-context-window-sizes)
  - [2. Monitoring Context Saturation in the UI](#2-monitoring-context-saturation-in-the-ui)
  - [3. Real-Time Billing & Token Tracking](#3-real-time-billing--token-tracking)
- [Part 6: Model Context Protocol (MCP) Integration](#part-6-model-context-protocol-mcp-integration)
  - [1. Native MCP Support](#1-native-mcp-support)
  - [2. Configuring MCP Servers (AWS CLI, Filesystem, etc.)](#2-configuring-mcp-servers-aws-cli-filesystem-etc)
- [Part 7: Pro Tips & Troubleshooting](#part-7-pro-tips--troubleshooting)
  - [Desktop Launcher (.desktop file)](#desktop-launcher-desktop-file)
  - [Checking Sandbox Status](#checking-sandbox-status)
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

## Part 3: Antigravity Configuration & Permissions (Auto-Mode)

In agentic workflows, the assistant executes multi-step plans by directly reading/writing files and executing terminal commands. By default, Antigravity requires interactive user confirmation before running sensitive tools (such as running shell commands or modifying files).

To grant automated permissions and configure an **Auto-Mode** (analogous to `.claude/settings.json` in Claude Code), Antigravity uses a standard `settings.json` hierarchy.

### 1. Configuration File Locations

Configuration can be applied at two levels:

- **Global Configuration (User-wide):**
  ```bash
  ~/.config/Antigravity/settings.json
  ```
  *(Or via the UI by clicking the Settings gear icon in the bottom-left corner of the IDE).*

- **Project-Level Configuration (Workspace-specific):**
  Create a `.antigravity` directory at the root of your project:
  ```bash
  .antigravity/settings.json
  ```

### 2. Enabling Auto-Mode & Tool Approvals

To allow the agent to run development commands (e.g. `git`, `npm`, `ls`, `mkdir`) without prompting for manual approval on every single step, add the `agent.autoApprove` section to `settings.json`:

```json
{
  "agent.autoApprove": {
    "fileEdits": true,
    "terminalCommands": true,
    "allowedCommands": [
      "git *",
      "npm *",
      "ls *",
      "cat *",
      "mkdir *",
      "echo *",
      "test *"
    ]
  },
  "terminal.integrated.defaultProfile.linux": "zsh"
}
```

### 3. Cloud & DevOps Guardrails

> [!CAUTION]
> **Safety Guardrails:** Enabling `terminalCommands: true` grants the agent permission to execute shell commands directly.
>
> To safeguard your infrastructure and repositories:
> - **Never allow wildcard `sudo *` or `rm -rf *`** in `allowedCommands`.
> - Always require interactive confirmation for destructive commands, production deployments, and force-pushes (`git push --force`).
> - Restrict `allowedCommands` to idempotent, inspectable read/build commands.

---

## Part 4: Project Instructions & Vendor-Agnostic Rules

AI coding tools currently suffer from format fragmentation: Cursor uses `.cursorrules`, Claude Code uses `CLAUDE.md`, Gemini uses `GEMINI.md`, and OpenAI/ChatGPT agents use `AGENTS.md`.

### 1. Supported Instruction Formats

Google Antigravity natively parses multiple instruction formats at project root:
- **`GEMINI.md`**: Native format for Gemini and Antigravity.
- **`AGENTS.md`**: Emerging open, vendor-neutral standard supported natively by Antigravity and modern agent runtimes.
- **`.cursorrules`**: Supported for backward compatibility with Cursor workflows.

### 2. The Universal Symlink Pattern

To maintain a **single source of truth** across all AI agents and IDEs without duplicating instruction files, use Git-friendly symbolic links pointing to a primary `AGENTS.md` file:

```bash
# At the root of your Git repository:
touch AGENTS.md

# Create symbolic links for each agent ecosystem:
ln -s AGENTS.md GEMINI.md
ln -s AGENTS.md CLAUDE.md
ln -s AGENTS.md .cursorrules
```

Because Git preserves symbolic links natively, all developers and AI agents—regardless of tool choice—read and respect the exact same rules.

### 3. The "Second Brain" Architecture (Obsidian Vault Integration)

To prevent vendor lock-in and preserve contextual engineering memory across AI models:

- **Universal Markdown Storage:** Keep architecture notes, engineering decisions, and context in an **Obsidian Vault** as raw Markdown.
- **Agent Consumption:**
  - Link your Obsidian vault directly as a Git submodule or local path inside your workspace.
  - In `AGENTS.md`, reference key memory files:
    ```markdown
    ## Project Context & Knowledge Base
    Refer to architectural guidelines in: `docs/notes/architecture.md`
    ```
- **Portability:** If you switch models or tools (from Gemini to Claude, or vice versa), your entire knowledge base remains intact in portable Markdown.

---

## Part 5: Context Window Management, Token Usage & Costs

Agentic execution loops repeatedly consume and feed back terminal outputs, file contents, and tool results into the context window. Keeping track of context size is critical for maintaining agent speed and accuracy.

### 1. Gemini Native Context Window Sizes

Unlike older generation models limited to 128k or 200k tokens:

| Model | Context Window | Best Suited For |
| :--- | :--- | :--- |
| **Gemini 1.5 Flash / 2.0 Flash** | **1,000,000 tokens (1M)** | Fast execution, CLI scripting, daily refactoring |
| **Gemini 1.5 Pro / 2.0 Pro** | **2,000,000 tokens (2M)** | Large codebases, complex architectural reasoning |

> [!TIP]
> While Gemini's 1M–2M context window easily accommodates vast codebases, excessive terminal log accumulation can increase response latency. Keep conversations scoped to distinct features or tasks.

### 2. Monitoring Context Saturation in the UI

In Google Antigravity:
- **Chat Status Indicator:** Hovering over the model selector at the bottom of the chat panel reveals a circular gauge and percentage indicating the current context window utilization against the 1M/2M ceiling.
- **Session Compaction:** If context usage climbs excessively, start a fresh conversation or prune unnecessary background logs.

### 3. Real-Time Billing & Token Tracking

Antigravity focuses on code generation and does not calculate exact dollar costs in the UI. To track precise token consumption and billing:

- **Google AI Studio (API Key Users):**
  Visit [aistudio.google.com](https://aistudio.google.com/?utm_source=gemini) > **Plan & Billing** or **Dashboard** to view real-time request counts, token consumption graphs, and cost breakdowns.
- **Google Cloud Console (Vertex AI Users):**
  Monitor token usage under **Vertex AI > Metrics / Cloud Billing** for enterprise quotas and budgets.

---

## Part 6: Model Context Protocol (MCP) Integration

The **Model Context Protocol (MCP)** is an open standard (initiated by Anthropic and adopted across the AI industry) that enables agents to interact with external tools, APIs, filesystems, and databases securely.

### 1. Native MCP Support

Google Antigravity and Gemini CLI provide **native support for MCP servers**. Any MCP server compatible with Claude Desktop or Cursor works directly in Antigravity.

### 2. Configuring MCP Servers (AWS CLI, Filesystem, etc.)

Declare your MCP servers inside your global (`~/.config/Antigravity/settings.json`) or workspace (`.antigravity/settings.json`) configuration under the `mcpServers` object:

```json
{
  "mcpServers": {
    "aws": {
      "command": "uvx",
      "args": ["mcp-server-aws-cli"]
    },
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/home/hentox/Sources"
      ]
    },
    "github": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-github"
      ],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "YOUR_GITHUB_PAT_HERE"
      }
    }
  }
}
```

Once declared, Antigravity automatically detects and exposes the MCP server tools to the agent during interactive sessions.

---

## Part 7: Pro Tips & Troubleshooting

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

If you encounter Chromium/Electron sandbox issues after an OS upgrade:
```bash
ls -l /opt/antigravity/chrome-sandbox
# Must display SUID root: -rwsr-xr-x 1 root root
```

---

## 📄 License

This repository is licensed under the [MIT License](LICENSE).
