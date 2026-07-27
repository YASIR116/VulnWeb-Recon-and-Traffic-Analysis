# 02 — Directory Enumeration with Gobuster & Dirb

**Target:** `testphp.vulnweb.com`
**Severity of exposure:** Low–Medium (CVSS ~3.0–4.0)
**Goal:** Identify hidden, unlinked, or forgotten directories/files that aren't referenced anywhere in the visible site but are still reachable — a common source of accidental data exposure.

## Commands Used

```bash
# Gobuster
gobuster dir -u http://testphp.vulnweb.com/ -w /usr/share/wordlists/dirb/common.txt

# Dirb (cross-check with a second tool)
dirb http://testphp.vulnweb.com/
dirb http://testphp.vulnweb.com/ /usr/share/wordlists/dirb/common.txt
```

Running two different tools against the same wordlist-driven approach helps confirm results aren't tool-specific false positives/negatives.

## Results

| Path | Likely Purpose | Risk if Exposed |
|---|---|---|
| `/admin` | Admin panel | Unauthorized access, brute-forceable login |
| `/cgi-bin` | Legacy server-side scripts | Possible RCE via outdated CGI scripts |
| `/crossdomain.xml` | Flash cross-domain policy | Misconfiguration could allow unintended cross-origin access |
| `/CVS`, `/CVS/Entries`, `/CVS/Repository`, `/CVS/Root` | Leftover CVS version-control metadata | Source history/config leakage |
| `/favicon.ico` | Site icon | Can be fingerprinted to identify CMS/framework |
| `/images`, `/pictures` | Static asset directories | Low risk directly, but can be abused to host malicious files if upload controls are weak |
| `/index.php` | App entry point | Could expose debug output if misconfigured |
| `/secured` | Presumably restricted content | High risk if access control isn't actually enforced |
| `/vendor` | Third-party dependency directory (Laravel/Symfony-style) | Exposes framework/library versions, enabling targeted exploits |

## Analysis

The most notable finding is the leftover `/CVS` directory — version-control metadata that has no reason to be publicly reachable and can reveal information about the codebase's history. Similarly, an exposed `/vendor` directory can let an attacker fingerprint exact dependency versions and cross-reference them against known CVEs, effectively giving them a shortcut to vulnerability research.

`/admin` and `/secured` are the highest-priority paths to verify — the scan only confirms they *exist and respond*, not that they're properly access-controlled, which would need to be checked separately (e.g., confirming they enforce authentication rather than security-through-obscurity).

## Recommendations

- Remove legacy version-control artifacts (`/CVS`) from production entirely.
- Restrict `/admin` and `/secured` behind proper authentication + IP allow-listing, not just an unlisted URL.
- Disable directory listing site-wide.
- Regularly audit and update dependencies referenced under `/vendor`.
- Monitor for automated enumeration traffic (repeated 403/404 patterns from a single source) at the WAF/reverse-proxy layer.
