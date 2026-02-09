# Phase 1 – Proxmox GUI Security: Let's Encrypt + Cloudflare DNS-01

## Objective
Replace scary self-signed cert with free, auto-renewing, browser-trusted certificate.

## Why this approach?
- DNS-01 challenge = no need to expose port 80 or 8006 publicly (security win)
- Cloudflare = fast propagation, free API, scoped tokens (least privilege)
- Staging environment first = safe testing without hitting real rate limits

## High-Level Diagram (use Mermaid or ASCII art)
![mermaid-diagram-2026-02-08-004248 (1)](https://github.com/user-attachments/assets/0ade1738-baac-4614-9fdb-4750ad17365a)


## Key Steps I Performed
1. Changed nameservers from Namecheap → Cloudflare
2. Created scoped API token (DNS:Edit + Zone:Read, zone-specific only!)
3. Added temporary A record → public IP (for validation)
4. In Proxmox GUI → Datacenter → ACME:
   - Added Cloudflare challenge plugin (CF_Token + CF_Account_ID)
   - Created staging account → ordered test cert (success but browser warning expected)
   - Created production account → ordered real cert (TASK OK)
5. Troubleshooting:
   - pveproxy restart hang → used `pkill -9 pveproxy` + `systemctl start`
   - Persistent warning → staging issuer, browser cache
   - Timeout on domain → changed A record back to local LAN IP 192.168.1.23
6. Final result: Green padlock at https://pve.rdomlab.xyz:8006

## Security Choices & Lessons
- Scoped token (never global API key!)
- No port forwarding (never expose pveproxy to WAN)
- Validation delay 60s (bump if propagation slow)
- Staging first = rate-limit safety net
- Always test in incognito + flush DNS

## Proof (add screenshots – blur secrets!)
- Cloudflare token creation
- Proxmox ACME plugin config
- Tasks log from production order
- Final green padlock + cert details (issuer: Let's Encrypt R12/R13)

