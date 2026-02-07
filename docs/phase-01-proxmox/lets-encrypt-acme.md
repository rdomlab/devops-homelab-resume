# Securing Proxmox GUI with Let's Encrypt + Cloudflare DNS-01

## Goal
Replace self-signed certificate with trusted Let's Encrypt cert for https://pve.rdomlab.xyz:8006

## Why this way?
- DNS-01 challenge → no need to expose port 80/8006 to internet
- Cloudflare → free, fast DNS + API for automation
- Staging first → avoid rate limits during testing

## Architecture
(simple text diagram)
Laptop ──(LAN)──► pve.rdomlab.xyz (192.168.1.23:8006)
                  ↑
            Cloudflare DNS (A record → local IP)

## Steps I followed
1. Set up Cloudflare for rdomlab.xyz (nameservers change in Namecheap)
2. Created scoped API token (DNS Edit + Zone Read, zone-specific)
3. Added A record pve → public IP (temporary)
4. In Proxmox ACME:
   - Created Cloudflare challenge plugin (CF_Token + CF_Account_ID)
   - Created staging account → tested order
   - Created production account → ordered real cert
5. Fixed browser cache & pveproxy restart issues
6. Switched A record to local IP 192.168.1.23 for LAN access

## Security Decisions
- Scoped token, no global key
- No port forwarding (never expose pveproxy to WAN)
- Validation delay 60s (later increased if needed)
- Used staging environment first

## Lessons Learned
- Staging certs are NOT trusted by browsers (on purpose)
- pveproxy restart can hang → use pkill -9 + start
- DNS propagation & cache matter a lot

Screenshots: (add blurred ones – token creation, ACME GUI, final cert details, Tasks log)
