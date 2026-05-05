# Responsible AI Control Mapping — AI-Assisted Credit Underwriting Governance Workflow
> **Design artifact only.** This document maps identified AI risks to governance controls within the AI-assisted credit underwriting workflow. It is not a formal compliance document, a regulatory submission, or a certified risk assessment. Controls described here are designed to reduce risk — they do not guarantee elimination of risk.

---

## Purpose

This document identifies seven AI risk categories relevant to AI-assisted credit underwriting decision support and maps each risk to the governance controls designed to address it within the workflow.

Each risk entry includes:
- **Risk Description** — what the risk is and why it matters in this context
- **Control Mechanism** — how the workflow is designed to address it
- **Where in Workflow** — which node or nodes the control is applied
- **Framework Reference** — relevant governance framework alignment

---

## Risk and Control Mapping

---

### Risk 1 — Privacy Risk

**Risk Description**
Credit underwriting requires processing sensitive personal and financial data, including income, employment history, and external credit scores. If personal identifiers or sensitive attributes enter the AI processing layer without controls, there is a risk of unnecessary exposure, re-identification, or data minimization failure.

**Control Mechanism**
Direct personal identifiers are designed to be removed or pseudonymized before the application record enters the LLM layer. Sensitive / Proxy Attributes (Group 2) are generalized — for example, date of birth is converted to an age band — before LLM processing. Field selection at the Data Assembly stage is restricted to the approved field set only, applying data minimization by design.

**Where in Workflow**

| Node | Control Applied |
|---|---|
| Node 3 — Data Assembly | Field selection restricted to approved set; Group 3 fields blocked |
| Node 4 — Identifier Removal | Direct identifiers removed or pseudonymized; sensitive attributes generalized |

**Framework Reference**

| Reference | Alignment |
|---|---|
| NIST AI RMF — GOVERN / MAP | Privacy by design; data minimization at field selection stage |
| OWASP LLM Top 10 — Sensitive Information Disclosure | Controls designed to reduce exposure of personal identifiers and sensitive applicant information in LLM processing |
| PDPA-aware data handling | Sensitive personal data handling in Thai FSI context |

---

### Risk 2 — Hallucination and Groundedness Risk

**Risk Description**
Large language models may generate plausible-sounding but unsupported claims — including fabricated risk signals, invented financial figures, or assertions not traceable to the input data. In a credit underwriting context, hallucinated content in an underwriter memo could mislead human reviewers and introduce errors into the decision process.

**Control Mechanism**
A secondary LLM Judge checks whether the AI-assisted risk summary is grounded in the assembled field set, flagging unsupported claims, hallucinated details, or assertions not traceable to input data. Summaries that fail the groundedness check are returned for revision and must not proceed to the Credit Memo stage without manual inspection.

**Where in Workflow**

| Node | Control Applied |
|---|---|
| Node 6 — LLM Judge | Groundedness check; unsupported claims flagged; failed summaries returned for revision or manual inspection |

**Framework Reference**

| Reference | Alignment |
|---|---|
| NIST AI RMF — MEASURE | Output quality assessment; groundedness verification |
| OWASP LLM Top 10 — Hallucination / Overreliance | Checks designed to reduce risk of fabricated content and over-reliance on unverified AI output |

---

### Risk 3 — Bias and Fairness Risk

**Risk Description**
AI-assisted risk summaries may embed bias if sensitive or proxy attributes — such as age, family status, education level, or income type — are used directly as risk signals or adverse decision reasons. In a credit context, this creates fair lending risk and may result in discriminatory outcomes.

**Control Mechanism**
Sensitive / Proxy Attributes (Group 2) are governed with strict controls: they may be used only with explicit restrictions — generalized, contextualized, or restricted to supporting narrative. Fields carrying fair lending violation risk (Group 3) — including gender — are excluded entirely from the assembled field set. LLM prompt design and output guardrail checks are designed to prevent sensitive attributes from appearing as adverse rationales in the AI-assisted memo.

**Where in Workflow**

| Node | Control Applied |
|---|---|
| Node 3 — Data Assembly | Group 3 fields blocked; Group 2 fields assembled with governance restrictions |
| Node 4 — Identifier Removal | Sensitive attributes generalized before LLM processing |
| Node 5 — LLM Summary | Prompt design restricts use of sensitive attributes as risk rationales |
| Node 7 — Output Guardrail | Discriminatory language check flags sensitive attribute references used as adverse reasons |

**Framework Reference**

| Reference | Alignment |
|---|---|
| NIST AI RMF — MEASURE / MANAGE | Bias identification and mitigation at data and output layers |
| Fair lending principles | Non-discrimination in AI-assisted credit decision support |

---

### Risk 4 — Explainability Risk

**Risk Description**
If an underwriter cannot understand or trace why a particular risk signal appeared in an AI-assisted memo, the decision process lacks explainability. This creates accountability risk and makes it difficult to contest, audit, or justify credit decisions — particularly where adverse outcomes affect applicants.

**Control Mechanism**
The workflow is designed to surface only approved, traceable risk signals in the AI-assisted memo. External scores (`EXT_SOURCE_2`, `EXT_SOURCE_3`) are presented as corroborating signals only, with explicit acknowledgment that source methodology has not been independently verified — preventing them from being treated as unexplained black-box inputs. Each memo includes a Flagged Items section that surfaces unresolved items for underwriter interpretation.

**Where in Workflow**

| Node | Control Applied |
|---|---|
| Node 3 — Data Assembly | EXT_SOURCE methodology gate applied before fields proceed to LLM layer |
| Node 5 — LLM Summary | External scores presented as corroborating signals only; methodology status disclosed |
| Node 8 — Credit Memo | Memo structure designed to make risk signals traceable and interpretable by the underwriter |

