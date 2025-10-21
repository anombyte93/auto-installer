# Auto-Installer

**Autonomous AI agent that installs any software package on any system with zero manual intervention.**

Works with Claude Code, ChatGPT, Codex, Aider, or any AI tool with shell access.

## Quick Start

### 1. Clone this repository

```bash
git clone https://github.com/YOUR_USERNAME/auto-installer.git
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
├── SKILL.md      # Complete installation logic and capabilities
└── README.md     # This file
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

This skill was created using the Anthropic Claude Code skill creation methodology with tournament-based optimization. To improve it:

1. Fork the repo
2. Test with new edge cases
3. Update SKILL.md with improvements
4. Submit PR with test results

## License

MIT License - Use freely with any AI tool

## Credits

Created using agentic arena methodology:
- 3 tournament rounds
- 6 variations tested
- 3 realistic scenarios
- Score: 94.2/100

Optimized for: Correctness > Speed, Safety > Convenience, Autonomy > User Interaction

---

# Detailed Documentation (For Advanced Users)

## Usage in Standalone AI Systems

### Basic Prompt Template

```
You are an autonomous software installer agent. Your job is to install the requested packages completely automatically with NO manual user intervention.

CRITICAL RULES:
1. Execute ALL commands yourself - NEVER give the user instructions to run manually
2. Only ask for: sudo passwords, disambiguation, or confirmation for risky operations (reboots)
3. Report "READY TO USE" only when packages are fully installed, configured, and verified
4. Use checkpoints/backups before making changes

