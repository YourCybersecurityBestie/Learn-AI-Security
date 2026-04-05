# 🔐 AI Model Security Fundamentals
### A Cyber Queen Deep Dive — Model Security

> **By Venicia Solomons ([@cyber.queen_](https://www.instagram.com/cyber.queen_/))**  
> Cloud & AI Security · Microsoft
>
> Connect with me: 
> [Instagram](https://www.instagram.com/cyber.queen_/) · [LinkedIn](https://www.linkedin.com/in/veniciasolomons/) · [YouTube](https://www.youtube.com/@cyber.queen_) · [TikTok](https://www.tiktok.com/@cyber.queen_) 

---

> 💡 **You are reading this because you want to learn AI security properly.**  
> This is the foundation. Everything else builds on this.  
> Save it. Bookmark it. Come back to it.

---

## Table of Contents

- [Why This Matters](#why-this-matters)
- [What Is an AI Model?](#what-is-an-ai-model)
- [Models You Need to Know Right Now](#models-you-need-to-know-right-now)
- [The 3 Core Attack Paths](#the-3-core-attack-paths)
- [Real World Incidents](#real-world-incidents)
- [Security Controls That Actually Apply](#security-controls-that-actually-apply)
- [The Frameworks You Need to Know](#the-frameworks-you-need-to-know)
- [What Should You Do Right Now](#what-should-you-do-right-now)
- [Resources and Next Steps](#resources-and-next-steps)

---

## Why This Matters

```
73% of enterprises have hundreds of AI models in production right now.
41% reported an AI security incident by late 2024.
Most had zero AI-specific security controls in place.
```

AI is not a future threat surface. It is a present one.

Every copilot, every assistant, every automated workflow your organisation uses is built on a model. And most security teams are still treating AI security like it is someone else's problem.

It is not. It is yours.

But before you can protect something, you need to understand it. That is what this guide is for.

---

## What Is an AI Model?

Strip away the hype and a model is this:

> **A collection of billions of numerical values called weights, shaped by training data, stored in files on a server.**

That is it. No magic. Just math at massive scale.

Here is how it works at a high level:

```
TRAINING PHASE
──────────────────────────────────────────────
Raw Data ──► Data Pipeline ──► Model Training
                                     │
                              Weights are updated
                              billions of times
                                     │
                              Final Model Weights
                              (stored as files)

INFERENCE PHASE
──────────────────────────────────────────────
User Input (Prompt) ──► Model ──► Output
                          │
                    Weights are READ
                    not updated
```

### Key Terms You Need to Know

| Term | What It Means | Why It Matters for Security |
|------|--------------|----------------------------|
| **Weights** | The numerical values that define what the model knows | Stealing weights = stealing the entire model |
| **Training Data** | The data used to shape those weights | Poisoning training data corrupts the model permanently |
| **Parameters** | The count of weights in a model (billions to trillions) | More parameters = larger attack surface |
| **Fine-tuning** | Retraining a model on new specific data | Introduces supply chain risk if not controlled |
| **Inference** | When the model runs and generates output | Where most runtime attacks happen |
| **Context Window** | What the model can "see" in one interaction | Can be poisoned with malicious instructions |

---

## Models You Need to Know Right Now

These are not hypothetical. These models are running in enterprise environments today.

### Closed Source Models

These are hosted by the provider. You do not have access to the weights. Limited visibility but managed infrastructure.

| Model | Provider | Deployed Via |
|-------|----------|-------------|
| GPT-5.4 | OpenAI | Azure OpenAI Service |
| Claude Opus 4.6 | Anthropic | AWS Bedrock |
| Gemini 3.1 Ultra | Google | Vertex AI |
| Grok 3 | xAI | API |

### Open Source / Open Weight Models

Weights are publicly available. Full access means full control and full risk.

| Model | Provider | How It Is Deployed |
|-------|----------|-------------------|
| Llama 4 | Meta | Self-hosted or cloud |
| Mistral Large 2 | Mistral AI | Self-hosted or API |
| DeepSeek V3 | DeepSeek | Self-hosted (open weights) |
| Nemotron 3 | NVIDIA | Edge and cloud |

### ⚠️ A Special Note on DeepSeek

DeepSeek deserves its own callout. It delivers near-frontier performance at a fraction of the cost of closed models and its weights are publicly available. That makes it attractive for organisations watching their AI spend.

It comes from China, which introduces a geopolitical risk layer that security teams may not be factoring in. If your organisation is considering DeepSeek, your risk assessment needs to include data sovereignty, supply chain trust, and regulatory exposure — not just technical performance benchmarks.

---

## The 3 Core Attack Paths

Every AI model has three fundamental attack surfaces. Understanding these is how you start thinking like an attacker.

```
┌─────────────────────────────────────────────────┐
│                   AI MODEL                       │
│                                                  │
│  ┌─────────────┐  ┌──────────┐  ┌────────────┐  │
│  │  TRAINING   │  │ WEIGHTS  │  │  OUTPUTS   │  │
│  │    DATA     │  │          │  │            │  │
│  │  Attack 1   │  │ Attack 2 │  │  Attack 3  │  │
│  └─────────────┘  └──────────┘  └────────────┘  │
└─────────────────────────────────────────────────┘
```

### Attack Path 1 — Training Data Poisoning

**What it is:** An attacker injects malicious, biased, or corrupted data into the training pipeline. The model learns from that data and its behaviour is permanently altered.

**Why it is dangerous:** The model behaves normally on regular inputs but produces incorrect, harmful, or attacker-controlled outputs when triggered. By the time you notice, the damage is baked into the weights.

**Real signal:** Think of it like corrupting the textbook before the student ever starts studying. Everything they learn is built on a compromised foundation.

---

### Attack Path 2 — Model Theft (Weight Extraction)

**What it is:** An attacker either directly steals model weight files, or reconstructs a near-identical model by sending thousands of crafted queries and analysing the responses.

**Why it is dangerous:** Stealing the weights means stealing the model entirely. The attacker can run it privately, bypass licensing, probe it for vulnerabilities offline, or use it to craft attacks against the original system.

**Real signal:** Your model is intellectual property. Losing it is equivalent to losing your source code.

---

### Attack Path 3 — Training Data Extraction via Outputs

**What it is:** An attacker queries the model repeatedly with carefully crafted inputs to reconstruct private data that was in the training set.

**Why it is dangerous:** Information that was considered private when it went into training can leak back out through model responses. This is especially critical for models trained on sensitive internal data — HR records, customer data, legal documents.

**Real signal:** What goes in does not stay in. This is a data breach that happens one response at a time.

---

## Real World Incidents

This is not theoretical. It has already happened.

---

### 🔴 ByteDance Data Poisoning — 2024

A ByteDance AI intern deliberately manipulated training data to skew an algorithm's outputs. This was an insider threat at the training data layer.

**What it tells us:** Your biggest data poisoning risk might not be an external attacker. It is the person with access to your training pipeline.

---

### 🔴 Model Extraction via APIs — 2024

Multiple cloud AI providers suffered model extraction attacks through their public APIs. Attackers sent thousands of crafted queries to reverse-engineer and clone the models without authorisation.

**What it tells us:** If your model is accessible via API with no rate limiting or anomaly detection, it is vulnerable to extraction. Access does not have to be unauthorised to be exploited.

---

### 🔴 ChatGPT Training Data Leak — 2023

Google researchers demonstrated they could pull pieces of ChatGPT's private training data by querying the model repeatedly and analysing what came back. The technique was straightforward and required no special access.

**What it tells us:** Even the most widely deployed models in the world are not immune to data extraction. If you are training models on sensitive data, the outputs are a potential leak channel.

---

### 🔴 Claude Code AI-Orchestrated Espionage — 2025

Anthropic publicly disclosed that a Chinese state-sponsored group manipulated Claude Code into attempting infiltration into approximately thirty global targets, succeeding in a small number of cases. This is the first documented large-scale cyberattack executed with minimal human intervention, with AI performing 80 to 90 percent of the campaign autonomously.

**What it tells us:** Agentic AI is a force multiplier for attackers. When AI can take actions, write exploit code, harvest credentials, and exfiltrate data on its own, the blast radius is enormous.

---

## Security Controls That Actually Apply

Good news. A lot of these map to security fundamentals you already know. The context is new, the principles are not.

### Preventive Controls

| Control | What It Does | Where It Applies |
|---------|-------------|-----------------|
| **Data Provenance and Validation** | Know where your training data came from. Audit it before it touches your model. | Training pipeline |
| **Access Controls on Model Weights** | Treat weights like source code. Role-based access, encryption at rest, full audit logs. | Model storage and deployment |
| **Model Signing and Integrity Checks** | Verify the model has not been tampered with before deployment. Critical for open source models. | Deployment pipeline |
| **Differential Privacy** | Add mathematical noise to outputs so training data cannot be reconstructed from responses. | Model outputs |
| **Supply Chain Vetting** | Audit every dataset, library, and pre-trained model before it enters your pipeline. | Training and fine-tuning |

### Detective Controls

| Control | What It Does | Where It Applies |
|---------|-------------|-----------------|
| **Output Monitoring** | Watch what your model returns. Anomalous query patterns are your early warning signal for extraction attempts. | Runtime |
| **Query Rate Limiting and Anomaly Detection** | Flag and throttle unusual querying behaviour that may indicate extraction attempts. | API layer |
| **Model Drift Detection** | Monitor model behaviour over time. Unexpected changes in outputs can indicate tampering. | Production |
| **Audit Logging** | Log all access to model weights, training pipelines, and inference endpoints. | All layers |

---

## The Frameworks You Need to Know

You do not need to memorise all of these right now. Understand what each one is for and you will know where to go when you need it.

---

### 🟣 OWASP LLM Top 10 (2025)
**Start here.**

The closest thing to the classic OWASP Top 10 but built specifically for Large Language Models. If you know the OWASP Top 10 for web security, this will feel familiar.

Covers: Prompt injection, sensitive information disclosure, supply chain vulnerabilities, data and model poisoning, insecure output handling, excessive agency, system prompt leakage, vector and embedding weaknesses, misinformation, and unbounded consumption.

🔗 [genai.owasp.org](https://genai.owasp.org)

---

### 🔵 OWASP AI Exchange
**Go deeper here.**

Awarded OWASP Flagship project status in March 2025. A comprehensive framework covering threats, controls, and best practices for all AI systems, not just LLMs. It is actively feeding into the EU AI Act and ISO/IEC 27090 standards.

🔗 [owaspai.org](https://owaspai.org)

---

### 🔴 MITRE ATLAS
**Use this for threat modelling.**

ATT&CK but built for AI. A living knowledge base of real-world adversary techniques against machine learning systems, mapped to the MITRE ATT&CK framework. This is your go-to when you need to understand how attackers actually target AI systems in the wild.

🔗 [atlas.mitre.org](https://atlas.mitre.org)

---

### 🟡 NIST AI Risk Management Framework (AI RMF)
**Use this for governance.**

Not attack-specific but the governance and enterprise risk management standard your compliance and risk teams will reference. If you are working in a regulated environment or building an AI security programme, this is your foundation.

🔗 [nist.gov/system/files/documents/2023/01/26/AI RMF 1.0.pdf](https://www.nist.gov/system/files/documents/2023/01/26/AI%20RMF%201.0.pdf)

---

## What Should You Do Right Now

Depending on where you are in your journey, your next step looks different.

---

### You are breaking into security

Start with the OWASP LLM Top 10. Read it once all the way through. Every job interview in AI security will touch something on that list. You will already be ahead of most candidates.

Then come back to this series. Post 2 covers training versus inference and why they are completely different attack surfaces.

---

### You are already working in security

Pull up one AI tool your organisation is using right now and ask yourself two questions:

1. Do we have access controls on the model weights or the API?
2. Are we monitoring model outputs for anomalous behaviour?

If you cannot answer yes to both, you have work to do. Start there.

---

### You use AI tools at work

The data you put into that model does not disappear. Ask your IT or security team one question:

**"Is this AI tool enterprise-approved and what model is it using?"**

That single question could protect you and your company. If nobody can answer it, that is the problem.

---

## Resources and Next Steps

### Essential Reading

- 📄 [OWASP Top 10 for LLM Applications 2025](https://genai.owasp.org/llm-top-10/)
- 📄 [OWASP AI Exchange](https://owaspai.org)
- 📄 [MITRE ATLAS Knowledge Base](https://atlas.mitre.org)
- 📄 [NIST AI Risk Management Framework](https://airc.nist.gov/Home)
- 📄 [Anthropic AI Espionage Case Study](https://www.anthropic.com/news/disrupting-AI-espionage)
- 📄 [Microsoft Security Blog](https://www.microsoft.com/en-us/security/blog/)

### Coming Up in This Series

| Post | Topic |
|------|-------|
| ✅ Post 1 | The Model — what it is, how it gets attacked, how to protect it |
| 🔜 Post 2 | Training vs Inference — two completely different attack surfaces |
| 🔜 Post 3 | Prompts are Inputs — where prompt injection lives |
| 🔜 Post 4 | Context Windows — what the model sees and how it gets poisoned |
| 🔜 Post 5 | Embeddings and Vector Databases — RAG pipeline attacks |
| 🔜 Post 6 | Agents and Tool Use — when AI can take actions, the blast radius explodes |
| 🔜 Post 7 | Fine-tuning and RLHF — supply chain risk at the AI layer |
| 🔜 Post 8 | APIs as the Delivery Mechanism — classic API risk with an LLM on top |

---

## Stay Connected

If this was useful, the best thing you can do is share it with someone who is learning AI security.

> 🔐 **Follow the series on Instagram:** [@cyber.queen_](https://www.instagram.com/cyber.queen_/)

> 🎙️ **Subscribe on YouTube:** [Cyber Queen Podcast](https://www.youtube.com/@cyber.queen_)  

> 💼 **Connect on LinkedIn:** [Venicia Solomons](https://www.linkedin.com/in/veniciasolomons/)  

> 🎙️ **Listen to the Podcast:** [Cyber Queen Podcast](https://www.youtube.com/@cyber.queen_)

> 🎙️ **Some Entertainment on TikTok:** [TikTok](https://www.tiktok.com/@cyber.queen_) 
---

*Built with love for the security community. No gatekeeping. Just knowledge.*

**Cyber Queen © 2026**
