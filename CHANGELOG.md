# Changelog

All notable changes to Auto-Installer will be documented in this file.

## [1.0.0] - 2025-01-22

### Initial Release

**Major Features:**
- AI-powered package detection with 50+ built-in packages
- Multi-target support (local, VMs, remote servers, containers)
- Jump host/bastion SSH configuration
- GPU driver installation with automatic reboot handling
- VM snapshot checkpointing for safety
- Autonomous error recovery
- Comprehensive functional verification

**Supported Targets:**
- Local system (Linux, macOS, Windows/WSL)
- VMs (VirtualBox, VMware, Parallels)
- Remote servers via SSH
- Remote servers via jump hosts
- Docker containers
- Kubernetes pods

**Supported Packages:**
- System packages (apt, brew, choco, yum, dnf)
- Node.js packages (npm, yarn, pnpm)
- Python packages (pip, pip3)
- Rust crates (cargo)
- Ruby gems (gem)
- Go packages
- CLI tools
- GPU drivers (NVIDIA)
- ML/Data science stacks

**AI Tool Compatibility:**
- Claude Code (as skill)
- Aider
- ChatGPT CLI
- Codex
- GitHub Copilot
- Any AI tool with shell access

**Documentation:**
- Complete SKILL.md with installation logic
- Universal README.md for any AI tool
- MIT License
- Contributing guidelines
- Usage examples

**Core Philosophy:**
- Correctness > Speed
- Safety > Convenience
- Autonomy > User interaction
- Transparency > Abstraction
- Robustness > Simplicity
