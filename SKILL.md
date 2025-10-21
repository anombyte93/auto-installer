---
name: auto-installer
description: Automatically installs any software, package, or tool using AI-powered detection. Supports local systems, VMs, and remote servers (including jump hosts). Handles system packages, language packages, version-specific requirements, GPU drivers, and ambiguous requests. Includes VM snapshot checkpointing, autonomous error recovery, and comprehensive verification. Works across Linux, macOS, Windows/WSL. Use when user requests "install XYZ" on any target system.
allowed-tools: Bash, Task, Read, Write, Grep, Glob
---

# Auto-Installer Skill

Intelligently installs any software package with complete autonomy, safety checkpoints, and comprehensive verification.

## Activation

Triggers on installation requests:
- "install XYZ"
- "install X, Y, and Z"
- "install docker on my VM"
- "install node 20 and postgres on staging and prod"
- "install python ML stack on my GPU server behind bastion"
- "I need pytest"
- "set up rust"
- "get me the latest terraform"

## Core Capabilities

### 1. AI-Powered Package Detection

**Built-in Knowledge Database:**
- System packages: docker, postgresql, redis, nginx, git, curl, vim, etc.
- Language runtimes: python, node/nodejs, ruby, go, rust, java, php
- Language packages: npm/yarn/pnpm, pip/pip3, cargo, gem, go packages
- CLI tools: gh, kubectl, terraform, ansible, aws-cli, gcloud
- Development tools: build-essential, gcc, make, cmake, pkg-config
- Databases: mysql, mariadb, mongodb, cassandra, elasticsearch
- ML/Data Science: pytorch, tensorflow, numpy, pandas, scikit-learn, jupyter

**Version Detection:**
- "node 20" → Node.js v20.x (fetches from NodeSource)
- "python 3.11" → Python 3.11.x
- "latest stable rust" → Fetches from rust-lang.org
- "postgres 15" → PostgreSQL 15.x

**Ambiguity Resolution:**
- "docker" → docker.io vs docker-desktop
- "python" → python3 vs python-is-python3 vs python3-dev
- "python testing tools" → pytest vs full suite (pytest+cov+xdist+tox)
- "python ML stack" → Basic vs PyTorch vs TensorFlow vs Full

### 2. Multi-Target Installation

**Local System:**
Direct installation on current machine

**Single VM:**
Detects VirtualBox/VMware/Parallels VMs, sets up SSH + mount points automatically

**Multiple VMs (Parallel):**
Installs on multiple VMs simultaneously

**Remote Server:**
Direct SSH to remote server

**Remote Server via Jump Host:**
Configures ProxyJump automatically for bastion/jump host access

**Containers:**
Detects running containers and installs inside them

### 3. Safety & Checkpointing

**VM Snapshots:** Creates snapshots before installation, can rollback on failure

**System State Backups:** Saves dpkg/pip/systemd state for remote servers

**Rollback Capability:** Can restore from checkpoints if installation fails

### 4. Advanced Features

**GPU Support:**
- Detects NVIDIA GPUs
- Installs appropriate drivers
- Configures NVIDIA Container Toolkit (nvidia-docker2)
- Handles system reboot requirements
- Polls for server to come back online
- Verifies CUDA availability post-reboot

**Multi-Hop SSH:**
- Configures ~/.ssh/config with ProxyJump
- Creates convenient aliases
- Handles passwordless SSH key setup

**Version-Specific Installation:**
- Fetches latest stable versions from official sources
- Adds third-party repositories when needed

**Ambiguous Package Clarification:**
- Presents options to user with context
- Recommends based on environment

## Installation Workflow

### Phase 1: Detection & Analysis

Parse user request, extract packages, detect target type (Local/VM/Remote/Container), identify package types

### Phase 2: Environment Setup

**For VMs:**
1. Scan for running VMs
2. Test SSH connectivity
3. Install SSH if needed (via VBoxManage guestcontrol)
4. Configure passwordless SSH keys
5. Set up shared mount point
6. Create SSH config alias

**For Remote Servers:**
1. Test direct SSH connectivity
2. Configure jump host if needed
3. Test multi-hop connectivity

### Phase 3: Checkpoint Creation

Create VM snapshots or system state backups before any changes

### Phase 4: Installation Execution

Execute appropriate package manager commands based on OS and package type

**System Packages:**
- Linux: apt/yum/dnf/pacman
- macOS: brew
- Windows: choco/winget

**Language Packages:**
- Node: npm/yarn/pnpm
- Python: pip3
- Rust: cargo
- Ruby: gem
- Go: go install

**Special Cases:**
- GPU drivers with reboot handling
- Version-specific repositories (NodeSource, etc.)
- Latest version fetching (Rust from rust-lang.org)

### Phase 5: Verification

Test each package installation:
- Check binary exists
- Check version
- Test service is running (if applicable)
- Run functional tests (docker hello-world, pytest --version, etc.)
- Verify GPU access (for CUDA installations)

### Phase 6: Cleanup

Remove checkpoints and backups if installation successful

## Error Recovery

**Autonomous troubleshooting for:**
- Package not found → Add repository
- Permission denied → Retry with sudo/--user
- Dependency conflicts → Fix broken packages
- Network timeout → Exponential backoff retry
- GPU driver reboot → Automatic reboot + polling

## Platform-Specific Handling

### Linux (Ubuntu/Debian)
- apt package manager
- pip3 for Python
- Handles Ubuntu-specific naming (docker → docker.io)

### macOS
- brew for system packages
- brew --cask for GUI apps

### Windows (WSL)
- Inside WSL: Same as Linux
- Native: choco/winget via PowerShell remoting

### Containers
- docker exec for Docker containers
- kubectl exec for Kubernetes pods

## Multi-Package Optimization

**Parallel Installation:** Different package managers run in parallel via Task tool agents

**Sequential Installation:** Same package manager installs sequentially to avoid cache conflicts

## Design Philosophy

**Priorities:**
1. Correctness > Speed
2. Safety > Convenience
3. Autonomy > User interaction
4. Transparency > Abstraction
5. Robustness > Simplicity

**User Experience:**
- ZERO manual commands
- Only ask for: disambiguation, passwords, risky operation confirmation
- Always report "READY TO USE" only when truly ready
- Provide convenient access methods

**Safety:**
- VM snapshots for virtual machines
- State backups for remote servers
- Checkpoint at every critical step
- Can rollback on failures
- Never leave systems in broken state
