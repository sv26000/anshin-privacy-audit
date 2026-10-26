# Anshin Formal Threat Model & Attack Surface Analysis

- **Version**: 1.0
- **Evaluation Framework**: STRIDE (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege)
- **Target**: Anshin for macOS (On-Device Architecture)

---

## Executive Summary

Unlike conventional SaaS mental health platforms that transmit emotional disclosures to multi-tenant cloud servers, Anshin completely eliminates the **server-side attack surface**. Because Anshin maintains **no servers, no user accounts, and no cloud databases**, an attacker cannot breach what does not exist.

---

## 1. STRIDE Threat Analysis Matrix

| Threat Category | Conventional Cloud Mental Health SaaS (Wysa, Woebot) | Anshin On-Device Architecture | Mitigation in Anshin |
| :--- | :--- | :--- | :--- |
| **Spoofing** | Attacker impersonates user via credential stuffing, stolen session tokens, or password reset flaws. | **Not Applicable**. No user accounts, passwords, or authentication servers exist. | Zero account system. Authentication is tied strictly to local Mac user profile. |
| **Tampering** | Man-in-the-Middle (MITM) intercepts HTTP traffic between client and cloud API. | **Mitigated**. All IPC occurs over loopback (`127.0.0.1:11434`). Traffic never touches network hardware. | Strict CSP denies non-loopback connections. Packets cannot be intercepted over Wi-Fi. |
| **Repudiation** | Server logs record user IP, timestamps, and queries; logs can be leaked or audited. | **Mitigated**. Zero server logs. Anshin generates zero diagnostic telemetry. | No server telemetry. Client retains 100% data custody. |
| **Information Disclosure** | Cloud database breach (e.g. S3 misconfiguration, SQLi, insider threat) exposes patient data. | **Eliminated**. There is no central database. User data resides solely on the customer's SSD. | Local SQLite database encrypted via AES-256-GCM + Argon2id KDF inside macOS Sandbox. |
| **Denial of Service** | Cloud provider outages, DDoS attacks, or API rate limits disable the mental health service. | **Eliminated**. Software executes 100% locally on Apple Silicon. | Runs completely offline in airplane mode. Zero uptime dependency. |
| **Elevation of Privilege** | Compromised microservice allows tenant-to-tenant data lateral movement on AWS/GCP. | **Mitigated**. Anshin operates within the macOS App Sandbox with minimal entitlements. | Sandboxed inside `~/Library/Containers/com.anshin.app/Data/`. Kernel rejects cross-app reads. |

---

## 2. Specific Threat Scenarios & Defenses

### Scenario A: Government / Legal Subpoena
- **Threat**: Law enforcement, civil litigants, or foreign entities issue a subpoena or warrant demanding all conversations, mood entries, and thoughts belonging to a specific individual.
- **Cloud SaaS Response**: The cloud provider (e.g., Wysa, Woebot, OpenAI) must comply by extracting stored database records or server backups.
- **Anshin Response**: **Cryptographic Immunity**. Anshin has no servers, no accounts, and no customer database. We cannot comply with a request to produce data we never collected, never received, and cannot access.

### Scenario B: Cloud Infrastructure & Supply Chain Compromise
- **Threat**: An attacker breaches an analytics SDK (like Mixpanel or PostHog) or an LLM provider (OpenAI API key compromise) to intercept data streams.
- **Anshin Defense**: Anshin includes **zero external SDKs** and communicates with **zero third-party APIs**. Even if a major cloud provider or analytics vendor suffers a total breach, Anshin users are completely isolated and unaffected.

### Scenario C: Physical Theft of User's Mac
- **Threat**: The user's physical laptop is stolen while unlocked or locked.
- **Anshin Defense**:
  1. Operating-system level FileVault full-disk encryption (AES-XTS).
  2. Application-level SQLite encryption (AES-256-GCM) with key stretching via Argon2id.
  3. Memory wiping of transient buffers on application termination.

### Scenario D: Local Untrusted Application / Spyware
- **Threat**: User installs an untrusted third-party application on macOS that attempts to read other applications' data.
- **Anshin Defense**: Enforced by macOS App Sandbox (`com.apple.security.app-sandbox`). Under macOS kernel security architecture, sandboxed containers can only be accessed by the owning application binary matching the code signature.
