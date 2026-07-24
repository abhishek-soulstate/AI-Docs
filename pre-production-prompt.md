# Pre-Production Security Checklist

A reusable set of prompts to run (in Claude, or any AI coding assistant) before shipping any app to production — web, mobile, API, CLI, desktop, or browser extension.

**How to use:** Run each prompt one at a time, in order. Ask the assistant to show you the actual files/lines it changed, not just a description of what it reviewed. Check the box once you've verified the change yourself.

---

## 1. Rate Limiting & Abuse Prevention

- [ ] Reviewed / fixed

> Add rate limiting appropriate to each endpoint/action type: strict limits on authentication routes (login, signup, password reset, token refresh), moderate limits on public-facing endpoints, and looser limits on authenticated user actions. Use a combination of per-IP and per-account/per-key limits with exponential backoff rather than hard lockouts. Make all thresholds configurable, not hardcoded. If the product has no traditional "endpoints" (e.g. a CLI or desktop app), apply the same logic to any network calls, license checks, or external API usage it makes.

---

## 2. Input Validation

- [ ] Reviewed / fixed

> Validate every input — from users, files, API responses, config files, URL params, or IPC messages — against a strict schema (type, length, format, allowed values) and reject anything that doesn't match. Don't rely on sanitization/escaping alone as the primary defense. This applies to any trust boundary, not just HTTP requests: command-line args, message queues, webhooks, and inter-process communication all count.

---

## 3. Authentication & Access Control

- [ ] Reviewed / fixed

> Verify that every action checks authorization on the server/backend side, not just in the UI — assume any client (browser, mobile app, CLI) can be bypassed or tampered with. Check for broken object-level authorization (users accessing other users' data by changing an ID) and privilege escalation paths. Confirm sessions/tokens expire appropriately, use secure storage, and are invalidated on logout or password change.

---

## 4. Secrets & Sensitive Data Exposure

- [ ] Reviewed / fixed

> Scan the complete codebase for hardcoded API keys, tokens, passwords, or credentials. Confirm secrets are loaded via environment variables or a secrets manager, and verify nothing sensitive is shipped into client-side bundles, mobile app binaries, git history, or logs. Include config files, Docker images, and CI/CD pipeline definitions in the scan, not just source code.

---

## 5. Dependency & Supply Chain Vulnerabilities

- [ ] Reviewed / fixed

> Run a dependency audit across the project (including transitive dependencies). Identify packages with known vulnerabilities, list their severity, and update or replace them where safe. Also flag any dependencies pulled from untrusted sources, unpinned versions, or postinstall scripts that could execute arbitrary code.

---

## 6. Error Handling & Information Leakage

- [ ] Reviewed / fixed

> Review all error handling. Ensure users never see stack traces, internal file paths, database errors, or environment details — return generic messages instead, while logging full details server-side for debugging. Apply this to API responses, crash reports, and any client-facing logs (browser console, mobile crash logs) too.

---

## 7. File & Data Handling Safety

- [ ] Reviewed / fixed

> If the app accepts file uploads, imports, or attachments: validate file type, size, and actual content (not just extension or MIME header), store files outside the web root or in isolated/sandboxed storage, and ensure uploaded files can never be executed as code. If the app processes external data formats (CSV, XML, images, PDFs), check for parser-level vulnerabilities like XXE or zip bombs.

---

## 8. Transport & Data-at-Rest Security

- [ ] Reviewed / fixed

> Confirm all network traffic uses TLS/HTTPS (including internal service-to-service calls), sensitive data is encrypted at rest where required, and security headers (CSP, HSTS, X-Frame-Options, CORS policy) are correctly configured for the platform. For mobile/desktop apps, confirm certificate pinning or equivalent where appropriate.

---

## 9. Logging & Monitoring

- [ ] Reviewed / fixed

> Verify security-relevant events (failed logins, permission denials, rate-limit hits, admin actions) are logged with enough detail to investigate an incident, without logging sensitive data itself (passwords, tokens, full card numbers). Confirm there's a way to detect anomalous activity, even if it's just alerting on log patterns.

---

## Notes on Using This Checklist

- **Order matters.** Run items in rough order of "blast radius if broken" — auth/access control (#3) and secrets (#4) first, since those turn into full breaches fastest.
- **Ask for proof, not summaries.** A good response shows you diffs, file paths, and specific lines — not just "I reviewed this and it looks fine."
- **Re-run #5 (dependencies) periodically.** New CVEs get disclosed continuously; this isn't a one-time check.
- **This checklist is a floor, not a ceiling.** For anything handling payments, health data, or regulated data, you'll need a real security audit or pen test on top of this — an AI assistant catching common mistakes isn't the same as a professional review.
