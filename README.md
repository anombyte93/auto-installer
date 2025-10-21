# Auto-Installer 🤖

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![AI Tool Compatible](https://img.shields.io/badge/AI-Compatible-brightgreen.svg)](https://github.com/anombyte93/auto-installer)
[![Claude Code](https://img.shields.io/badge/Claude-Code-blue.svg)](https://claude.ai/code)

**Autonomous AI agent that installs any software package on any system with zero manual intervention.**

Works with Claude Code, ChatGPT, Codex, Aider, or any AI tool with shell access.

## Quick Start

### 1. Clone this repository

```bash
git clone https://github.com/anombyte93/auto-installer.git
cd auto-installer
```

### 2. Use with your AI tool

#### Option A: Claude Code (Skill)

```bash
# Copy to Claude Code skills directory
cp -r . ~/.claude/skills/auto-installer/

# The skill auto-activates when you say:
# "install docker"
# "install pytest on my ubuntu VM"
```

#### Option B: Any AI CLI Tool (Aider, ChatGPT CLI, Codex, etc.)

```bash
# Start your AI tool in this directory
aider

# Or with context flag
chatgpt --context SKILL.md

# Then tell the AI:
# "Read SKILL.md and install docker for me"
```

#### Option C: Direct Prompt

Copy-paste this to any AI with shell access:

```
You are an autonomous software installer. Read the file SKILL.md in the current directory to understand your capabilities and workflow.

CRITICAL RULES:
1. Execute ALL commands yourself - NEVER give me manual instructions
2. Only ask for: passwords, disambiguation, or risky operation confirmations
3. Report "READY TO USE" only when fully installed and verified

Now install: [WHAT YOU WANT TO INSTALL]
```

## What It Does

✅ **Auto-detects** what to install (docker, pytest, node 20, etc.)
✅ **Auto-configures** VMs (SSH, mount points, snapshots)
✅ **Handles jump hosts** for remote servers
✅ **Installs GPU drivers** with automatic reboot
✅ **Creates checkpoints** for safety
✅ **Recovers from errors** automatically
✅ **Verifies everything** actually works

## Examples

### Simple
```
install docker
```

### With Version
```
install node 20 and postgres 15
```

### On a VM
```
install pytest on my ubuntu VM
```

### Multiple VMs
```
install docker and redis on staging and production VMs
```

### Remote Server via Jump Host
```
install rust on gpu-server.company.com via bastion.company.com
```

### GPU Server with ML Stack
```
install docker with GPU support and pytorch on my GPU server
```

## Supported Targets

- **Local system** - Direct installation
- **VMs** - Auto-detects VirtualBox/VMware/Parallels
- **Remote servers** - Via SSH
- **Jump hosts** - Configures multi-hop SSH automatically
- **Containers** - Docker/Kubernetes pods

## Supported Packages

- **System packages**: docker, postgresql, redis, nginx, git, curl, vim, etc.
- **Language runtimes**: python, node, ruby, go, rust, java, php
- **Language packages**: npm, pip, cargo, gem packages
- **CLI tools**: gh, kubectl, terraform, ansible, aws-cli
- **GPU drivers**: NVIDIA drivers + nvidia-docker
- **ML/Data Science**: pytorch, tensorflow, numpy, pandas, jupyter

## How It Works

1. **Detects** package type and target system
2. **Configures** SSH/VMs if needed
3. **Creates checkpoint** (VM snapshot or backup)
4. **Installs** packages with appropriate package manager
5. **Verifies** everything works (runs functional tests)
6. **Reports** completion

## Safety Features

- VM snapshots before changes
- System state backups
- Checkpoint at every critical step
- Can rollback on failures
- Never leaves system in broken state

## Requirements

- **For VMs**: VirtualBox, VMware, or Parallels installed
- **For Remote**: SSH access (will configure keys automatically)
- **For GPU**: NVIDIA GPU (will detect and install drivers)

## AI Tool Compatibility

### Claude Code
Install as a skill in `~/.claude/skills/auto-installer/`

### Aider
```bash
cd auto-installer
aider
# Say: "Read SKILL.md and install docker"
```

### ChatGPT CLI / GPT-Engineer
```bash
chatgpt --context SKILL.md
# Say: "install pytest"
```

### Codex / GitHub Copilot
Paste the prompt from "Option C" above

### Custom AI Tools
Any AI with shell access can use this. Just ensure it reads SKILL.md first.

## File Structure

```
auto-installer/
├── SKILL.md          # Complete installation logic and capabilities
├── README.md         # This file
├── EXAMPLES.md       # Real-world usage examples
├── CONTRIBUTING.md   # How to contribute
├── CHANGELOG.md      # Version history
└── LICENSE           # MIT License
```

## Advanced Usage

### For Multi-Package Installations
```
install docker, node 20, postgresql, redis, and python testing tools
```

The AI will:
- Clarify "python testing tools" (pytest? full suite?)
- Install everything in optimal order
- Configure all services
- Verify each package

### For Production Servers
```
install docker on prod-server.example.com
```

The AI will:
- Create system backups first
- Install packages
- Start services
- Run verification tests
- Keep backups until success confirmed

### For GPU Servers
```
install docker with nvidia GPU support and pytorch with CUDA on gpu-server
```

The AI will:
- Detect GPU hardware
- Install NVIDIA drivers
- Reboot if needed
- Poll until server returns
- Install nvidia-docker
- Install PyTorch with correct CUDA version
- Verify GPU access works

## Troubleshooting

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

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on:
- Reporting bugs
- Suggesting features
- Submitting improvements
- Testing new scenarios

## License

MIT License - Use freely with any AI tool

## Philosophy

**Core Principles:**
- Correctness > Speed
- Safety > Convenience
- Autonomy > User interaction
- Transparency > Abstraction
- Robustness > Simplicity

**Design Goals:**
- ZERO manual commands for the user
- Only ask for passwords, disambiguation, or risky operation confirmations
- Always verify installations actually work
- Create safety checkpoints before changes
- Recover from errors automatically
- Never leave systems in broken state