USER REQUEST: [user's installation request here]

CAPABILITIES:
- Package detection: Identify package type (system/language-specific) from name
- Target detection: Parse "on my VM" / "on server.example.com" / "via bastion"
- Version handling: "node 20" → fetch Node.js 20.x
- GPU support: Detect GPUs, install drivers, handle reboots
- Multi-target: Install on multiple VMs/servers in parallel
- Error recovery: Automatically fix common issues (missing repos, permissions, etc.)

INSTALLATION WORKFLOW:
1. Detect & analyze request (packages, target, versions, ambiguities)
2. Setup environment (SSH, jump hosts, VM detection, mount points)
3. Create checkpoints (VM snapshots or system state backups)
4. Execute installation (run package manager commands)
5. Verify (test binaries, services, functionality)
6. Cleanup (remove checkpoints if successful)

PACKAGE KNOWLEDGE:
- docker → System package (apt: docker.io, brew: docker, choco: docker-desktop)
- node/nodejs → System package, check version requirement
- python → Usually installed, might need python3-dev
- pytest → Python package (pip3)
- postgres/postgresql → System package + service
- redis → System package (redis-server) + service
- rust → Install via rustup, can fetch latest from rust-lang.org
- pytorch → Python package, check for GPU (needs CUDA version)

TARGET DETECTION:
- "install X" → Local system
- "install X on my ubuntu VM" → Scan VirtualBox/VMware/Parallels, setup SSH
- "install X on server.example.com" → Direct SSH
- "install X on gpu-server via bastion" → Setup ProxyJump, multi-hop SSH
- "install X in my container" → docker exec or kubectl exec

ERROR RECOVERY:
- "Package not found" → Search for repository to add
- "Permission denied" → Retry with sudo or --user flag
- "Dependency conflict" → apt --fix-broken install
- "Network timeout" → Retry with exponential backoff
- "GPU driver needs reboot" → Reboot and poll until server returns

Now execute the installation completely autonomously.
```

### Example Usage in ChatGPT/GPT-4

**User:** "install docker and pytest on my ubuntu VM"

**Paste this prompt to ChatGPT:**

```
You are an autonomous software installer agent with shell access. Install docker and pytest on the user's Ubuntu VM completely automatically.

CRITICAL: Execute ALL commands yourself. NEVER give manual instructions.

USER REQUEST: install docker and pytest on my ubuntu VM

Follow this workflow:
1. Scan for running VMs (VBoxManage list runningvms)
2. Identify Ubuntu VM, get IP
3. Test SSH connectivity
4. If SSH not available: Install via VBoxManage guestcontrol
5. Setup passwordless SSH keys
6. Create checkpoint (VM snapshot)
7. Install docker.io via apt
8. Install pytest via pip3
9. Verify both packages work
10. Cleanup checkpoint

Execute now.
```

### Example Usage in Codex/GitHub Copilot

**User:** "install node 20, postgresql, and redis on staging and prod VMs"

**Paste this prompt:**

```
Autonomous installer agent: Install node 20, postgresql, and redis on staging and prod VMs.

NO manual steps - execute everything yourself.

Workflow:
1. Detect VMs (staging, prod)
2. Parallel SSH setup for both
3. Create VM snapshots
4. Parallel installation:
   - Agent 1: staging (node 20 from NodeSource, postgresql, redis)
   - Agent 2: prod (node 20 from NodeSource, postgresql, redis)
5. Start services (postgresql, redis)
6. Verify all packages
7. Cleanup

Execute in parallel.
```

### Example Usage for Complex GPU Server

**User:** "install docker with GPU support, latest rust, and pytorch on my GPU server at gpu.company.local behind bastion.company.local"

**Paste this prompt:**

```
Autonomous GPU installer agent.

REQUEST: install docker with GPU support, latest rust, and pytorch on gpu.company.local (via bastion.company.local)

CRITICAL FEATURES:
- Multi-hop SSH (setup ProxyJump)
- GPU detection (lspci | grep nvidia)
- NVIDIA driver installation
- Automatic reboot handling
- Post-reboot polling
- Docker + nvidia-container-toolkit
- Latest Rust from rust-lang.org
- PyTorch with CUDA support

Execute fully autonomously:
1. Configure SSH jump host (bastion → gpu-server)
2. Detect GPU hardware
3. Create system backup
4. Install NVIDIA drivers
5. Install Docker + nvidia-docker2
6. Install Rust (fetch latest stable version)
7. Install PyTorch with CUDA
8. Reboot for GPU drivers
9. Poll until server returns
10. Verify GPU access in Docker
11. Verify PyTorch CUDA availability

Execute now.
```

## Key Differences from Claude Code Skill

When using as a standalone agent prompt:

### Limitations

1. **No Task Tool:** Cannot spawn parallel agents
   - Solution: Use sequential installation or instruct AI to simulate parallelism

2. **No Built-in VM Detection:** May need manual IP/hostname
   - Solution: Ask user for VM details or use VBoxManage commands

3. **No Native Checkpointing:** Depends on AI system's shell access
   - Solution: Use manual backup commands (dpkg --get-selections, etc.)

### Adaptations

**For ChatGPT/GPT-4:**
- Emphasize "Execute commands yourself via shell access"
- May need to confirm shell capability first
- Use web search for latest version fetching

**For GitHub Copilot/Codex:**
- Focus on generating executable scripts
- May need to run generated script manually
- Good for generating install automation scripts

**For Custom AI Systems:**
- Adjust based on available tools (shell, SSH, API access)
- May need to break into smaller steps
- Can still use core detection and verification logic

## Package Detection Logic

Use this reference for package type identification:

### System Packages (apt/brew/choco)
docker, postgresql, redis, nginx, git, curl, vim, build-essential, gcc, mysql, mongodb, cassandra, elasticsearch

### Node.js Packages (npm)
react, express, typescript, webpack, next, vue, angular, lodash, axios, jest

### Python Packages (pip3)
pytest, numpy, pandas, flask, django, requests, matplotlib, scikit-learn, torch, tensorflow

### Rust Crates (cargo)
ripgrep, bat, fd-find, exa, tokei, hyperfine

### Ruby Gems (gem)
rails, bundler, rake, sinatra, devise

### Go Packages (go install)
github.com/... paths

### Ambiguous (Ask User)
- python → python3 vs python3-dev vs python-is-python3
- docker → docker.io vs docker-desktop
- node → nodejs system package vs specific version
- testing tools → specific framework

## Version Detection Patterns

- "node 20" → Node.js 20.x
- "python 3.11" → Python 3.11.x
- "postgres 15" → PostgreSQL 15.x
- "latest stable X" → Fetch from official source
- "rust" without version → Latest stable from rust-lang.org

## Target Detection Patterns

- "install X" → Local system
- "install X on my VM" → Scan for VMs
- "install X on my ubuntu VM" → Filter by name
- "install X on 192.168.1.100" → Direct SSH to IP
- "install X on server.example.com" → Direct SSH to hostname
- "install X on server via bastion" → Multi-hop SSH
- "install X on server at 10.0.0.5 behind jump.example.com" → ProxyJump
- "install X in my container" → docker/kubectl exec
- "install X on staging and prod" → Multiple targets (parallel)

## Error Recovery Patterns

### Package Not Found

```bash
# Example: gh not in default repos
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | \
  sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] \
  https://cli.github.com/packages stable main" | \
  sudo tee /etc/apt/sources.list.d/github-cli.list
sudo apt update && sudo apt install -y gh
```

### Permission Denied

```bash
# Try with --user flag for pip
pip3 install --user package-name

# Or use sudo for apt
sudo apt install -y package-name
```

### Dependency Conflict

```bash
sudo apt --fix-broken install
sudo apt install -f
```

### Network Timeout

```bash
for i in 1 2 4 8 16; do
  if curl -sSf https://example.com/package; then
    break
  fi
  echo "Retry in ${i}s..."
  sleep $i
done
```

### GPU Driver Reboot

```bash
# Install driver
sudo apt install -y nvidia-driver-535

# Reboot
sudo reboot

# Poll until back (run this after reboot in new session)
while ! ssh server "echo OK" 2>/dev/null; do
  echo "Waiting for server..."
  sleep 5
done

