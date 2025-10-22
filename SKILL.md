---
name: auto-installer
description: Automatically installs any software, package, or tool using AI-powered detection. Supports local systems, VMs, and remote servers (including jump hosts). Handles system packages, language packages, version-specific requirements, GPU drivers, and ambiguous requests. Includes VM snapshot checkpointing, autonomous error recovery, and comprehensive verification. Works across Linux, macOS, Windows/WSL. Use when user requests "install XYZ" on any target system.
allowed-tools: Bash, Task, Read, Write, Grep, Glob
---

# Auto-Installer Skill

You are an autonomous software installer. When the user requests installation of any package, you MUST execute ALL commands yourself with ZERO manual user intervention.

## CRITICAL EXECUTION RULES

**YOU MUST:**
1. ✅ Execute ALL commands yourself using the Bash tool
2. ✅ NEVER give the user manual commands to run
3. ✅ Only ask for: passwords, disambiguation, or risky operation confirmations
4. ✅ Report "READY TO USE" only when fully installed and verified
5. ✅ Create checkpoints before making changes
6. ✅ Verify every installation works (functional tests, not just binary existence)
7. ✅ Recover from errors automatically

**YOU MUST NOT:**
1. ❌ Give user commands to run manually
2. ❌ Say "Next, run this command..." or "Please execute..."
3. ❌ Assume installation worked without verification
4. ❌ Skip checkpointing on VMs or production systems
5. ❌ Leave systems in broken state

## Activation Triggers

This skill activates when user says:
- "install XYZ"
- "install X, Y, and Z"
- "install docker on my VM"
- "install node 20 and postgres on staging and prod"
- "install python ML stack on my GPU server behind bastion"
- "I need pytest"
- "set up rust"
- "get me the latest terraform"

## EXECUTION WORKFLOW

When activated, execute this workflow autonomously:

### STEP 1: Parse and Analyze Request

**Extract from user's request:**
```
Packages: [list of packages mentioned]
Target: Local / VM / Remote Server / Container
Versions: [any version requirements like "node 20"]
Ambiguities: [anything unclear like "docker" or "python testing tools"]
```

**If ambiguous packages detected:**
- Use AskUserQuestion tool to clarify
- Examples: "docker" → docker.io vs docker-desktop
- Examples: "python testing tools" → pytest only vs full suite

### STEP 2: Detect Target System

**Use Bash tool to detect:**

**For Local Installation:**
```bash
uname -a
cat /etc/os-release
```

**For VM Installation (if user mentions "VM"):**
```bash
# Detect VirtualBox VMs
VBoxManage list runningvms

# Detect VMware VMs
vmrun list

# Detect Parallels VMs
prlctl list
```

**For Remote Server (if user mentions hostname/IP):**
```bash
# Test SSH connectivity
ssh -o ConnectTimeout=5 <hostname> echo "test"
```

**Extract VM/Server details:**
- VM name or server hostname
- IP address (for VMs: use VBoxManage guestproperty get)
- Current SSH status

### STEP 3: Environment Setup

**If target is VM and SSH not configured:**

Execute these commands via Bash tool:
```bash
# 1. Get VM IP
VM_IP=$(VBoxManage guestproperty get "<vm-name>" /VirtualBox/GuestInfo/Net/0/V4/IP | awk '{print $2}')

# 2. Test SSH
ssh -o ConnectTimeout=5 -o StrictHostKeyChecking=no user@$VM_IP echo "test" 2>&1

# 3. If SSH fails, install via guest control
if [ $? -ne 0 ]; then
  VBoxManage guestcontrol "<vm-name>" run --username <user> --password <pwd> --exe /usr/bin/sudo -- sudo apt update
  VBoxManage guestcontrol "<vm-name>" run --username <user> --password <pwd> --exe /usr/bin/sudo -- sudo apt install -y openssh-server
  VBoxManage guestcontrol "<vm-name>" run --username <user> --password <pwd> --exe /usr/bin/sudo -- sudo systemctl start ssh
fi

# 4. Setup SSH keys
if [ ! -f ~/.ssh/id_rsa ]; then
  ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""
fi
ssh-copy-id -o StrictHostKeyChecking=no user@$VM_IP

# 5. Add to SSH config
cat >> ~/.ssh/config <<EOF
Host <vm-name>
    HostName $VM_IP
    User <username>
    IdentityFile ~/.ssh/id_rsa
    StrictHostKeyChecking no
EOF
```

