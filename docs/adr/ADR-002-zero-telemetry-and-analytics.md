# ADR-002: Zero Telemetry, Zero Analytics, and Zero Crash Reporting Over the Wire

- **Status**: Accepted
- **Deciders**: Anshin Core Architecture Team
- **Date**: 2024-10-18
- **Technical Context**: Anshin macOS Application (Build 0.1.53+)
- **Keywords**: Zero Telemetry, No Analytics, Mixpanel, PostHog, Google Analytics, Privacy Nutrition Label

---

## Context and Problem Statement

Modern software development standardizes embedding diagnostic telemetry SDKs:
- Product analytics: PostHog, Mixpanel, Amplitude, Google Analytics.
- Error monitoring: Sentry, Bugsnag, Datadog.
- Marketing pixels and session replay: FullStory, LogRocket, Meta Pixel.

In health and wellness software, telemetry SDKs frequently cause catastrophic privacy breaches:
1. **Metadata leakage**: Session timestamps, screen duration, tool clicks (e.g., clicking "Severe Depression Thought Record"), and feature engagement reveal clinical status.
2. **Crash dump leakage**: When an uncaught exception occurs, crash reporters often serialize runtime state—which may include raw user chat strings and journal excerpts—and transmit them to third-party dashboards.
3. **Fingerprinting**: Hardware IDs, IP addresses, MAC addresses, and OS build numbers are aggregated into commercial identity graphs.

We must answer: How does Anshin maintain code quality without collecting telemetry from users?

---

## Considered Alternatives

1. **"Anonymized" Telemetry SDKs (PostHog self-hosted or Mixpanel with IP hashing)**
   - *Pros*: Provides visibility into feature adoption and drop-off funnels.
   - *Cons*: Even hashed IDs and event names ("clicked_panic_button") reveal sensitive user state; introduces network endpoints that violate the Content Security Policy.
2. **Opt-in Crash Reporting (Sentry with scrubbing regexes)**
   - *Pros*: Immediate stack traces when bugs occur.
   - *Cons*: Regex scrubbing is non-deterministic; one edge-case string interpolation can leak user text; requires outbound HTTP endpoints.
3. **Absolute Zero Telemetry, Zero Analytics, Zero SDKs**
   - *Pros*: Zero risk of data leakage; hard-coded CSP remains completely sealed; qualifies for the Apple App Store's cleanest tier: "Data Not Collected".
   - *Cons*: Feature design must rely on direct voluntary customer emails and rigorous local integration tests rather than surveillance metrics.

---

## Decision Outcome

**Chosen Alternative: Alternative 3 — Absolute Zero Telemetry, Zero Analytics, Zero SDKs.**

Anshin does not include a single third-party analytics library or crash tracker:
- No Google Analytics.
- No Mixpanel.
- No PostHog.
- No Sentry.
- No Segment / RudderStack.
- No advertising identifiers (IDFA/GAID) or device fingerprinting.

The macOS application binary is compiled with a Content Security Policy that strictly rejects all outbound network calls:
```
connect-src 'self' ipc: http://ipc.localhost http://localhost:11434 http://127.0.0.1:11434
```

### Apple App Store Verification
Under Apple's mandatory Privacy Nutrition Label framework, Apple independently audits developer declarations against binary symbols. Because Anshin links zero telemetry symbols, Apple certifies Anshin with the official label: **"Data Not Collected"**.

### Bug Reporting Alternative
If a user encounters an anomaly, they can voluntarily email `support@anshinwellness.pro` with their macOS version and a description of the issue. No automated diagnostic payload is ever generated or transmitted silently in the background.
