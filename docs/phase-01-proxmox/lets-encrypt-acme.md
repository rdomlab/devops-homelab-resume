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
- Cloudflare token creation <img width="1182" height="351" alt="rdomlab-token" src="https://github.com/user-attachments/assets/1393cb22-69ea-4716-9a3e-d61100fb9792" />
<img width="991" height="264" alt="rdomlab-token-active" src="https://github.com/user-attachments/assets/4073a5d6-b8b5-482c-9752-6fd3b64f5171" />

- Proxmox ACME plugin config <img width="960" height="304" alt="rdomlab-plugin-config" src="https://github.com/user-attachments/assets/0f6e7b16-2804-4894-b62a-33adb7bcda34" />

- Tasks log from production order <br />
<img width="564" height="407" alt="rdomlab-pve-Order-Cert" src="https://github.com/user-attachments/assets/6d8a03d9-2152-4aea-8aed-245ce50760f8" />


- Final green padlock + cert details (issuer: Let's Encrypt R12/R13) <img width="889" height="503" alt="rdomlab-no-ssl" src="https://github.com/user-attachments/assets/ec568b79-2a23-49d1-8b92-2b38c531ac58" />
<img width="893" height="503" alt="rdomlab-with-ssl" src="https://github.com/user-attachments/assets/750275a6-caf2-4fa6-8a23-37991fbd1888" />


