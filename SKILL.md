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
Installs on multiple VMs simultaneously using Task tool

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

**1. Parse user request:**
```bash
# Extract packages, target, and version requirements
# Example: "install docker and pytest on my ubuntu VM"
# → packages: ["docker", "pytest"]
# → target: VM (ubuntu)
# → versions: default
```

**2. Detect target type:**
```bash
# Local system
uname -a
cat /etc/os-release

# VMs (VirtualBox)
VBoxManage list runningvms
VBoxManage guestproperty get <vm-name> /VirtualBox/GuestInfo/Net/0/V4/IP

# VMs (VMware)
vmrun list

# VMs (Parallels)
prlctl list

# Remote servers - check if hostname/IP mentioned in request
```

**3. Identify package types:**
- System: docker, postgresql, redis, nginx
- Python: pytest, numpy, pandas, pytorch
- Node: express, react, next
- Rust: via cargo/rustup
- CLI: gh, kubectl, terraform

### Phase 2: Environment Setup

#### For VMs:

**1. Scan for running VMs:**
```bash
VBoxManage list runningvms
# Output: "ubuntu-vm" {12345678-1234-1234-1234-123456789abc}
```

**2. Get VM IP:**
```bash
VBoxManage guestproperty get "ubuntu-vm" /VirtualBox/GuestInfo/Net/0/V4/IP
# Output: Value: 192.168.56.101
```

**3. Test SSH connectivity:**
```bash
ssh -o ConnectTimeout=5 -o StrictHostKeyChecking=no user@192.168.56.101 echo "test"
```

**4. If SSH not available, install via guest control:**
```bash
# First, test if we can execute commands
VBoxManage guestcontrol "ubuntu-vm" run --exe /usr/bin/whoami --username <user> --password <pwd>

# Install openssh-server
VBoxManage guestcontrol "ubuntu-vm" run --exe /usr/bin/sudo --username <user> --password <pwd> -- sudo apt update
VBoxManage guestcontrol "ubuntu-vm" run --exe /usr/bin/sudo --username <user> --password <pwd> -- sudo apt install -y openssh-server

# Start SSH service
VBoxManage guestcontrol "ubuntu-vm" run --exe /usr/bin/sudo --username <user> --password <pwd> -- sudo systemctl start ssh
VBoxManage guestcontrol "ubuntu-vm" run --exe /usr/bin/sudo --username <user> --password <pwd> -- sudo systemctl enable ssh
```

**5. Configure passwordless SSH:**
```bash
# Generate key if not exists
if [ ! -f ~/.ssh/id_rsa ]; then
  ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""
fi

# Copy to VM
ssh-copy-id -o StrictHostKeyChecking=no user@192.168.56.101

# Test passwordless
ssh user@192.168.56.101 echo "SSH configured successfully"
```

**6. Add to SSH config:**
```bash
cat >> ~/.ssh/config <<EOF
Host ubuntu-vm
    HostName 192.168.56.101
    User <username>
    IdentityFile ~/.ssh/id_rsa
    StrictHostKeyChecking no
EOF
```

**7. Set up shared mount point (optional):**
```bash
# Add shared folder
VBoxManage sharedfolder add "ubuntu-vm" --name "shared" --hostpath "/path/to/host/folder" --automount

# Mount on VM
ssh ubuntu-vm "sudo mkdir -p /mnt/shared && sudo mount -t vboxsf shared /mnt/shared"
```

#### For Remote Servers:

**1. Test direct SSH:**
```bash
ssh -o ConnectTimeout=5 server.example.com echo "test"
```

**2. If jump host mentioned, configure ProxyJump:**
```bash
cat >> ~/.ssh/config <<EOF
Host bastion
    HostName bastion.mycompany.local
    User <username>
    IdentityFile ~/.ssh/id_rsa

Host gpu-server
    HostName gpu-server.mycompany.local
    User <username>
    IdentityFile ~/.ssh/id_rsa
    ProxyJump bastion
EOF
```

