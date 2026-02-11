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