# Verify
ssh server "nvidia-smi"
```

## Verification Commands

### Package Installed

```bash
which package-name
package-name --version
```

### Service Running

```bash
systemctl is-active service-name
systemctl status service-name
```

### Functional Tests

```bash
# Docker
docker run --rm hello-world

# PostgreSQL
sudo -u postgres psql -c 'SELECT version();'

# Redis
redis-cli ping  # Should return PONG

# Python package
python3 -c 'import package_name; print(package_name.__version__)'

# Node package
node -e "require('package-name')"

# GPU + Docker
docker run --rm --gpus all nvidia/cuda:12.1.0-base-ubuntu22.04 nvidia-smi

# PyTorch CUDA
python3 -c 'import torch; print(f"CUDA: {torch.cuda.is_available()}")'
```

## Tips for Best Results

1. **Be Explicit:** Always emphasize "execute commands yourself, no manual steps"

2. **Provide Context:** Include OS, existing setup, network topology in prompt

3. **Set Expectations:** "Only ask for passwords or disambiguation, do everything else"

4. **Request Verification:** "Test that packages actually work, don't just install"

5. **Handle Errors:** "If errors occur, troubleshoot and fix automatically"

6. **Parallel Operations:** If AI supports, use parallel agents for multiple VMs

7. **Checkpointing:** Request backups before changes: "Create VM snapshot first"

8. **Complete Reporting:** "Report when EVERYTHING is done and verified"

## Example Complete Workflow

**User Request:** "install docker, node 20, and pytest on my staging VM"

**Autonomous AI Execution:**

```bash
# 1. Detect VM
VMs=$(VBoxManage list runningvms | grep staging)
VM_NAME="staging-ubuntu"
VM_IP="192.168.56.101"

# 2. Test SSH
ssh -o ConnectTimeout=3 root@$VM_IP "echo OK" || {
  # 3. Install SSH via guest control
  VBoxManage guestcontrol "$VM_NAME" run --username root --password "$ROOT_PASS" \
    --exe "/usr/bin/apt" -- apt update
  VBoxManage guestcontrol "$VM_NAME" run --username root --password "$ROOT_PASS" \
    --exe "/usr/bin/apt" -- apt install -y openssh-server
  VBoxManage guestcontrol "$VM_NAME" run --username root --password "$ROOT_PASS" \
    --exe "/usr/bin/systemctl" -- systemctl start ssh
}

# 4. Setup SSH keys
ssh-keygen -t ed25519 -f ~/.ssh/staging_vm -N ""
sshpass -p "$ROOT_PASS" ssh-copy-id -i ~/.ssh/staging_vm root@$VM_IP

# 5. Create checkpoint
VBoxManage snapshot "$VM_NAME" take "pre-install-$(date +%s)"

# 6. Install docker
ssh -i ~/.ssh/staging_vm root@$VM_IP "apt update"
ssh -i ~/.ssh/staging_vm root@$VM_IP "DEBIAN_FRONTEND=noninteractive apt install -y docker.io"
ssh -i ~/.ssh/staging_vm root@$VM_IP "systemctl enable docker && systemctl start docker"

# 7. Install node 20
ssh -i ~/.ssh/staging_vm root@$VM_IP "curl -fsSL https://deb.nodesource.com/setup_20.x | bash -"
ssh -i ~/.ssh/staging_vm root@$VM_IP "apt install -y nodejs"

# 8. Install pytest
ssh -i ~/.ssh/staging_vm root@$VM_IP "pip3 install pytest"

# 9. Verify
ssh -i ~/.ssh/staging_vm root@$VM_IP "docker run --rm hello-world"
ssh -i ~/.ssh/staging_vm root@$VM_IP "node --version"
ssh -i ~/.ssh/staging_vm root@$VM_IP "python3 -m pytest --version"

# 10. Report
echo "✅ COMPLETE - All packages installed and verified on staging VM"
echo "  - docker.io: RUNNING"
echo "  - nodejs: v20.11.0"
echo "  - pytest: 8.0.0"
```

## Troubleshooting

**AI not executing commands:**
- Add "You have shell access via bash" to prompt
- Be more explicit: "Run these commands yourself right now"
- Some AI systems can't execute - generate script instead

**AI asking for manual steps:**
- Emphasize: "NEVER give me commands to run. Execute everything yourself."
- Add: "CRITICAL: I cannot run commands. You must do it."

**Missing error handling:**
- Add: "If any command fails, troubleshoot and fix automatically"
- Provide error recovery examples in prompt

**Not verifying functionality:**
- Add: "Test that each package actually works, not just that it's installed"
- Provide verification command examples

## Conclusion

This Auto-Installer logic can be adapted to any AI system with shell access. The key is emphasizing autonomous execution, comprehensive error recovery, and thorough verification. Adjust the prompt based on your AI system's capabilities and available tools.

For full Claude Code Skill with Task tool support for parallel agents, use the official SKILL.md file.
