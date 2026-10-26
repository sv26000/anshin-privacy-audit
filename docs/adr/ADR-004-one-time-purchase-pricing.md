# ADR-004: $39 One-Time Purchase vs. Monthly SaaS Subscription Model

- **Status**: Accepted
- **Deciders**: Anshin Product & Engineering Leadership
- **Date**: 2024-11-01
- **Technical Context**: Anshin Business & Commercial Architecture
- **Keywords**: Pricing Model, One-Time Purchase, No Subscriptions, Local Compute Economics, SaaS Contrast

---

## Context and Problem Statement

The mental health and wellness software ecosystem is overwhelmingly dominated by recurring monthly subscriptions:
- **Wysa**: ~$10–$25/month ($120–$300/year)
- **Woebot**: Institutional / B2B or recurring subscription
- **Rosebud AI**: ~$13–$20/month ($156–$240/year)
- **Day One Journal**: ~$35/year recurring
- **ChatGPT Plus**: $20/month ($240/year)

This business model is dictated by infrastructure: because cloud AI apps route every prompt through centralized GPU clusters (AWS, Azure, OpenAI API), every user interaction incurs marginal compute costs.

However, subscription models create perverse product incentives:
1. **Gamification & notification spam**: SaaS apps must artificially hook users to prevent monthly churn, even when constant screen time is detrimental to mental health.
2. **Subscription fatigue**: Users facing anxiety or depression are burdened by perpetual recurring payments.
3. **Data monetization pressure**: SaaS companies often introduce monetization layers (data aggregation, corporate partnerships, enterprise reporting) to justify high burn rates.

---

## Decision Outcome

**Chosen Policy: Perpetual License via a Single $39 USD One-Time Purchase.**

Because Anshin executes 100% of inference locally using the customer's own Apple Silicon hardware (M1/M2/M3/M4), Anshin incurs **$0.00 in per-query server infrastructure costs**.

```
+-----------------------------------+-----------------------------------+
| Cloud AI Subscriptions            | Anshin One-Time Purchase ($39)    |
+-----------------------------------+-----------------------------------+
| $120 – $300 / year                | $39 once forever                  |
| Server compute cost per message   | $0 marginal cost (uses your Mac)  |
| Monetization pressure on data     | Zero data collected to monetize   |
| Churn-reduction notifications     | Zero push spam or dark patterns   |
| Requires active internet          | Operates completely offline       |
+-----------------------------------+-----------------------------------+
```

### Strategic Alignment
1. **Incentive Alignment**: Our financial incentive is simply to build a stable, clinically helpful macOS tool that users recommend by word-of-mouth. We have zero incentive to keep you scrolling or typing endless messages.
2. **Lifetime Value for User**: A user using Anshin for 3 years saves between **$320 and $860** compared to standard cloud mental health subscriptions.
3. **Distribution Simplicity**: Sold directly through Apple's Mac App Store, providing familiar, trusted billing with Apple-backed receipt validation and family sharing options.