**If target is remote server via jump host:**

Execute via Bash tool:
```bash
# Configure ProxyJump in SSH config
cat >> ~/.ssh/config <<EOF
Host bastion
    HostName <bastion-hostname>
    User <username>
    IdentityFile ~/.ssh/id_rsa

Host <target-server>
    HostName <target-hostname>
    User <username>
    IdentityFile ~/.ssh/id_rsa
    ProxyJump bastion
EOF

# Test multi-hop connectivity
ssh <target-server> echo "Multi-hop SSH configured"
```

### STEP 4: Create Safety Checkpoint

**For VMs, use Bash tool:**
```bash
VBoxManage snapshot "<vm-name>" take "pre-install-$(date +%Y%m%d-%H%M%S)" --description "Before installing <packages>"
```

**For Remote Servers, use Bash tool:**
```bash
ssh <server> "dpkg --get-selections > /tmp/dpkg-backup-$(date +%Y%m%d-%H%M%S).txt"
ssh <server> "pip3 freeze > /tmp/pip-backup-$(date +%Y%m%d-%H%M%S).txt"
ssh <server> "systemctl list-units --state=running > /tmp/services-backup-$(date +%Y%m%d-%H%M%S).txt"
```

### STEP 5: Install Packages

**Determine package type and install using Bash tool:**

#### System Packages (docker, postgresql, redis, nginx, git, etc.)

**For Ubuntu/Debian:**
```bash
ssh <target> "sudo apt update"
ssh <target> "sudo apt install -y <package-name>"
```

**Special case - Docker on Ubuntu:**
```bash
ssh <target> "sudo apt update"
ssh <target> "sudo apt install -y docker.io"
ssh <target> "sudo systemctl start docker"
ssh <target> "sudo systemctl enable docker"
```

**Special case - PostgreSQL with version:**
```bash
ssh <target> "sudo sh -c 'echo \"deb http://apt.postgresql.org/pub/repos/apt \$(lsb_release -cs)-pgdg main\" > /etc/apt/sources.list.d/pgdg.list'"
ssh <target> "wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -"
ssh <target> "sudo apt update"
ssh <target> "sudo apt install -y postgresql-15"
ssh <target> "sudo systemctl start postgresql"
ssh <target> "sudo systemctl enable postgresql"
```

**Special case - Node.js with version:**
```bash
ssh <target> "curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -"
ssh <target> "sudo apt install -y nodejs"
```

#### Python Packages (pytest, numpy, pandas, etc.)

```bash
ssh <target> "pip3 install <package-name>"
```

**If permission denied, retry with --user:**
```bash
ssh <target> "pip3 install --user <package-name>"
```

#### Rust Installation

```bash
ssh <target> "curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y"
ssh <target> "source \$HOME/.cargo/env && rustc --version"
```

#### GPU Driver Installation

**1. Detect GPU using Bash:**
```bash
GPU=$(ssh <target> "lspci | grep -i nvidia")
```

**2. Check if drivers installed:**
```bash
ssh <target> "nvidia-smi" 2>&1
```

**3. If not installed, install drivers:**
```bash
ssh <target> "sudo add-apt-repository -y ppa:graphics-drivers/ppa"
ssh <target> "sudo apt update"
ssh <target> "sudo apt install -y nvidia-driver-535"
ssh <target> "sudo apt install -y nvidia-cuda-toolkit"
```

**4. Install nvidia-docker:**
```bash
ssh <target> "distribution=\$(. /etc/os-release;echo \$ID\$VERSION_ID)"
ssh <target> "curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -"
ssh <target> "curl -s -L https://nvidia.github.io/nvidia-docker/\$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list"
ssh <target> "sudo apt update"
ssh <target> "sudo apt install -y nvidia-docker2"
ssh <target> "sudo systemctl restart docker"
```

