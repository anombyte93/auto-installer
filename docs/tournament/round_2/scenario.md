# Round 2 Test Scenario

**User Request:** "install docker, node 20, postgresql, redis, and python testing tools on my staging VM and my production VM"

**Context:**
- Platform: macOS (host)
- VMs:
  - staging-ubuntu (192.168.56.101) - Running
  - prod-ubuntu (192.168.56.102) - Running
- Multiple packages with version requirements
- Multiple target VMs (parallel VM installation)
- "python testing tools" is ambiguous (pytest? pytest-cov? tox? all?)

**Complexity:** High
- 5+ packages across multiple ecosystems
- Version-specific requirement (node 20)
- Ambiguous package ("python testing tools")
- Multiple VMs requiring parallel orchestration
- Production VM → higher safety requirements

**What skill must do:**
1. Detect both VMs
2. Clarify "python testing tools" ambiguity
3. Setup SSH/checkpoints for both VMs
4. Install packages on both VMs (ideally in parallel)
5. Handle Node.js 20 repository addition
6. Start all services (docker, postgresql, redis)
7. Verify all packages on both VMs
8. Report complete for both VMs
