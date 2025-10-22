# Auto-Installer 🤖

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![AI Tool Compatible](https://img.shields.io/badge/AI-Compatible-brightgreen.svg)](https://github.com/anombyte93/auto-installer)

**Autonomous AI agent that installs ANYTHING on any system with zero manual intervention.**

Handles system packages, npm/pip/cargo packages, API configurations, environment setup, VM deployments, and more.

## Quick Start

```bash
git clone https://github.com/anombyte93/auto-installer.git
cd auto-installer
```

Then use with any AI tool below ⬇️

## 🤖 AI Tool Compatibility

| Tool | Type | Installation | Status |
|------|------|--------------|--------|
| [Claude Code](https://claude.com/code) | CLI Skill | `cp -r . ~/.claude/skills/auto-installer/` | ✅ Native |
| [Open Codex](https://github.com/ymichael/open-codex) | CLI Agent | See below | ✅ Full |
| [Gemini CLI](https://github.com/google-gemini/gemini-cli) | CLI Agent | See below | ✅ Full |
| [Grok CLI](https://github.com/superagent-ai/grok-cli) | CLI Agent | See below | ✅ Full |
| [OpenAI Codex](https://github.com/openai/codex) | CLI Agent | See below | ✅ Full |
| [Continue.dev](https://www.continue.dev/) | IDE Extension | VSCode/JetBrains | ✅ Full |
| [Aider](https://aider.chat/) | CLI Agent | See below | ✅ Full |
| ChatGPT CLI | CLI | N/A | ✅ Full |
| Any AI with shell access | Custom | Paste SKILL.md | ✅ Universal |

## 📦 Installation by Tool

### Claude Code (Skill System)

```bash
# Install as a skill
cp -r . ~/.claude/skills/auto-installer/

# Auto-activates when you say:
# "install docker"
# "configure gemini API in open-codex"
# "install pytest on my ubuntu VM"
```

### CLI Tools (Open Codex, Gemini CLI, Grok CLI, Codex, Aider)

**All CLI tools follow the same pattern:**

```bash
cd auto-installer

# Start your AI CLI tool
open-codex          # or
gemini-cli          # or
grok-cli            # or
codex               # or
aider

# Tell the AI:
"Read SKILL.md and install [WHAT YOU WANT]"
```

**Tool-Specific Context Flags (if supported):**

```bash
# Gemini CLI
gemini-cli --context SKILL.md

# Some tools auto-read files in directory
open-codex  # Automatically detects SKILL.md
```

### Continue.dev (IDE Extension)

1. Install Continue extension in VSCode/JetBrains
2. Open the auto-installer directory in your IDE
3. Use Continue chat: `@SKILL.md install docker`

### Universal Method (Any AI)

Paste this prompt to any AI with shell access:

```
You are an autonomous software installer. Read the file SKILL.md in the current directory to understand your capabilities and workflow.

CRITICAL RULES:
1. Execute ALL commands yourself - NEVER give me manual instructions
2. Only ask for: passwords, disambiguation, or risky operation confirmations
3. Report "READY TO USE" only when fully installed and verified

Now install: [WHAT YOU WANT TO INSTALL]
```

## 🎯 What It Does

✅ **Auto-detects** installation type (system pkg, npm, API config, etc.)
✅ **System packages** - docker, postgresql, redis, nginx, etc.
✅ **Language packages** - npm, pip, cargo, gem, composer, go
✅ **API configurations** - Set API keys, create config files
✅ **Environment setup** - PATH, .bashrc, .env files
✅ **VMs & remote servers** - Auto-configures SSH, jump hosts
✅ **GPU drivers** - NVIDIA drivers + nvidia-docker with reboot
✅ **Safety checkpoints** - VM snapshots before changes
✅ **Error recovery** - Automatic retry with fixes
✅ **Verification** - Functional tests, not just binary checks

## 💡 Examples

### System Packages
```
install docker
install node 20 and postgres 15
install rust
```

### NPM/Language Packages
```
install npm package open-codex
install pytest and numpy
install ruby gem rails
```

### API & Configuration
```
configure gemini API in open-codex
set up my AWS credentials
create .env file with DATABASE_URL
```

### VM & Remote
```
install pytest on my ubuntu VM
install docker on staging and production VMs
install rust on gpu-server.company.com via bastion.company.com
```

### GPU & ML
```
install docker with GPU support and pytorch on my GPU server
```

## 📚 Supported Installation Types

### System Packages
- **Package Managers**: apt, brew, dnf, pacman, choco
- **Examples**: docker, postgresql, redis, nginx, git, curl, vim

### Language Package Managers
- **Python**: pip, pip3, pipx, poetry
- **Node.js**: npm, yarn, pnpm, bun
- **Ruby**: gem, bundler
- **Rust**: cargo, rustup
- **Go**: go install, go get
- **PHP**: composer
- **Perl**: cpan
- **Java**: maven, gradle

### Development Tools
- **Version Managers**: nvm, pyenv, rbenv
- **CLI Tools**: gh, kubectl, terraform, ansible, aws-cli
- **IDEs**: VSCode extensions (`code --install-extension`)
- **Desktop Apps**: snap, flatpak, AppImage, Homebrew Cask

### Configuration & Setup
- **API Keys**: Environment variables, config files
- **Config Files**: JSON, TOML, YAML creation/editing
- **Environment**: PATH, .bashrc, .zshrc, .env files
- **SSH Keys**: Generation and configuration

### Deployment Targets
- **Local system** - Direct installation
- **VMs** - VirtualBox, VMware, Parallels (auto-detects)
- **Remote servers** - Via SSH
- **Jump hosts** - Multi-hop SSH configuration
- **Containers** - Docker, Kubernetes pods

### Specialized
- **GPU Drivers**: NVIDIA drivers + nvidia-docker
- **ML/Data Science**: PyTorch, TensorFlow, CUDA
- **Language Runtimes**: python, node, ruby, go, rust, java, php

## 🔧 How It Works

1. **Parse** - AI detects package type and target system
2. **Configure** - Sets up SSH/VMs if needed
3. **Checkpoint** - Creates VM snapshot or system backup
4. **Install** - Uses appropriate package manager
5. **Verify** - Runs functional tests (not just checks if binary exists)
6. **Report** - Confirms "READY TO USE" with versions

## 🛡️ Safety Features

- **VM snapshots** before any changes
- **System state backups** for remote servers
- **Checkpoint at every critical step**
- **Automatic rollback** on failures
- **Never leaves systems in broken state**
- **Logs all actions** for debugging

## ⚙️ Requirements

- **Any OS**: Linux, macOS, Windows/WSL
- **For VMs**: VirtualBox, VMware, or Parallels
- **For Remote**: SSH access (auto-configured if needed)
- **For GPU**: NVIDIA GPU (drivers auto-installed)

## 🔍 Troubleshooting

**AI asks me to run commands manually:**
- Emphasize: "Execute everything yourself. I cannot run commands."
- Add to prompt: "CRITICAL: Run all commands yourself via shell access"

**AI doesn't read SKILL.md:**
- Use: "Read the file SKILL.md then install XYZ"
- Or paste SKILL.md content directly into chat

**Skill doesn't activate in Claude Code:**
- Check it's in `~/.claude/skills/auto-installer/SKILL.md`
- Verify YAML frontmatter is valid
- Say explicitly: "use auto-installer skill to install XYZ"

**Installation logs:**
- Check: `/var/log/apt/`, `~/.npm/_logs/`, `~/.pip/`
- All bash commands are logged by Claude/AI tools
- Use `history` to review executed commands

## 📁 File Structure

```
auto-installer/
├── SKILL.md          # Complete installation logic (AI reads this)
├── README.md         # This file (for humans)
├── EXAMPLES.md       # Real-world usage examples
├── CONTRIBUTING.md   # How to contribute
├── CHANGELOG.md      # Version history
└── LICENSE           # MIT License
```

## 🚀 Advanced Usage

### Multi-Package Installations
```
install docker, node 20, postgresql, redis, and python testing tools
```

The AI will:
- Clarify ambiguities ("python testing tools" → pytest only? full suite?)
- Install everything in optimal dependency order
- Configure and start all services
- Verify each package works

### Production Deployments
```
install docker on prod-server.example.com
```

The AI will:
- Create system backups first
- Install packages with minimal downtime
- Start and verify services
- Keep backups until success confirmed

### GPU Servers
```
install docker with nvidia GPU support and pytorch with CUDA on gpu-server
```

The AI will:
- Detect GPU hardware
- Install NVIDIA drivers
- Reboot server and poll until back online
- Install nvidia-docker
- Install PyTorch with matching CUDA version
- Verify GPU access with test container

## 🤝 Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on:
- Reporting bugs
- Suggesting features
- Submitting improvements
- Testing new scenarios

## 📜 License

MIT License - Use freely with any AI tool

## 💭 Philosophy

**Core Principles:**
- **Correctness > Speed** - Do it right, not fast
- **Safety > Convenience** - Checkpoints before changes
- **Autonomy > User interaction** - ZERO manual commands
- **Transparency > Abstraction** - Clear logs and reports
- **Robustness > Simplicity** - Handle edge cases

**Design Goals:**
- ZERO manual commands for the user
- Only ask for: passwords, disambiguation, risky confirmations
- Always verify installations actually work
- Create safety checkpoints before changes
- Recover from errors automatically
- Never leave systems in broken state

---

**Made with ❤️ for the AI coding community**

**Supports**: Claude Code • Open Codex • Gemini CLI • Grok CLI • Continue.dev • Aider • and more
