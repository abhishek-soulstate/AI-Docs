Here are the 6 prompts to run before shipping your app to production — just paste these into Claude one by one:

1. Rate limiting & abuse prevention
Add rate limiting appropriate to each endpoint/action type: strict limits on authentication routes (login, signup, password reset, token refresh), moderate limits on public-facing endpoints, and looser limits on authenticated user actions. Use a combination of per-IP and per-account/per-key limits with exponential backoff rather than hard lockouts. Make all thresholds configurable, not hardcoded. If the product has no traditional "endpoints" (e.g. a CLI or desktop app), apply the same logic to any network calls, license checks, or external API usage it makes.

2. Input validation
Validate every input — from users, files, API responses, config files, URL params, or IPC messages — against a strict schema (type, length, format, allowed values) and reject anything that doesn't match. Don't rely on sanitization/escaping alone as the primary defense. This applies to any trust boundary, not just HTTP requests: command-line args, message queues, webhooks, and inter-process communication all count.

3. Authentication & access control
Verify that every action checks authorization on the server/backend side, not just in the UI — assume any client (browser, mobile app, CLI) can be bypassed or tampered with. Check for broken object-level authorization (users accessing other users' data by changing an ID) and privilege escalation paths. Confirm sessions/tokens expire appropriately, use secure storage, and are invalidated on logout or password change.

4. Secrets & sensitive data exposure
Scan the complete codebase for hardcoded API keys, tokens, passwords, or credentials. Confirm secrets are loaded via environment variables or a secrets manager, and verify nothing sensitive is shipped into client-side bundles, mobile app binaries, git history, or logs. Include config files, Docker images, and CI/CD pipeline definitions in the scan, not just source code.

5. Dependency & supply chain vulnerabilities
Run a dependency audit across the project (including transitive dependencies). Identify packages with known vulnerabilities, list their severity, and update or replace them where safe. Also flag any dependencies pulled from untrusted sources, unpinned versions, or postinstall scripts that could execute arbitrary code.

6. Error handling & information leakage
Review all error handling. Ensure users never see stack traces, internal file paths, database errors, or environment details — return generic messages instead, while logging full details server-side for debugging. Apply this to API responses, crash reports, and any client-facing logs (browser console, mobile crash logs) too.

7. File & data handling safety
If the app accepts file uploads, imports, or attachments: validate file type, size, and actual content (not just extension or MIME header), store files outside the web root or in isolated/sandboxed storage, and ensure uploaded files can never be executed as code. If the app processes external data formats (CSV, XML, images, PDFs), check for parser-level vulnerabilities like XXE or zip bombs.

8. Transport & data-at-rest security
Confirm all network traffic uses TLS/HTTPS (including internal service-to-service calls), sensitive data is encrypted at rest where required, and security headers (CSP, HSTS, X-Frame-Options, CORS policy) are correctly configured for the platform. For mobile/desktop apps, confirm certificate pinning or equivalent where appropriate.

9. Logging & monitoring
Verify security-relevant events (failed logins, permission denials, rate-limit hits, admin actions) are logged with enough detail to investigate an incident, without logging sensitive data itself (passwords, tokens, full card numbers). Confirm there's a way to detect anomalous activity, even if it's just alerting on log patterns.
