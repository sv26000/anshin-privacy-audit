# Competitive Architecture & Privacy Comparison Matrix

A technical, evidence-backed evaluation comparing **Anshin** to conventional mental health chatbots, cloud AI journals, and general LLMs.

---

## Technical Comparison Table

| Architecture Dimension | Anshin (Mac) | Cloud Mental Health Apps (Wysa, Woebot) | Cloud AI Journals (Rosebud AI) | Traditional Journals (Day One) | General LLMs (ChatGPT) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Trust Model** | **Architectural Zero-Trust** (physically air-gapped) | **Policy-Based Trust** (legal promises on cloud data) | **Policy-Based Trust** (stored on cloud servers) | **Policy-Based Trust** (syncs to server) | **Policy-Based Trust** (stored on OpenAI servers) |
| **AI Model Execution** | **100% On-Device** (Apple Silicon Neural Engine / Qwen 2.5) | Cloud Server (AWS / proprietary cloud) | Cloud APIs (OpenAI / Anthropic APIs) | None / Server-side search | OpenAI Cloud Data Centers |
| **Outbound Network Traffic** | **Zero outbound calls** (100% loopback on `127.0.0.1`) | Continuous HTTP API calls with chat history | Continuous API calls containing reflections | Syncs entries across cloud servers | Continuous streaming prompts to cloud |
| **On-Disk Encryption** | **AES-256-GCM** with Argon2id KDF | Decryptable by provider in cloud DB | SaaS server-side managed keys | End-to-end sync encryption (cloud-hosted) | Server-side at rest; accessible to OpenAI systems |
| **User Account Required** | **Zero accounts** (no email, phone, or name) | Mandatory email or phone registration | Mandatory email or Google OAuth | Mandatory Day One account for sync | Mandatory phone & email registration |
| **Third-Party Telemetry** | **Zero SDKs** (No PostHog, Mixpanel, Sentry) | Commercial analytics & tracking SDKs | Analytics & marketing tracking | Analytics & crash monitoring | Full telemetry & prompt retention |
| **Works 100% Offline** | **Yes** (airplane mode, zero Wi-Fi required) | **No** (fails completely without internet) | **No** (AI guidance fails offline) | Text works; sync requires internet | **No** (fails without active internet) |
| **Apple Nutrition Label** | **"Data Not Collected"** (Certified by Apple) | Collects diagnostics, user IDs, usage | Collects contact info, user content | Collects diagnostics, user content | Collects identifiers, user content |
| **Pricing Model** | **$39 USD one-time purchase** (perpetual license) | $10 – $25 / month ($120 – $300 / year) | $13 – $20 / month ($156 – $240 / year) | $35 / year recurring subscription | $20 / month ($240 / year) |

---

## Architectural Zero-Trust vs. Policy-Based Trust

The defining distinction between Anshin and cloud AI mental health apps is the difference between **Architectural Zero-Trust** and **Policy-Based Trust**:

### Policy-Based Trust (The Industry Norm)
Most mental health apps operate under policy-based trust:
1. When you type: *"I feel overwhelmed and hopeless today,"* the app transmits that string across your ISP to remote servers on AWS or Google Cloud.
2. The company points to their written Privacy Policy and HIPAA / GDPR / SOC-2 compliance certificates, promising: *"We promise not to sell your data, and we encrypt it on our servers."*
3. **The Fragility**: Written promises cannot stop:
   - Zero-day database breaches or S3 bucket misconfigurations.
   - Hostile corporate acquisitions where new owners rewrite the privacy policy.
   - Government or court subpoenas compelling the cloud host to decrypt records.
   - Rogue insider employees with database administrative access.

### Architectural Zero-Trust (Anshin)
Anshin replaces paper promises with mathematical and physical reality:
1. When you type: *"I feel overwhelmed and hopeless today,"* the text never leaves your Mac's RAM and SSD.
2. Token generation occurs entirely inside Apple Silicon's Unified Memory using local weights.
3. The Content Security Policy hard-codes loopback-only communication (`127.0.0.1:11434`).
4. **The Guarantee**: Even if Anshin were subpoenaed, we possess zero keys, zero databases, and zero servers. There is no server to hack, no employee who can inspect your text, and no cloud log in existence. **If data does not exist on a server, it cannot be leaked.**