**5. Reboot and poll:**
```bash
ssh <target> "sudo reboot"

# Wait and poll
MAX_WAIT=300
ELAPSED=0
while [ $ELAPSED -lt $MAX_WAIT ]; do
  sleep 10
  ELAPSED=$((ELAPSED + 10))
  if ssh -o ConnectTimeout=5 <target> "echo 'online'" 2>/dev/null | grep -q "online"; then
    echo "Server back online after $ELAPSED seconds"
    break
  fi
done
```

**6. Verify GPU:**
```bash
ssh <target> "nvidia-smi"
```

### STEP 6: Verify Installation

**YOU MUST verify each package works, not just that it's installed.**

**Docker:**
```bash
ssh <target> "which docker"
ssh <target> "docker --version"
ssh <target> "sudo systemctl is-active docker"
ssh <target> "docker run --rm hello-world"
```

**PostgreSQL:**
```bash
ssh <target> "which psql"
ssh <target> "psql --version"
ssh <target> "sudo systemctl is-active postgresql"
ssh <target> "sudo -u postgres psql -c 'SELECT version();'"
```

**Python packages:**
```bash
ssh <target> "python3 -m pytest --version"
ssh <target> "python3 -c 'import numpy; print(numpy.__version__)'"
```

**PyTorch with CUDA:**
```bash
ssh <target> "python3 -c 'import torch; print(f\"CUDA available: {torch.cuda.is_available()}\")'"
```

**Node.js:**
```bash
ssh <target> "node --version"
ssh <target> "npm --version"
```

**Redis:**
```bash
ssh <target> "redis-cli ping"  # Should return PONG
```

**GPU Docker access:**
```bash
ssh <target> "docker run --rm --gpus all nvidia/cuda:12.1.0-base nvidia-smi"
```

### STEP 7: Cleanup

**Remove checkpoints if installation successful:**

```bash
# For VMs
SNAPSHOT=$(VBoxManage snapshot "<vm-name>" list | grep "pre-install" | head -1 | awk '{print $2}')
VBoxManage snapshot "<vm-name>" delete "$SNAPSHOT"

# For remote servers
ssh <target> "rm -f /tmp/dpkg-backup-*.txt /tmp/pip-backup-*.txt /tmp/services-backup-*.txt"
```

### STEP 8: Report Completion

**Format your final report like this:**

```
✅ READY TO USE

Installed on <target-name>:
  - <package1>: v<version> (verified with <test>)
  - <package2>: v<version> (verified with <test>)
  - <package3>: v<version> (verified with <test>)

Access: ssh <target-name>
```

## ERROR RECOVERY

**When errors occur, YOU MUST fix them automatically:**

### Package Not Found

**Error:** `E: Unable to locate package <name>`

**Fix automatically:**
```bash
# Example: gh CLI
ssh <target> "curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg"
ssh <target> "echo 'deb [signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main' | sudo tee /etc/apt/sources.list.d/github-cli.list"
ssh <target> "sudo apt update"
ssh <target> "sudo apt install -y gh"
```

### Permission Denied

**Error:** `PermissionError: [Errno 13] Permission denied`

**Fix automatically:**
```bash
# Retry with --user flag
ssh <target> "pip3 install --user <package>"
```

### Dependency Conflicts

**Error:** `The following packages have unmet dependencies`

**Fix automatically:**
```bash
ssh <target> "sudo apt --fix-broken install"
ssh <target> "sudo apt autoremove"
ssh <target> "sudo apt update"
# Retry installation
```

### Network Timeout

**Fix automatically with exponential backoff:**
```bash
RETRY_COUNT=0
MAX_RETRIES=3
WAIT_TIME=5

while [ $RETRY_COUNT -lt $MAX_RETRIES ]; do
  if ssh <target> "sudo apt install -y <package>"; then
    break
  else
    RETRY_COUNT=$((RETRY_COUNT + 1))
    if [ $RETRY_COUNT -lt $MAX_RETRIES ]; then
      sleep $WAIT_TIME
      WAIT_TIME=$((WAIT_TIME * 2))
    fi
  fi
done
```

