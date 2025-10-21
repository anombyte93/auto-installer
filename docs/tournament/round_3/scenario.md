# Round 3 Test Scenario (Edge Cases)

**User Request:** "install the latest stable rust compiler, docker with nvidia GPU support, and python ML stack on my remote GPU server at gpu-server.mycompany.local that's behind a jump host at bastion.mycompany.local"

**Context:**
- Platform: macOS (local)
- Target: Remote GPU server (not a VM, actual remote server)
- Jump host configuration required (SSH through bastion)
- No direct SSH access to target (must go through jump host)
- GPU support required (nvidia-docker2)
- "Python ML stack" is highly ambiguous (could be 20+ packages)
- "Latest stable rust" requires checking rust-lang.org
- Server might not have NVIDIA drivers installed
- Unknown server OS (could be Ubuntu, RHEL, Debian, etc.)

**Edge Cases Being Tested:**
1. Jump host/bastion configuration
2. GPU driver detection and installation
3. Highly ambiguous package definition (ML stack)
4. Remote server (not VM) detection
5. Latest version detection (not in package manager)
6. Multi-hop SSH configuration
7. Unknown target OS detection
8. Critical hardware requirements (GPU)
9. System reboot handling
10. Post-reboot polling and verification

**What skill must do:**
1. Configure multi-hop SSH (local → bastion → gpu-server)
2. Detect target OS
3. Detect GPU hardware
4. Clarify "python ML stack" ambiguity
5. Fetch latest Rust version from rust-lang.org
6. Install NVIDIA GPU drivers
7. Install Docker + nvidia-container-toolkit
8. Install Rust via rustup
9. Install PyTorch with CUDA support
10. Handle system reboot for GPU drivers
11. Poll until server comes back online
12. Verify GPU access in Docker
13. Verify PyTorch CUDA availability
14. Configure Jupyter for remote access
15. Report complete with access instructions
