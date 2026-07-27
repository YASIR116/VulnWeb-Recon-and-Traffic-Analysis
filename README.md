# VulnWeb-Recon-and-Traffic-Analysis
# VulnWeb Recon & Traffic Analysis

A beginner-level web application security assessment performed against **[testphp.vulnweb.com](http://testphp.vulnweb.com)** — Acunetix's intentionally vulnerable test site, which is publicly provided for security training and CTF-style practice.

This project documents three core phases of a basic external web recon exercise: **port scanning**, **directory enumeration**, and **network traffic analysis**, along with findings and remediation recommendations for each.

> ⚠️ **Scope note:** `testphp.vulnweb.com` is a legal, intentionally vulnerable target maintained for public security testing and training. All activity described here was performed against this sanctioned test environment only — never attempt these techniques against systems you don't have explicit authorization to test.

---

## Objective

To practice and document a standard reconnaissance workflow used in web application penetration testing:

1. Identify exposed network services via port scanning
2. Enumerate hidden or unlinked directories/files
3. Capture and analyze login traffic to check for plaintext credential exposure

## Tools Used

| Tool | Purpose |
|---|---|
| **Nmap** | Port scanning and service/version detection |
| **Gobuster** | Directory/file brute-forcing |
| **Dirb** | Secondary directory enumeration (cross-check) |
| **Wireshark** | Network packet capture and traffic analysis |
| **Kali Linux** | Testing environment |

## Project Structure

```
.
├── README.md
├── 01-port-scanning.md
├── 02-directory-enumeration.md
├── 03-traffic-analysis.md
└── mitigations.md
```

## Summary of Findings

| Phase | Key Result |
|---|---|
| Port Scan | 4 open ports identified: FTP (21), HTTP (80, Nginx 1.19.0), RTSP (554), PPTP (1723) |
| Directory Enumeration | Several exposed paths found, including `/admin`, `/cgi-bin`, `/CVS`, `/vendor`, `/secured` |
| Traffic Analysis | Login form submitted credentials over unencrypted HTTP, visible in plaintext via packet capture |

Full write-ups with commands, screenshots-equivalent output, and analysis are in the linked files above. A consolidated set of remediation steps is in [`mitigations.md`](./mitigations.md).

## Key Takeaway

Even a "beginner" recon pass against a deliberately vulnerable app surfaces real, common issues: unnecessary open services, forgotten legacy directories (like leftover `/CVS` metadata), and cleartext credential transmission. These are exactly the class of low-hanging findings that show up in real-world assessments — which is why this kind of practice target is useful for building the habit of checking for them systematically.

## Disclaimer

This repository is for educational purposes only. All testing was conducted against `testphp.vulnweb.com`, a target explicitly made available by Acunetix for this kind of practice. Do not run these techniques against any system without explicit written authorization.