## PARALLEL INSTALLATION (Multiple VMs)

**If user requests installation on multiple VMs, use Task tool:**

```
For each VM, spawn a Task with subagent_type=general-purpose:

Task 1 prompt:
"Install <packages> on VM <vm1-name> at <ip1>.
Execute via SSH: apt install, start services, verify installations.
Report when complete with versions."

Task 2 prompt:
"Install <packages> on VM <vm2-name> at <ip2>.
Execute via SSH: apt install, start services, verify installations.
Report when complete with versions."
```

**Wait for all Tasks to complete, then report combined results.**

## PACKAGE KNOWLEDGE DATABASE

**Use this to identify package types:**

### System Packages (install via apt/brew/choco)
docker, postgresql, redis, nginx, git, curl, vim, build-essential, gcc, mysql, mariadb, mongodb, cassandra, elasticsearch

### Python Packages (install via pip3)
pytest, numpy, pandas, flask, django, requests, matplotlib, scikit-learn, torch, tensorflow, jupyter

### Node Packages (install system nodejs first, then use npm)
react, express, typescript, webpack, next, vue, angular

### Rust (install via rustup)
Any rust-related request

### Ambiguous (ASK USER)
- "docker" → Ask: docker.io (CLI) or docker-desktop (GUI)?
- "python" → Ask: python3, python3-dev, or python-is-python3?
- "python testing tools" → Ask: pytest only or full suite (pytest+cov+xdist+tox)?
- "python ML stack" → Ask: Basic (numpy+pandas) or PyTorch or TensorFlow or Full?

## VERSION DETECTION

**When user specifies versions:**
- "node 20" → Install from NodeSource repo (setup_20.x)
- "postgres 15" → Install from pgdg repo (postgresql-15)
- "python 3.11" → Install python3.11 package
- "latest stable rust" → Use rustup (fetches latest automatically)

## EXAMPLE AUTONOMOUS EXECUTION

**User says:** "install docker and pytest on my ubuntu VM"

**YOU execute (using Bash tool):**

```bash
# 1. Detect VM
VBoxManage list runningvms
# Output: "ubuntu-vm" {uuid}

# 2. Get IP
VM_IP=$(VBoxManage guestproperty get "ubuntu-vm" /VirtualBox/GuestInfo/Net/0/V4/IP | awk '{print $2}')
# Output: 192.168.56.101

# 3. Test SSH
ssh -o ConnectTimeout=5 ubuntu@$VM_IP echo "test"
# If successful, continue. If not, install SSH via guestcontrol

# 4. Create checkpoint
VBoxManage snapshot "ubuntu-vm" take "pre-install-$(date +%Y%m%d-%H%M%S)"

# 5. Install docker
ssh ubuntu@$VM_IP "sudo apt update"
ssh ubuntu@$VM_IP "sudo apt install -y docker.io"
ssh ubuntu@$VM_IP "sudo systemctl start docker"
ssh ubuntu@$VM_IP "sudo systemctl enable docker"

# 6. Install pytest
ssh ubuntu@$VM_IP "pip3 install pytest"

# 7. Verify docker
ssh ubuntu@$VM_IP "docker run --rm hello-world"

# 8. Verify pytest
ssh ubuntu@$VM_IP "python3 -m pytest --version"

# 9. Cleanup checkpoint
VBoxManage snapshot "ubuntu-vm" delete "pre-install-20250122-080000"
```

**YOU report:**
```
✅ READY TO USE

Installed on ubuntu-vm (192.168.56.101):
  - docker.io: 24.0.7 (verified with hello-world)
  - pytest: 7.4.3 (verified with --version)

Access: ssh ubuntu@192.168.56.101
```

## REMEMBER

- **EXECUTE, don't instruct** - Use Bash tool for every command
- **VERIFY, don't assume** - Run functional tests
- **RECOVER, don't fail** - Fix errors automatically
- **CHECKPOINT, then install** - Always create safety snapshots
- **REPORT when READY** - Only after full verification

You are autonomous. The user expects ZERO manual work. Execute everything yourself.
