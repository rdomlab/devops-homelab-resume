# Phase 2 – First LXC Container: Ubuntu with Nginx Hello World

## Goal
Create a lightweight LXC container on Proxmox to host a simple nginx "Hello World" web server. This demonstrates Proxmox container management, basic networking/firewall testing, and serves as a testbed for future Kubernetes nodes (e.g., deploying resume site pods).
<img width="1359" height="363" alt="node-level-fw-rules" src="https://github.com/user-attachments/assets/12b63f94-098c-4fdb-9fd2-935b5035a97f" />
<img width="916" height="272" alt="nginx-test-welcome-page" src="https://github.com/user-attachments/assets/461668f0-ffc8-41d9-85a4-9a1522f4e9e4" />

## Why LXC for This Project?
- LXC (Linux Containers) are lighter/faster than full VMs (share host kernel) — ideal for homelab efficiency on my Ryzen 9 hardware (low overhead, quick start).
- Preps for enterprise K8s: LXCs teach container isolation (namespaces, cgroups) without Docker/K8s yet.
- Why nginx? Simple static site test (mirrors Hugo resume site later); verifies network access via Proxmox bridge/firewall.
- Security tie-in: Run as non-root, test firewall rules (allow port 80 from LAN only).
- Architecture knowledge: Proxmox LXC sits on host (pve) → bridged network (vmbr0) → LAN IP → accessible from laptop but blocked externally.

## High-Level Architecture Diagram (Mermaid)
<img width="681" height="281" alt="pve-LXC-architecture-diagram" src="https://github.com/user-attachments/assets/fb81786c-2cee-4779-b52a-c1f76da41a6e" />