**3. Test multi-hop connectivity:**
```bash
ssh gpu-server echo "Multi-hop SSH configured successfully"
```

### Phase 3: Checkpoint Creation

#### For VMs:

**Create VM snapshot:**
```bash
VBoxManage snapshot "ubuntu-vm" take "pre-install-$(date +%Y%m%d-%H%M%S)" --description "Before installing docker and pytest"
```

**Verify snapshot:**
```bash
VBoxManage snapshot "ubuntu-vm" list
```

#### For Remote Servers:

**Create system state backup:**
```bash
# Backup package state
ssh gpu-server "dpkg --get-selections > /tmp/dpkg-backup-$(date +%Y%m%d-%H%M%S).txt"

# Backup pip packages
ssh gpu-server "pip3 freeze > /tmp/pip-backup-$(date +%Y%m%d-%H%M%S).txt"

# Backup service status
ssh gpu-server "systemctl list-units --state=running > /tmp/services-backup-$(date +%Y%m%d-%H%M%S).txt"
```

### Phase 4: Installation Execution

#### System Packages (apt/yum/brew):

**Docker on Ubuntu/Debian:**
```bash
ssh ubuntu-vm "sudo apt update"
ssh ubuntu-vm "sudo apt install -y docker.io"
ssh ubuntu-vm "sudo systemctl start docker"
ssh ubuntu-vm "sudo systemctl enable docker"
ssh ubuntu-vm "sudo usermod -aG docker $USER"
```

**PostgreSQL with version:**
```bash
# PostgreSQL 15 on Ubuntu
ssh ubuntu-vm "sudo sh -c 'echo \"deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main\" > /etc/apt/sources.list.d/pgdg.list'"
ssh ubuntu-vm "wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -"
ssh ubuntu-vm "sudo apt update"
ssh ubuntu-vm "sudo apt install -y postgresql-15"
ssh ubuntu-vm "sudo systemctl start postgresql"
ssh ubuntu-vm "sudo systemctl enable postgresql"
```

**Node.js with version:**
```bash
# Node.js 20 via NodeSource
ssh ubuntu-vm "curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -"
ssh ubuntu-vm "sudo apt install -y nodejs"
```

**Redis:**
```bash
ssh ubuntu-vm "sudo apt update"
ssh ubuntu-vm "sudo apt install -y redis-server"
ssh ubuntu-vm "sudo systemctl start redis-server"
ssh ubuntu-vm "sudo systemctl enable redis-server"
```

#### Python Packages:

**Basic pip install:**
```bash
ssh ubuntu-vm "pip3 install pytest"
```

**With --user flag (if permission denied):**
```bash
ssh ubuntu-vm "pip3 install --user pytest"
```

**Python testing tools (after clarification):**
```bash
# If user selects "pytest + pytest-cov + pytest-xdist + tox"
ssh ubuntu-vm "pip3 install pytest pytest-cov pytest-xdist tox"
```

**PyTorch with CUDA:**
```bash
# Detect CUDA version first
CUDA_VERSION=$(ssh gpu-server "nvidia-smi | grep 'CUDA Version' | awk '{print \$9}' | cut -d. -f1,2")

# Install PyTorch with matching CUDA version
if [ "$CUDA_VERSION" = "12.1" ]; then
  ssh gpu-server "pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121"
elif [ "$CUDA_VERSION" = "11.8" ]; then
  ssh gpu-server "pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118"
fi
```

#### Latest Version Fetching:

**Rust (latest stable):**
```bash
# Install via rustup
ssh ubuntu-vm "curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y"
ssh ubuntu-vm "source \$HOME/.cargo/env && rustc --version"
```

#### GPU Driver Installation:

**1. Detect GPU:**
```bash
GPU_DETECTED=$(ssh gpu-server "lspci | grep -i nvidia")
if [ -n "$GPU_DETECTED" ]; then
  echo "NVIDIA GPU detected: $GPU_DETECTED"
fi
```

