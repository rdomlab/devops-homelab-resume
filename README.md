# My DevOps / Cloud Engineer Homelab Portfolio

Goal: Build & document an enterprise-grade Kubernetes homelab on Proxmox → host personal resume site → demonstrate real DevOps skills (IaC, GitOps, observability, security, YAML mastery) to land first Cloud/DevOps role.

## Hardware
- CPU: AMD Ryzen 9 5900X (12c/24t)
- RAM: 32 GB
- GPU: NVIDIA RTX 3080/3089 (24 GB) – passthrough possible later
- Storage: ~2.73 TB SSD (2× drives)

## Roadmap – Current Status
- [ ] Phase 1: Proxmox bare-metal install + basic hardening
- [ ] Phase 2: Networking (DNS / firewall / VLANs if possible)
- [ ] Phase 3: Multi-node-ish Kubernetes cluster (k3s / kubeadm)
- [ ] Phase 4: GitOps (ArgoCD or Flux)
- [ ] Phase 5: Hugo resume site + domain + HTTPS (cert-manager + nginx-ingress)
- [ ] Phase 6: Monitoring stack (Prometheus + Grafana + Loki)
- [ ] Phase 7: Security hardening & scans
- [ ] Phase 8: Backup, chaos testing, polish docs

## Documentation Style
Each phase gets its own folder `/docs/phase-01-proxmox/` with:
- architecture.md (diagram + explanation)
- step-by-step.md
- security-decisions.md
- All YAML/config files committed
