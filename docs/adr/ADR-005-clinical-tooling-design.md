# ADR-005: Evidence-Based Clinical Frameworks vs. Unconstrained Chatbot Interaction

- **Status**: Accepted
- **Deciders**: Anshin Clinical Psychology & Engineering Working Group
- **Date**: 2024-11-10
- **Technical Context**: Anshin Interactive Architecture & Prompt Systems
- **Keywords**: Cognitive Behavioral Therapy (CBT), Acceptance and Commitment Therapy (ACT), Clinical Protocol, Prompt Engineering, Hallucination Prevention

---

## Context and Problem Statement

Many contemporary AI chatbots positioned for mental health allow freeform, unconstrained open chat. Research in computational psychology and clinical safety shows that unconstrained LLM conversation poses notable risks:
1. **Sycophancy**: LLMs tend to agree with negative self-schemas (e.g., if a user states "I'm a failure", an unguided model may inadvertently validate or amplify the cognitive distortion).
2. **Endless ruminative looping**: Open chat allows anxious or depressed users to ruminate for hours without therapeutic progress or actionable reframing.
3. **Medical hallucination**: Chatbots frequently generate unauthorized medical advice or simulate human therapist qualifications they do not possess.

We required an interaction paradigm that grounds the on-device AI in peer-reviewed clinical methodologies while maintaining psychological safety.

---

## Decision Outcome

**Chosen Policy: Grounding LLM Interaction in Structured, Peer-Reviewed Clinical Frameworks.**

Instead of acting as a free-floating conversational partner, Anshin acts as an interactive facilitator for proven, structured modalities:

### 1. Cognitive Behavioral Therapy (CBT) Frameworks
- **5-Column Thought Records** (*Beck, 1979*): Situation, Automatic Thoughts, Underlying Emotions, Objective Evidence For/Against, Balanced Alternative Thought.
- **Categorization of 10 Cognitive Distortions** (*Burns, 1980*): Identifying All-or-Nothing Thinking, Overgeneralization, Mental Filter, Disqualifying the Positive, Jumping to Conclusions (Mind Reading / Fortune Telling), Magnification/Catastrophizing, Emotional Reasoning, "Should" Statements, Labeling, Personalization.

### 2. Acceptance and Commitment Therapy (ACT) Frameworks
- **Values Compass** (*Hayes et al., 1999*): Aligning daily behavioral choices with core personal values rather than avoidance-driven impulses.
- **Cognitive Defusion**: Acknowledging thoughts as mental events rather than literal truths.

### 3. Exposure and Response Prevention (ERP)
- **Graded Exposure Ladder**: Structuring hierarchy of avoided situations to build distress tolerance step-by-step.

### 4. Expressive Writing & Boundary Tools
- **Unsent Letters** (*Pennebaker, 1997*): Processing unprocessed interpersonal conflict safely with zero risk of transmission.

### Technical Guardrails & Prompt Containment
- The local Qwen 2.5 system prompt instructs the model to guide the user *through* the tool's structure rather than generating unprompted advice.
- Strict Crisis Protocol: If crisis or self-harm keywords are detected, the UI immediately surfaces official crisis intervention resources (such as 988 Suicide & Crisis Lifeline in the US) without relying on probabilistic model generation.
- Clear Boundary Definition: Anshin explicitly states in UI and documentation that it is an introspective wellness companion, not a licensed medical provider or crisis response service.
