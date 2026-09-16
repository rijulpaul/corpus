# API Security
## SQL Injection (SQLi)
- **What it is**: Attacker injects malicious SQL via input fields or query params.
- **Impact**: Steal/modify/delete data, escalate privileges.
- **Spot it**: Inputs concatenated directly into SQL strings.

- **How to avoid**:
    - Always use parameterized queries / prepared statements (?, $1).
    - Use ORM safely (don’t pass raw strings).
    - Give lowest possible privilege to DB user.
    - Always preproces and filter the input before passing into database.

## Cross-Site Scripting (XSS)
- **What it is**: Attacker injects malicious JavaScript into your site.
- **Impact**: Steal cookies, session tokens, do actions as victim.
- **Spot it**: User input rendered in HTML without escaping.

- **How to avoid**:
    - Escape output for the correct context (HTML, JS, URL).
    - Use frameworks with auto-escaping (React, Angular, Vue).
    - Content Security Policy (CSP) to limit what scripts can run.

## Cross-Site Request Forgery (CSRF)
- **What it is**: Attacker tricks logged-in user’s browser into making authenticated requests.
- **Impact**: Perform state-changing actions (transfer money, change password).
- **Spot it**: Any state-changing request that only relies on cookies.
- **How to avoid**:
    - Use SameSite=Lax/Strict cookies.
    - Require CSRF tokens in forms/requests.
    - Double-check with re-authentication for sensitive actions.

## Broken Authentication & Session Management
- **What it is**: Weak login/session handling (e.g., predictable tokens, no expiration).
- **Impact**: Account takeover.
- **Spot it**:
    - Sessions never expire.
    - Tokens not rotated on login/logout.
    - Passwords stored in plain text.
- **How to avoid**:
    - Store passwords with bcrypt/argon2, never plain text.
    - Use JWTs or opaque tokens with short lifetimes + refresh.
    - Set cookies as HttpOnly, Secure, SameSite.
    - Invalidate sessions on logout.

## Insecure Direct Object References (IDOR)
- **What it is**: Attacker changes an ID in a request to access data they shouldn’t.
- **Impact**: Data leakage, account takeover.
- **Spot it**: API relies only on IDs, no access checks.
- **How to avoid**:
    - Always enforce authorization checks server-side.
    - Never trust just the ID in the request.

## Insecure Deserialization
- **What it is**: Attacker sends malicious serialized objects (JSON, JWT, XML) → server executes unintended code.
- **Impact**: RCE (Remote Code Execution), privilege escalation.
- **Spot it**: Accepting serialized data without validation.
- **How to avoid**:
    - Don’t deserialize untrusted data blindly.
    - Use safe serializers.
    - Validate integrity (e.g., JWT signatures).

## Security Misconfiguration
- **What it is**: Default credentials, verbose error messages, debug mode in production.
- **Impact**: Easy entry point for attackers.
- **Spot it**:
    - “It works!” default pages, stack traces in prod.
    - Open ports/services not needed.
- **How to avoid**:
    - Disable debug/prod differences.
    - Minimal privileges.
    - Regular patching/updates.
    - Harden configs (firewall, headers).

## Sensitive Data Exposure
- **What it is**: Exposing sensitive data via weak crypto or no crypto.
- **Impact**: Credential theft, GDPR/PCI violations.
- **Spot it**:
    - No HTTPS.
    - Storing passwords/keys in plain text.
    - Leaking tokens in URLs.
- **How to avoid**:
    - Always use HTTPS (TLS 1.2+).
    - Encrypt sensitive data at rest (AES-256).
    - Don’t log secrets.
    - Use strong key management (rotate, don’t hardcode).

## Server-Side Request Forgery (SSRF)
- **What it is**: Attacker tricks your server into making HTTP requests to internal services.
- **Impact**: Pivot into internal network, metadata services (AWS/GCP).
- **Spot it**: Any feature fetching URLs (webhooks, previews).
- **How to avoid**:
    - Validate/whitelist URLs.
    - Block requests to internal IP ranges (169.254.169.254, 127.0.0.1).
    - Use network segmentation.

## Insufficient Logging & Monitoring
- **What it is**: Attacks go undetected because no logs/alerts.
- **Impact**: Breaches discovered months later.
- **How to avoid**:
    - Centralize logs (ELK, Splunk, CloudWatch).
    - Log security events (failed logins, permission errors).
    - Set up alerts and monitoring.

# Security Checklist (always do this)
- Use HTTPS everywhere.
- Sanitize/escape all inputs and outputs.
- Use parameterized queries for DB.
- Protect sessions and cookies (HttpOnly, Secure, SameSite).
- Validate auth & authorization server-side.
- Rotate and store secrets/keys securely.
- Keep software patched.
- Set security headers: CSP, X-Frame-Options, X-Content-Type-Options, HSTS.
- Run regular penetration testing or at least OWASP ZAP/Burp scans.
