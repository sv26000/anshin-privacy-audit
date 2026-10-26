# ADR-001: 100% On-Device Inference via Local Qwen 2.5 on Apple Silicon

- **Status**: Accepted
- **Deciders**: Anshin Core Architecture Team
- **Date**: 2024-10-15
- **Technical Context**: Anshin macOS Application (Build 0.1.53+)
- **Keywords**: On-device AI, Apple Silicon, Local LLM, Qwen 2.5, Ollama, Zero Cloud, Air-gap

---

## Context and Problem Statement

Mental health and wellness applications deal with the most intimate personal data a human can generate: emotional vulnerabilities, depressive thoughts, cognitive distortions, interpersonal relationship anxieties, and trauma narratives.

Virtually all contemporary AI therapy and journaling applications (such as Wysa, Woebot, Rosebud AI, and ChatGPT) operate by transmitting user text across the public internet to hosted cloud endpoints (e.g., OpenAI API, Anthropic Claude API, or proprietary cloud servers). This introduces unavoidable security, ethical, and privacy vulnerabilities:
1. **Third-party data transit**: Packets traverse transit providers, ISPs, and CDNs.
2. **Subpoena & data discovery risks**: Cloud-stored logs and prompts can be legally compelled from cloud providers without user knowledge.
3. **Data breach liability**: Centralized cloud databases represent high-value honeypots for malicious actors.
4. **Latency and connectivity reliance**: Users cannot access emotional support in airplane mode, remote areas, or during internet disruptions.

We needed an inference architecture that provides high-empathy, clinically sound language understanding while making data exfiltration physically impossible.

---

## Considered Alternatives

1. **Cloud LLM APIs (OpenAI GPT-4o / Anthropic Claude 3.5 Sonnet)**
   - *Pros*: Maximum parameter size, zero local RAM consumption, trivial implementation.
   - *Cons*: Total violation of user privacy; requires ongoing recurring API fees; requires internet connection; prompt leakage risks.
2. **Hybrid Cloud / Local Anonymization Proxy**
   - *Pros*: Scrub PII before sending to cloud.
   - *Cons*: "Anonymization" of psychological text is fundamentally flawed—the emotional narrative *is* the sensitive data.
3. **Dedicated On-Device Inference via Apple Silicon & Local LLM (Qwen 2.5 via Ollama)**
   - *Pros*: True zero-trust air-gap; zero network traffic; sub-second local latency on Apple Neural Engine / Unified Memory; works 100% offline; zero API costs.
   - *Cons*: Requires initial one-time model download (~2–8 GB); restricted to Apple Silicon (M1/M2/M3/M4) hardware.

---

## Decision Outcome

**Chosen Alternative: Alternative 3 — 100% On-Device Inference using Qwen 2.5 via Ollama on Apple Silicon.**

Anshin binds exclusively to the local loopback interface (`http://127.0.0.1:11434`). Once the open-source Qwen 2.5 model is downloaded during first-run setup, no further network communication is ever attempted. All token generation, context embedding, and prompt evaluation occur inside the user's unified memory.

### Positive Consequences
- **Architectural Zero-Trust**: Privacy is guaranteed by the laws of networking rather than corporate privacy policy paperwork. If no packets leave the machine, data cannot be intercepted.
- **Air-Gapped Operation**: Users can engage with CBT thought records and journal reflections in airplane mode with Wi-Fi switched off.
- **Predictable Latency**: Local token generation streams at 30–60 tokens/sec on Apple Silicon without server queueing or cloud throttling.
- **Zero Compute CO2 / Cloud Cost**: Processing runs on the user's existing hardware, eliminating server maintenance expenses and enabling a sustainable one-time purchase business model.

### Trade-offs and Mitigations
- **Storage Requirement**: Requires 2–8 GB disk space for the local model weights. Mitigated by automated hardware tier selection (e.g., 3B, 7B, or 14B depending on unified RAM).
- **Platform Constraint**: Requires Apple Silicon (M-series Mac). Mitigated by clear system requirement disclosures in the Mac App Store.
