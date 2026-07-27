# Consolidated Mitigations

A single reference summarizing remediation steps across all three phases of this assessment.

## Network / Service Exposure
- Close or firewall off unused services (RTSP, PPTP) unless explicitly required.
- Replace FTP with SFTP or FTPS.
- Replace PPTP-based VPN with a modern protocol (WireGuard / OpenVPN + IPSec).
- Keep the web server (Nginx or equivalent) patched and version-obscured where practical.

## Application / Directory Exposure
- Remove legacy artifacts (e.g., `/CVS` version-control metadata) from production entirely.
- Enforce real authentication + IP restrictions on `/admin` and any `/secured` paths — don't rely on unlisted URLs.
- Disable directory listing.
- Keep third-party dependencies (`/vendor`) patched and avoid exposing dependency manifests publicly.
- Rate-limit and monitor for automated scanning/enumeration patterns.

## Transport Security
- Enforce HTTPS across the entire site, especially authentication endpoints.
- Enable HSTS to prevent protocol downgrade.
- Use secure, `HttpOnly`, `SameSite` cookies for session tokens.
- Add multi-factor authentication to reduce the impact of any credential leak.

## Process
- Re-run this kind of recon pass periodically (or on a CI schedule) so configuration drift — like a proxy silently falling back to HTTP — gets caught early.
- Treat findings like these as a checklist for any new project: closed unused ports, no legacy directories in prod, TLS enforced everywhere.