**2. Check if drivers installed:**
```bash
DRIVER_INSTALLED=$(ssh gpu-server "nvidia-smi" 2>&1)
if echo "$DRIVER_INSTALLED" | grep -q "NVIDIA-SMI"; then
  echo "NVIDIA drivers already installed"
else
  echo "NVIDIA drivers not found, installing..."
fi
```

**3. Install NVIDIA drivers:**
```bash
# Add NVIDIA repository
ssh gpu-server "sudo add-apt-repository -y ppa:graphics-drivers/ppa"
ssh gpu-server "sudo apt update"

# Install driver (version depends on GPU)
ssh gpu-server "sudo apt install -y nvidia-driver-535"

# Install CUDA toolkit
ssh gpu-server "sudo apt install -y nvidia-cuda-toolkit"
```

**4. Install NVIDIA Container Toolkit:**
```bash
ssh gpu-server "distribution=\$(. /etc/os-release;echo \$ID\$VERSION_ID)"
ssh gpu-server "curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -"
ssh gpu-server "curl -s -L https://nvidia.github.io/nvidia-docker/\$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list"
ssh gpu-server "sudo apt update"
ssh gpu-server "sudo apt install -y nvidia-docker2"
```

**5. Configure Docker for GPU:**
```bash
ssh gpu-server "sudo systemctl restart docker"
```

**6. Reboot if needed:**
```bash
echo "GPU drivers require reboot. Rebooting server..."
ssh gpu-server "sudo reboot"
```

**7. Poll for server to come back:**
```bash
echo "Waiting for server to come back online..."
MAX_WAIT=300  # 5 minutes
ELAPSED=0
while [ $ELAPSED -lt $MAX_WAIT ]; do
  sleep 10
  ELAPSED=$((ELAPSED + 10))
  if ssh -o ConnectTimeout=5 gpu-server "echo 'online'" 2>/dev/null | grep -q "online"; then
    echo "Server is back online after $ELAPSED seconds"
    break
  fi
  echo "Still waiting... ($ELAPSED seconds elapsed)"
done

if [ $ELAPSED -ge $MAX_WAIT ]; then
  echo "ERROR: Server did not come back online within $MAX_WAIT seconds"
  exit 1
fi
```

**8. Verify GPU post-reboot:**
```bash
ssh gpu-server "nvidia-smi"
```

#### Jupyter Setup (for remote access):

```bash
# Install Jupyter
ssh gpu-server "pip3 install jupyter"

# Configure for remote access
ssh gpu-server "jupyter notebook --generate-config"
ssh gpu-server "echo \"c.NotebookApp.ip = '0.0.0.0'\" >> ~/.jupyter/jupyter_notebook_config.py"
ssh gpu-server "echo \"c.NotebookApp.open_browser = False\" >> ~/.jupyter/jupyter_notebook_config.py"
ssh gpu-server "echo \"c.NotebookApp.port = 8888\" >> ~/.jupyter/jupyter_notebook_config.py"

echo "Jupyter configured. Access via: ssh -L 8888:localhost:8888 gpu-server"
```

#### Parallel Multi-VM Installation:

```bash
# Use Task tool to spawn parallel agents
# Agent 1: staging-vm installation
# Agent 2: prod-vm installation
```

Example Task tool usage:
```
Task tool with subagent_type=general-purpose
prompt: "Install docker, node 20, postgresql, redis on staging-vm (192.168.56.101).
Execute these commands via SSH:
1. sudo apt update
2. Install Node.js 20 repository
3. Install all packages
4. Start services
5. Verify installations
Report when complete."
```

### Phase 5: Verification

**Docker:**
```bash
# Check docker installed
ssh ubuntu-vm "which docker"

# Check docker version
ssh ubuntu-vm "docker --version"

# Check docker service running
ssh ubuntu-vm "sudo systemctl is-active docker"

# Functional test
ssh ubuntu-vm "docker run hello-world"
```

