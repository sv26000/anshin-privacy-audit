# Security Policy

## Reporting Security Issues

Anshin is dedicated to verifiable architectural zero-trust. If you believe you have discovered a vulnerability, unintended network call, or cryptographic weakness in the Anshin macOS app or this audit repository, please report it privately.

- **Security Contact**: `support@anshinwellness.pro`
- **Response Target**: Within 48 hours.

Please do not open public GitHub issues for undisclosed security vulnerabilities. We will coordinate remediation and acknowledge your contribution in our audit changelog.

---

## Scope

### In Scope
- Any outbound network transmission initiated by Anshin outside of the documented initial model download.
- Circumvention of the hard-coded Content Security Policy (`connect-src 'self' ipc: http://ipc.localhost http://localhost:11434 http://127.0.0.1:11434`).
- Flaws in the local AES-256-GCM / Argon2id storage implementation.
- Bypass of macOS App Sandbox container isolation (`~/Library/Containers/com.anshin.app/Data/`).

### Out of Scope
- Attacks requiring root / kernel compromise of the host Mac.
- Reports merely restating that Anshin lacks analytics or cloud backup (this is deliberate architectural design).
- Denial of service on the local machine caused by terminating local processes.
