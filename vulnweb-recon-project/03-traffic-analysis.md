# 03 — Login Traffic Analysis with Wireshark

**Target:** `testphp.vulnweb.com`
**Severity of exposure:** Medium (CVSS ~4.0)
**Goal:** Determine whether login credentials are transmitted securely, by capturing traffic during an actual login attempt and inspecting it for plaintext data.

## Methodology

1. Start a Wireshark capture on the active interface.
2. Apply a display filter to isolate relevant traffic:
   ```
   http.request.method == POST
   ```
3. Log in via the site's login form while the capture is running.
4. Inspect the captured POST request for form data.
5. Check whether the username/password fields are readable in plaintext.

## Result

The login form submitted its POST request over plain HTTP rather than HTTPS. Inspecting the captured packet's form-encoded body showed the submitted username and password fields in **plaintext**, fully readable to anyone in a position to observe the traffic (e.g., on a shared/unsecured network or via a machine-in-the-middle position).

## Analysis

Because the connection isn't encrypted with TLS, this isn't a subtle flaw — any packet sniffer on the same network segment can trivially recover credentials. This is functionally equivalent to sending a password by postcard rather than in a sealed envelope. On a real production system, this would be a critical finding; here it's a clear, hands-on demonstration of *why* HTTPS-everywhere is treated as a non-negotiable baseline rather than a nice-to-have.

## Recommendations

- **Enforce HTTPS site-wide**, including the login endpoint — no HTTP fallback for authentication flows.
- Add **HSTS** so browsers refuse to downgrade to HTTP on repeat visits.
- Consider **MFA** so a leaked password alone isn't sufficient for account takeover.
- Avoid transmitting session identifiers or credentials in URL parameters or unencrypted cookies.
- Periodically re-test authentication flows with a packet capture as part of a regression checklist — encryption regressions (e.g., a misconfigured reverse proxy dropping back to HTTP) are easy to miss otherwise.
