# AI-Assisted Credit Underwriting Governance Workflow

> **AI should not approve or reject a customer.**
> AI should help underwriters understand risk faster — with evidence, controls, and human accountability.

---

## What This Is

A **governance-first design artifact** demonstrating how Generative AI can responsibly support credit underwriting decisions in financial services.

This project is not an automated credit decisioning system.
This project is not a machine learning model.
This project is not a deployable banking system.

It is a structured design that shows **how AI and humans should work together** in a regulated underwriting environment — with controls, transparency, and accountability built in from the start.

---

## Why This Matters

Credit underwriting is not just a prediction problem. It is a governance problem.

The risk in AI-assisted underwriting is not only supporting a poor underwriting outcome — it includes:

- **Privacy risk** — processing sensitive financial and personal data
- **Fairness risk** — embedding bias in AI-assisted risk signals
- **Explainability risk** — inability to justify a lending decision
- **Accountability risk** — unclear ownership when AI influences outcomes
- **Audit risk** — no traceable record of how a decision was reached

This artifact is designed to address these risks through workflow controls.

---

## What This Project Demonstrates

| Capability | Description |
|---|---|
| AI-assisted risk summarization | LLM summarizes applicant risk signals for underwriter review |
| Direct identifier removal before LLM processing | Designed to prevent direct personal identifiers from entering the language model |
| Evidence alignment check | A secondary review step checks whether the AI summary is grounded in input evidence — flagging unsupported claims before the memo reaches the underwriter |
| Output guardrail | A guardrail scan checks for personal data exposure, discriminatory language, and approval or rejection language before any memo is issued |
| Human-in-the-loop final decision | Underwriter makes all final credit decisions — AI does not |
| Audit trail | AI outputs are designed to be logged with reviewer ID, timestamp, and rationale |
| Governance flag-based review prioritization | Cases with complex risk signals or unresolved governance flags are surfaced for senior human review |

---

## Project Structure

```
ai-assisted-credit-underwriting-governance/
├── README.md                                        ← You are here
├── 01_architecture/
│   └── 01_underwriting_governance_workflow.md       ← Process flow & design rationale
├── 02_sample_credit_memo/
│   └── 02_sample_ai_credit_memo.md                  ← Example AI-assisted underwriter decision-support memo
├── 03_governance_controls/
│   └── 03_responsible_ai_control_mapping.md         ← Risk & control framework
├── 04_data_reference/
│   └── 04_dataset_mapping.md                        ← How public data maps to workflow design
└── 05_disclaimer.md                                 ← Scope, limitations, and responsible use
```

---

## Data Reference

This artifact uses **[Home Credit Default Risk](https://www.kaggle.com/c/home-credit-default-risk/data)** (Kaggle) as a public reference dataset to ground the workflow design in realistic applicant data fields.

The broader dataset provides context for applicant financial profile, credit exposure, and repayment capacity signals. This artifact currently maps selected application-level fields for governance workflow design.

**No model is trained. No customer-level records from the dataset are included in this repository. All applicant examples are synthetic.**

See `04_data_reference/04_dataset_mapping.md` for field-to-workflow mapping.

---

## Governance Framework

This design is informed by:

- **NIST AI RMF** (GOVERN / MAP / MEASURE / MANAGE)
- **OWASP LLM Top 10** — particularly prompt injection, sensitive information disclosure, hallucination, and overreliance risks
- **IAPP AIGP concepts** — human oversight, accountability, transparency, contestability, and lifecycle governance
- **Fair lending principles** — explainability and non-discrimination in AI-assisted decision support
- **BOT / FSI regulatory context** — human accountability in credit decisions

---

## Key Design Principles

```
1. AI summarizes — humans decide
2. Direct identifiers are designed to be removed before LLM processing
3. Every AI output is designed to be checkable and auditable
4. High-risk cases should escalate to human review
5. The system cannot approve or reject — only assist
```

---

## About This Project

Built by **Banlang Niyomsak (Bomb)**
AI Strategy & Governance | FSI Transformation | Enterprise AI Adoption

This artifact is part of a governance portfolio demonstrating responsible AI design thinking for financial services environments — connecting AI strategy, practical implementation, and regulatory accountability.

- LinkedIn: [linkedin.com/in/banlang](https://linkedin.com/in/banlang)
- Supply Chain Agent (previous project): [github.com/banlang/supply-chain-agent](https://github.com/banlang/supply-chain-agent)

---

*See `05_disclaimer.md` for full scope limitations and responsible use statement.*
