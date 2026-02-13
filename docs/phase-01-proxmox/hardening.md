# Basic Host Firewall Configuration

## Goal: Allow only what you need from your LAN (192.168.1.0/24), drop everything else.
Suggested minimal set for host level (pve node):

- Allow SSH (port 22) from LAN only
- Allow web GUI (port 8006) from LAN only
- Allow ICMP (ping) from LAN (optional but nice for troubleshooting)
- Default input policy: DROP
- No WAN/internet rules (no forwarding yet)

## Diagram (Laptop → LAN → Proxmox)
<img width="625" height="315" alt="rdomlab-proxmox-network" src="https://github.com/user-attachments/assets/4f2a3f03-61ed-4447-8e69-82090ac82292" />

## Rules table
<img width="1269" height="266" alt="rdomlab-firewall-conf" src="https://github.com/user-attachments/assets/4d33662a-fd04-4d70-90c7-73da8d46219d" />

## Tests performed & results
- From your laptop (LAN): https://pve.rdomlab.xyz:8006 → works
- From phone on mobile data (not LAN): timeout → good (blocked)
- SSH from laptop: ssh root@192.168.1.23 → works
- ssh root@pve.rdomlab.xyz → works (DNS resolves locally)
  
## What I'd add next (e.g. rate-limiting, fail2ban later)
- a 'management' group
- fail2ban

## Troubleshooting Lockout After Tweaks
- What happened: Added DROP rule + moved SSH → lost GUI/SSH (ping OK).
- Why: Rules misorder or source mismatch → default DROP blocked.
- Fix steps:
  1. Physical console (Ctrl+Alt+F1) → login root.
  2. `systemctl stop pve-firewall` → restored access. <img width="1358" height="549" alt="rdomlab-firewall-status" src="https://github.com/user-attachments/assets/7774df76-fc0a-4d37-a6af-1f72ceeeecf4" />

  3. Fixed rules in GUI (ensured DROP last, sources correct).
  4. Restarted firewall + tested.
- Lesson: Test incrementally, keep console open during changes.

## Automatic Security Updates Configuration (unattended-upgrades)

**Status**: Configured for Debian security only. Proxmox packages blacklisted (manual upgrade recommended for kernel/host stability).

**Key Config**:
- Origins: Debian trixie-security
- Blacklist: proxmox.*, pve.* (pinned -32768)
- No auto-reboot

**Dry-Run Output** (example):
No packages found that can be upgraded unattended...

**GUI Warning Suppression**:
- Edited proxmoxlib.js to disable subscription popup.
- Restarted pveproxy → clean dashboard.

**Why this way**:
- Auto-patches Debian vulns (reduces attack surface).
- Manual Proxmox updates → control over potential breaking changes.
- Aligns with production caution (no-sub repo warning is expected/cosmetic).

# SSH Hardening on Proxmox Host

## Goal
Eliminate password logins, disable root SSH, use key-only auth — secure access before creating K8s cluster VMs.

## Why
- Root + password = high risk (brute-force common).
- Key auth stronger + auditable.
- Aligns with production Kubernetes security (no root logins, audited access).

## Steps Performed
1. Created sudo user `devops`.
2. Generated ed25519 key on laptop → copied to host.
3. Edited /etc/ssh/sshd_config:
   - PermitRootLogin no
   - PasswordAuthentication no
4. Restarted ssh service.
5. Tested: Key login works, root fails.

## Tests
- ssh devops@192.168.1.23 → success (key)
- ssh root@192.168.1.23 → denied (publickey)
- From external IP (phone hotspot) → fails (firewall blocks anyway)

## Security Decisions
- ed25519 keys (modern, secure).
- No password fallback.
- Fail2ban added for brute-force protection.

## Lessons
- Always test in new session before closing old one.
- Physical console = safety net for recovery.

## Proof
- Screenshot of successful key login

<img width="584" height="339" alt="rdomlab-login-success" src="https://github.com/user-attachments/assets/3323b81f-4944-40e5-b490-02223f758f59" />

- Screenshot of failed root attempt

<img width="585" height="178" alt="rdomlab-root-login-fail" src="https://github.com/user-attachments/assets/6c76a8b8-6149-4eb7-ae8c-9df4a411c111" />