**PostgreSQL:**
```bash
# Check installed
ssh ubuntu-vm "which psql"

# Check version
ssh ubuntu-vm "psql --version"

# Check service running
ssh ubuntu-vm "sudo systemctl is-active postgresql"

# Functional test
ssh ubuntu-vm "sudo -u postgres psql -c 'SELECT version();'"
```

**Python packages:**
```bash
# pytest
ssh ubuntu-vm "python3 -m pytest --version"

# PyTorch with CUDA
ssh gpu-server "python3 -c 'import torch; print(torch.cuda.is_available())'"
# Should output: True
```

**GPU Docker access:**
```bash
ssh gpu-server "docker run --gpus all nvidia/cuda:12.1.0-base nvidia-smi"
```

**Node.js:**
```bash
ssh ubuntu-vm "node --version"
ssh ubuntu-vm "npm --version"
```

**Rust:**
```bash
ssh ubuntu-vm "rustc --version"
ssh ubuntu-vm "cargo --version"
```

**Redis:**
```bash
ssh ubuntu-vm "redis-cli ping"
# Should output: PONG
```

### Phase 6: Cleanup

**Remove VM snapshots if successful:**
```bash
SNAPSHOT_NAME=$(VBoxManage snapshot "ubuntu-vm" list | grep "pre-install" | head -1 | awk '{print $2}')
VBoxManage snapshot "ubuntu-vm" delete "$SNAPSHOT_NAME"
```

**Remove system backups if successful:**
```bash
ssh ubuntu-vm "rm -f /tmp/dpkg-backup-*.txt /tmp/pip-backup-*.txt /tmp/services-backup-*.txt"
```

## Error Recovery

### Package Not Found → Add Repository

**Example: GitHub CLI (gh)**

Error:
```
E: Unable to locate package gh
```

Autonomous fix:
```bash
# Add GitHub CLI repository
ssh ubuntu-vm "curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg"
ssh ubuntu-vm "echo \"deb [arch=\$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main\" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null"
ssh ubuntu-vm "sudo apt update"

# Retry installation
ssh ubuntu-vm "sudo apt install -y gh"
```

### Permission Denied → Retry with --user

Error:
```
PermissionError: [Errno 13] Permission denied
```

Autonomous fix:
```bash
# Retry pip install with --user flag
ssh ubuntu-vm "pip3 install --user pytest"
```

### Dependency Conflicts → Fix Broken Packages

Error:
```
The following packages have unmet dependencies
```

Autonomous fix:
```bash
ssh ubuntu-vm "sudo apt --fix-broken install"
ssh ubuntu-vm "sudo apt autoremove"
ssh ubuntu-vm "sudo apt update"
# Retry original installation
```

### Network Timeout → Exponential Backoff Retry

```bash
RETRY_COUNT=0
MAX_RETRIES=3
WAIT_TIME=5

while [ $RETRY_COUNT -lt $MAX_RETRIES ]; do
  if ssh ubuntu-vm "sudo apt install -y docker.io"; then
    echo "Installation successful"
    break
  else
    RETRY_COUNT=$((RETRY_COUNT + 1))
    if [ $RETRY_COUNT -lt $MAX_RETRIES ]; then
      echo "Installation failed, retrying in $WAIT_TIME seconds... (Attempt $RETRY_COUNT/$MAX_RETRIES)"
      sleep $WAIT_TIME
      WAIT_TIME=$((WAIT_TIME * 2))  # Exponential backoff
    else
      echo "ERROR: Installation failed after $MAX_RETRIES attempts"
      exit 1
    fi
  fi
done
```

## Platform-Specific Handling

### Linux (Ubuntu/Debian)

**Package manager:**
```bash
sudo apt update
sudo apt install -y <package>
```

**Docker naming:**
```bash
# Ubuntu uses "docker.io" not "docker"
sudo apt install -y docker.io
```

**Python:**
```bash
pip3 install <package>
```