**Framework Reference**

| Reference | Alignment |
|---|---|
| NIST AI RMF — MEASURE | Explainability expectations in FSI review context |
| IAPP AIGP — Transparency | Transparency of AI-assisted risk signals presented to human reviewers |

---

### Risk 5 — Over-automation Risk

**Risk Description**
If underwriters rely on AI-assisted summaries without applying independent judgment, or if the workflow is designed in a way that makes AI outputs appear to be recommendations, there is a risk of over-reliance on AI-assisted summaries. This undermines human accountability and governance.

**Control Mechanism**
The workflow is designed to position AI as decision support only. The LLM must not generate approval or rejection language at any stage. Output guardrail checks block approval and rejection language before memos are issued. Every memo closes with a mandatory disclaimer. The underwriter retains full authority to agree with, override, or disregard any AI-assisted signal. Prioritization is based on signal complexity and governance flags — not on an automated score or ranking.

**Where in Workflow**

| Node | Control Applied |
|---|---|
| Node 5 — LLM Summary | LLM restricted from generating approval or rejection language |
| Node 7 — Output Guardrail | Approval and rejection language blocked before memo generation |
| Node 8 — Credit Memo | Mandatory disclaimer on every memo |
| Node 10 — Underwriter Final Review | Human retains full decision authority; rationale must be documented |

**Framework Reference**

| Reference | Alignment |
|---|---|
| NIST AI RMF — GOVERN | Human oversight and accountability in AI-assisted decision support |
| OWASP LLM Top 10 — Overreliance | Controls designed to reduce risk of over-reliance on AI output without human verification |
| IAPP AIGP — Human Oversight | Human-in-the-loop as a non-negotiable design requirement |

---

### Risk 6 — Auditability Risk

**Risk Description**
If AI-assisted outputs are not logged or linked to human reviewer decisions, there is no traceable record of how a credit decision was reached. This creates audit risk and may make it difficult to demonstrate accountability in a regulated environment.

**Control Mechanism**
Every AI-assisted memo is timestamped and linked to the application record. Underwriter decisions are logged with reviewer ID, timestamp, and documented rationale. Cases with unresolved governance flags are escalated and tracked. No credit decision is recorded without a documented human review.

**Where in Workflow**

| Node | Control Applied |
|---|---|
| Node 8 — Credit Memo | Memo timestamped and linked to application record |
| Node 9 — Prioritization | Unresolved governance flags escalated and tracked |
| Node 10 — Underwriter Final Review | Decision, rationale, reviewer ID, and timestamp logged to audit trail |

**Framework Reference**

| Reference | Alignment |
|---|---|
| NIST AI RMF — MANAGE | Audit trail and accountability across the decision lifecycle |
| IAPP AIGP — Accountability | Documented human decision-making linked to AI-assisted outputs |

---

### Risk 7 — Adverse Decision Framing Risk

**Risk Description**
If an AI-assisted memo contains language that implies a credit approval, rejection, or recommendation — even indirectly — it may be interpreted as a decision rather than as decision support. This creates accountability risk, regulatory exposure, and a risk of AI outputs substituting for human judgment.

**Control Mechanism**
LLM prompt design explicitly prohibits approval, rejection, and recommendation language. The output guardrail checks for and blocks approval and rejection language before memos are issued. The mandatory memo disclaimer reinforces that the memo does not approve or reject the applicant. The underwriter is required to document independent rationale for the final decision.

**Where in Workflow**

| Node | Control Applied |
|---|---|
| Node 5 — LLM Summary | Prompt design prohibits approval, rejection, and recommendation language |
| Node 7 — Output Guardrail | Approval and rejection language blocked; non-compliant summaries returned for revision |
| Node 8 — Credit Memo | Mandatory disclaimer on every memo: *"This memo is for underwriter decision support only. It does not approve or reject the applicant."* |
| Node 10 — Underwriter Final Review | Underwriter documents independent rationale for every decision |

**Framework Reference**

| Reference | Alignment |
|---|---|
| NIST AI RMF — GOVERN | Governance controls on AI output scope and human decision authority |
| OWASP LLM Top 10 — Overreliance | Controls designed to reduce the risk of AI output being treated as a final decision or recommendation without independent human judgment |

---

## Summary Table

| Risk | Primary Control Nodes | NIST AI RMF | OWASP LLM Risk Area |
|---|---|---|---|
| Privacy | Node 3, Node 4 | GOVERN / MAP | Sensitive Information Disclosure |
| Hallucination and Groundedness | Node 6 | MEASURE | Hallucination / Overreliance |
| Bias and Fairness | Node 3, Node 4, Node 5, Node 7 | MEASURE / MANAGE | — |
| Explainability | Node 3, Node 5, Node 8 | MEASURE | — |
| Over-automation | Node 5, Node 7, Node 8, Node 10 | GOVERN | Overreliance |
| Auditability | Node 8, Node 9, Node 10 | MANAGE | — |
| Adverse Decision Framing | Node 5, Node 7, Node 8, Node 10 | GOVERN | Overreliance |

---

## Relationship to Other Artifacts

| Artifact | Relationship |
|---|---|
| `01_architecture/01_underwriting_governance_workflow.md` | Source workflow — control nodes referenced throughout this document map directly to the ten-node workflow design |
| `04_data_reference/04_dataset_mapping.md` | Field-level governance baseline — Group 1, Group 2, and Group 3 field classifications inform Privacy, Bias/Fairness, and Explainability controls |
| `02_sample_credit_memo/02_sample_ai_credit_memo.md` | Illustrates how Adverse Decision Framing and Explainability controls are reflected in the Node 8 memo output |

---

*This control mapping is a governance design artifact. It is not a formal risk register, a regulatory compliance statement, or a certified audit framework.*
