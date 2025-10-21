# Usage Examples

Real-world examples of using Auto-Installer with different AI tools.

## Table of Contents
- [Simple Examples](#simple-examples)
- [VM Examples](#vm-examples)
- [Remote Server Examples](#remote-server-examples)
- [GPU Server Examples](#gpu-server-examples)
- [Production Examples](#production-examples)
- [AI Tool Specific](#ai-tool-specific)

---

## Simple Examples

### Install Single Package (Local)

**Request:**
```
install docker
```

**What happens:**
1. Detects OS (Ubuntu/macOS/Windows)
2. Installs docker via appropriate manager (apt/brew/choco)
3. Starts docker service
4. Verifies with `docker run hello-world`
5. Reports ready

---

### Install Multiple Packages

**Request:**
```
install git, curl, and jq
```

**What happens:**
1. Detects all as system packages
2. Installs via apt/brew/choco
3. Verifies each binary exists
4. Reports versions

---

### Install with Version Requirement

**Request:**
```
install node 20 and postgres 15
```

**What happens:**
1. Adds NodeSource repository for Node.js 20
2. Installs nodejs 20.x
3. Installs PostgreSQL 15
4. Starts postgresql service
5. Verifies both versions
6. Reports ready

---

## VM Examples

### Install on Single VM

**Request:**
```
install pytest on my ubuntu VM
```

**What happens:**
1. Scans VirtualBox/VMware/Parallels for running VMs
2. Finds "ubuntu" VM (e.g., 192.168.56.101)
3. Tests SSH connectivity
4. If SSH not available:
   - Installs openssh-server via VBoxManage guestcontrol
   - Starts SSH service
5. Configures passwordless SSH keys
6. Creates VM snapshot (checkpoint)
7. Installs pytest via pip3
8. Verifies: `python3 -m pytest --version`
9. Cleans up checkpoint
10. Reports ready

---

### Install on Multiple VMs

**Request:**
```
install docker and redis on staging and production VMs
```

**What happens:**
1. Detects 2 VMs: staging-ubuntu, prod-ubuntu
2. Configures SSH for both (parallel)
3. Creates snapshots for both
4. Installs on both in parallel:
   - Agent 1: staging-ubuntu (docker, redis)
   - Agent 2: prod-ubuntu (docker, redis)
5. Starts services on both
6. Verifies on both
7. Cleans up checkpoints
8. Reports both ready

---

## Remote Server Examples

### Direct SSH to Remote Server

**Request:**
```
install rust on server.example.com
```

**What happens:**
1. Tests SSH to server.example.com
2. Creates system backup
3. Fetches latest stable Rust version
4. Installs via rustup
5. Verifies: `rustc --version`
6. Reports ready

---

### Remote Server via Jump Host

**Request:**
```
install kubectl on prod-k8s.company.local via bastion.company.local
```

**What happens:**
1. Tests SSH to bastion.company.local
2. Configures ProxyJump in ~/.ssh/config:
   ```
   Host bastion
     HostName bastion.company.local
     User youruser

   Host prod-k8s
     HostName prod-k8s.company.local
     User youruser
     ProxyJump bastion
   ```
3. Tests multi-hop connectivity
4. Creates system backup on prod-k8s
5. Installs kubectl
6. Verifies: `kubectl version --client`
7. Reports ready with access command: `ssh prod-k8s`

---

## GPU Server Examples

### Install GPU Drivers and Docker

**Request:**
```
install docker with nvidia GPU support on my GPU server
```

**What happens:**
1. Detects GPU: `lspci | grep -i nvidia` → RTX 4090 found
2. Checks drivers: `nvidia-smi` → not installed
3. Creates system backup
4. Installs appropriate NVIDIA driver (nvidia-driver-535)
5. Installs Docker
6. Installs NVIDIA Container Toolkit (nvidia-docker2)
7. Configures Docker runtime for GPU
8. **Reboots server**
9. **Polls until server returns** (automatic)
10. Verifies GPU driver: `nvidia-smi`
11. Verifies Docker GPU access: `docker run --gpus all nvidia/cuda:12.1.0-base nvidia-smi`
12. Reports ready with GPU confirmed

---

### Install ML Stack on GPU Server Behind Bastion

**Request:**
```
install pytorch with CUDA and jupyter on gpu-server.company.local via bastion.company.local
```

**What happens:**
1. Configures jump host SSH (bastion → gpu-server)
2. Detects GPU hardware
3. Creates system backup
4. Installs/verifies NVIDIA drivers
5. Installs CUDA toolkit
6. Installs PyTorch with correct CUDA version:
   ```bash
   pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
   ```
7. Installs Jupyter
8. Configures Jupyter for remote access (port 8888)
9. Verifies PyTorch CUDA: `python3 -c 'import torch; print(torch.cuda.is_available())'` → True
10. Reports ready with:
    - SSH command: `ssh gpu-server`
    - Jupyter access: `ssh -L 8888:localhost:8888 gpu-server`

---

## Production Examples

### Multi-Package on Production Servers

**Request:**
```
install docker, node 20, postgresql, redis, and python testing tools on staging and prod
```

**Clarification asked:**
```
Python testing tools options:
1. pytest only
2. pytest + pytest-cov
3. pytest + pytest-cov + pytest-xdist + tox
4. Full suite

Your choice?
```

**User selects:** 3

**What happens:**
1. Detects 2 servers: staging, prod
2. Creates backups on both
3. Parallel installation:
   - Agent 1: staging-server
     - Adds NodeSource repo
     - Installs docker, nodejs 20, postgresql, redis
     - Installs pytest + pytest-cov + pytest-xdist + tox
     - Starts all services
     - Verifies all packages
   - Agent 2: prod-server (same as Agent 1)
4. Reports both servers ready with full package list and versions

---

## AI Tool Specific

### Claude Code Usage

```bash
# In Claude Code terminal:
install docker on my ubuntu VM

# Skill auto-activates and handles everything
```

---

### Aider Usage

```bash
cd /path/to/auto-installer
aider

# In Aider:
Read SKILL.md and install pytest and numpy
```

---

### ChatGPT CLI Usage

```bash
chatgpt --context /path/to/auto-installer/SKILL.md

# In ChatGPT:
install docker and postgres on server.example.com
```

---

### Direct Prompt (Any AI)

```
You are an autonomous software installer. Read the file SKILL.md in the current directory.

CRITICAL RULES:
1. Execute ALL commands yourself - NO manual instructions
2. Only ask for: passwords, disambiguation, risky operation confirmations
3. Report "READY TO USE" only when fully verified

Now install: docker with GPU support and pytorch on gpu-server.company.local via bastion.company.local
```

---

## Advanced Examples

### Container Installation

**Request:**
```
install curl in my nginx container
```

**What happens:**
1. Detects running containers: `docker ps`
2. Finds nginx container
3. Executes: `docker exec nginx apt update && apt install -y curl`
4. Verifies: `docker exec nginx which curl`
5. Reports ready

---

### Kubernetes Pod Installation

**Request:**
```
install redis-cli in my app-pod
```

**What happens:**
1. Detects pod: `kubectl get pods | grep app-pod`
2. Executes: `kubectl exec app-pod -- apt update && apt install -y redis-tools`
3. Verifies: `kubectl exec app-pod -- redis-cli --version`
4. Reports ready

---

### Ambiguous Package Clarification

**Request:**
```
install docker
```

**Clarification:**
```
Docker options:
1. docker.io (Docker Engine - CLI only)
2. docker-desktop (Full Desktop with GUI)

Recommended for servers: Option 1
Recommended for development: Option 2

Your choice?
```

**User selects:** 1

**Proceeds with docker.io**

---

## Error Recovery Examples

### Missing Repository

**Request:**
```
install gh
```

**Error encountered:**
```
E: Unable to locate package gh
```

**Autonomous fix:**
1. Detects: Package not in default repos
2. Searches for solution: GitHub CLI requires official repo
3. Adds repository:
   ```bash
   curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
   echo "deb [signed-by=...] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list
   sudo apt update
   ```
4. Retries: `sudo apt install -y gh`
5. Success: gh 2.40.1 installed
6. Reports ready

---

### Permission Denied

**Request:**
```
install pytest
```

**Error encountered:**
```
Permission denied
```

**Autonomous fix:**
1. Retries with --user flag: `pip3 install --user pytest`
2. Success
3. Reports ready

---

These examples demonstrate the full range of Auto-Installer capabilities across different environments and scenarios.