### macOS

**System packages:**
```bash
brew install <package>
```

**GUI applications:**
```bash
brew install --cask <app>
```

### Windows (WSL)

**Inside WSL:** Same as Linux

**Native Windows:**
```powershell
# Via Chocolatey
choco install <package> -y

# Via winget
winget install <package>
```

### Containers

**Docker containers:**
```bash
docker exec <container-name> apt update
docker exec <container-name> apt install -y <package>
```

**Kubernetes pods:**
```bash
kubectl exec <pod-name> -- apt update
kubectl exec <pod-name> -- apt install -y <package>
```

## Multi-Package Optimization

**Parallel Installation (different managers):**

Use Task tool to spawn agents:
- Agent 1: System packages (apt)
- Agent 2: Python packages (pip3)
- Agent 3: Node packages (npm)

**Sequential Installation (same manager):**

```bash
# Single apt command for multiple packages
ssh ubuntu-vm "sudo apt install -y docker.io postgresql redis-server"

# Sequential pip for dependencies
ssh ubuntu-vm "pip3 install numpy pandas scikit-learn"
```

## Design Philosophy

**Priorities:**
1. **Correctness** > Speed: Always verify installations work
2. **Safety** > Convenience: Always checkpoint before changes
3. **Autonomy** > User interaction: Execute ALL commands, never give manual instructions
4. **Transparency** > Abstraction: Log every command executed
5. **Robustness** > Simplicity: Handle all edge cases

**User Experience:**
- **ZERO manual commands** - Execute everything autonomously
- **Only ask for:** Passwords, disambiguation, risky operation confirmation
- **Always report "READY TO USE"** only when truly verified and working
- **Provide convenient access methods** (SSH aliases, port forwarding commands)

**Safety:**
- VM snapshots for virtual machines (VBoxManage snapshot)
- State backups for remote servers (dpkg, pip, services)
- Checkpoint at every critical step
- Can rollback on failures
- Never leave systems in broken state

## Critical Rules

1. **NEVER provide manual commands to user** - Execute all commands yourself via Bash tool
2. **ALWAYS verify installations** - Don't assume success, test functionality
3. **ALWAYS create checkpoints** before making changes (VM snapshots or system backups)
4. **ALWAYS handle errors autonomously** - Add repos, retry with --user, fix dependencies
5. **ONLY report "READY TO USE"** when fully installed and verified
6. **ASK for clarification** only when genuinely ambiguous (docker vs docker-desktop, basic vs full ML stack)
7. **USE Task tool for parallel operations** (multi-VM installations)
8. **HANDLE reboots automatically** (GPU drivers) with polling mechanism
9. **LOG every command** for transparency
10. **PROVIDE convenient access** (SSH config aliases, port forwarding commands)

## Example Execution Flow

**User:** "install docker and pytest on my ubuntu VM"

**Autonomous Execution:**

1. ✓ Detect VM: VBoxManage list runningvms → "ubuntu-vm"
2. ✓ Get IP: 192.168.56.101
3. ✓ Test SSH: Success
4. ✓ Create checkpoint: VBoxManage snapshot take "pre-install-20250122-034500"
5. ✓ Install docker.io: ssh ubuntu-vm "sudo apt update && sudo apt install -y docker.io"
6. ✓ Start docker: ssh ubuntu-vm "sudo systemctl start docker"
7. ✓ Install pytest: ssh ubuntu-vm "pip3 install pytest"
8. ✓ Verify docker: ssh ubuntu-vm "docker run hello-world" → Success
9. ✓ Verify pytest: ssh ubuntu-vm "python3 -m pytest --version" → pytest 7.4.3
10. ✓ Cleanup: VBoxManage snapshot delete "pre-install-20250122-034500"
11. ✓ Report: "✅ READY TO USE - Docker 24.0.7 and pytest 7.4.3 installed on ubuntu-vm"

**NO manual steps for user. ZERO commands to run. Fully autonomous.**
