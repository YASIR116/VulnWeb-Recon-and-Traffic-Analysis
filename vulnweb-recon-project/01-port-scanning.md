# 01 — Port Scanning with Nmap

**Target:** `testphp.vulnweb.com`
**Severity of exposure:** Low (informational — CVSS ~2.5)
**Goal:** Enumerate open ports and identify the services/versions running behind them, since these are the entry points an attacker would map first.

## Commands Used

```bash
# Basic scan
nmap testphp.vulnweb.com

# Service/version detection
nmap -sV testphp.vulnweb.com

# Aggressive scan (OS detection, scripts, traceroute)
nmap -A testphp.vulnweb.com

# Firewall/IPS-aware scan
nmap -Pn testphp.vulnweb.com

# Full port range
nmap -p- testphp.vulnweb.com
```

## Results

| Port | State | Service | Version |
|---|---|---|---|
| 21/tcp | Open | FTP | — |
| 80/tcp | Open | HTTP | Nginx 1.19.0 |
| 554/tcp | Open | RTSP | — |
| 1723/tcp | Open | PPTP | — |

## Analysis

- **Port 80 (HTTP)** — the main web application entry point, running on Nginx 1.19.0. This is the primary attack surface for the rest of the assessment.
- **Port 21 (FTP)** — a legacy file transfer service. FTP transmits credentials and data unencrypted by default, making it a common target for credential sniffing.
- **Port 554 (RTSP)** — a media streaming service. Its presence here is unusual for a typical web app and worth flagging as unnecessary exposure.
- **Port 1723 (PPTP)** — an outdated VPN protocol with well-documented cryptographic weaknesses (MS-CHAPv2 exposure); it should not be relied on for secure remote access in a modern environment.

Having four different, unrelated services open on one host also increases the overall attack surface — each additional service is another potential point of compromise, even if the web app itself is hardened.

## Recommendations

- Disable services that aren't actively required (RTSP, PPTP if unused).
- Replace FTP with SFTP/FTPS.
- Replace PPTP with a modern VPN protocol (WireGuard/OpenVPN + IPSec).
- Keep the web server (Nginx) patched and enforce HTTPS with HSTS.
