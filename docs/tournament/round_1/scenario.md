# Round 1 Test Scenario

**User Request:** "install docker and pytest on my ubuntu VM"

**Context:**
- Platform: macOS (host system)
- VM: Ubuntu 22.04 running in VirtualBox
- VM IP: 192.168.56.101
- VM not yet SSH-configured
- User has VM root password
- Mount point: Not yet created

**Complexity:** Medium
- 2 packages from different ecosystems (system + Python)
- Remote VM target (not local)
- SSH setup required
- Mount point setup required

**What skill must do:**
1. Detect VM and get IP
2. Configure SSH access
3. Setup mount points
4. Install docker.io
5. Install pytest via pip3
6. Verify both work
7. Report complete
