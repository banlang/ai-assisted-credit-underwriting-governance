# AI-Assisted Credit Underwriting Governance Workflow
> **Design artifact only.** This document describes a governance-first workflow design for AI-assisted credit underwriting decision support. It is not a deployable system, not a credit scoring model, and not an automated decisioning engine. All underwriting decisions remain with human underwriters.

---

## Purpose

This document describes how AI and humans are designed to work together in a credit underwriting review process — with controls, transparency, and human accountability at every stage.

The workflow is designed to support underwriters, not replace them. AI summarizes risk signals. Humans interpret evidence and make all final credit decisions.

---

## Workflow Overview

The workflow moves through ten nodes in sequence. Each node has a defined input, output, and at least one governance control point.

```
Application Stream
       ↓
   Screening
       ↓
 Data Assembly
       ↓
Identifier Removal
       ↓
  LLM Summary
       ↓
  LLM Judge
       ↓
Output Guardrail
       ↓
  Credit Memo
       ↓
 Prioritization
       ↓
Underwriter Final Review
```

---

## Node-by-Node Design

### Node 1 — Application Stream

| Element | Detail |
|---|---|
| **Input** | Raw application data submitted by the applicant |
| **Output** | Application record passed to screening queue |
| **Control Point** | Application completeness check — incomplete records are routed for human follow-up or clarification before AI-assisted summarization |

---

### Node 2 — Screening

| Element | Detail |
|---|---|
| **Input** | Application record from Node 1 |
| **Output** | Screened application record with evidence-readiness status |
| **Control Point** | Basic evidence-readiness check — missing documents, incomplete fields, or inconsistent inputs are flagged for human follow-up before AI-assisted summarization |

---

### Node 3 — Data Assembly

| Element | Detail |
|---|---|
| **Input** | Screened application record |
| **Output** | Assembled field set from approved governance groups only |
| **Control Point 1** | Field selection is restricted to the approved field set defined in `04_dataset_mapping.md` — Group 1 (Core Underwriting Signals) and, where contextually permitted, Group 2 (Sensitive / Proxy Attributes with controls). Group 3 fields (Excluded from Decision Reason) are blocked at this stage. |
| **Control Point 2** | `TARGET` exclusion — the historical repayment label must not enter the assembled field set at any point. Inclusion would constitute data leakage into the decision-support narrative. |
| **Control Point 3** | `DAYS_EMPLOYED` anomaly check — if the value equals **365,243**, the field is flagged as a data quality anomaly and normalized or replaced with a placeholder indicator before proceeding. This value must not be interpreted as a valid employment duration by any downstream step. |
| **Control Point 4** | `EXT_SOURCE_2` and `EXT_SOURCE_3` methodology gate — these fields are flagged for methodology review. Directionality and source methodology must be verified before they are passed to the LLM Summary layer. If methodology is unverified, these fields are tagged as "corroborating signal — methodology unverified" in the assembled record. |
| **Control Point 5** | `NAME_INCOME_TYPE` context restriction — this field is assembled as an income stability context label only. It must not be ranked, scored, or used as a primary signal at this or any downstream stage. |

---

### Node 4 — Identifier Removal

| Element | Detail |
|---|---|
| **Input** | Assembled field set from Node 3 |
| **Output** | De-identified field set ready for LLM processing |
| **Control Point** | Direct personal identifiers — including applicant name, national ID, date of birth in raw form, address, and contact details — are designed to be removed or pseudonymized before the record enters the LLM layer. Sensitive / Proxy Attributes that are permitted for use (Group 2) must be generalized at this stage (e.g., `DAYS_BIRTH` converted to age band). |

---

### Node 5 — LLM Summary

| Element | Detail |
|---|---|
| **Input** | De-identified field set from Node 4 |
| **Output** | AI-assisted risk summary covering repayment capacity, credit exposure, employment stability, and relevant contextual signals |
| **Control Point 1** | Prompt design restricts the LLM to summarizing approved risk signals only. The prompt must not include `TARGET`, excluded fields, or direct identifiers. |
| **Control Point 2** | `EXT_SOURCE_2` / `EXT_SOURCE_3` wording control — if methodology is unverified, the summary must present these scores as corroborating signals only, with explicit acknowledgment that source methodology has not been verified. They must not appear as standalone adverse reasons. |
| **Control Point 3** | `NAME_INCOME_TYPE` framing control — the summary must present income type as a context label only. The LLM must not rank income types or frame them as risk rationales. |
| **Control Point 4** | The LLM must not generate approval language, rejection language, or a final credit recommendation of any kind. Output is limited to risk signal summary and evidence narrative. |

---

### Node 6 — LLM Judge

| Element | Detail |
|---|---|
| **Input** | AI-assisted risk summary from Node 5 |
| **Output** | Groundedness assessment with pass / flag / return-for-revision status |
| **Control Point 1** | A secondary LLM Judge checks whether the summary is grounded in the assembled field set — flagging unsupported claims, hallucinated details, or assertions not traceable to the input data. |
| **Control Point 2** | The LLM Judge checks that no excluded fields (Group 3), direct identifiers, or `TARGET` values appear in the summary narrative. |
| **Control Point 3** | Summaries that fail the groundedness check are flagged for human review and must not proceed to the Credit Memo stage without manual inspection. |

---

### Node 7 — Output Guardrail

| Element | Detail |
|---|---|
| **Input** | LLM Judge output — passed or flagged summary |
| **Output** | Cleared summary ready for memo generation, or escalation for human inspection |
| **Control Point 1** | Guardrail scan checks for PII patterns in the summary text — names, ID numbers, raw dates of birth, addresses. Detected PII is blocked or redacted before proceeding. |
| **Control Point 2** | Discriminatory language check — the guardrail is designed to flag language that references gender, religion, ethnicity, family status, or social-circle attributes as risk factors. |
| **Control Point 3** | Approval and rejection language check — any summary containing language that implies a credit approval, rejection, or recommendation is blocked and returned for revision. |

---

### Node 8 — Credit Memo

| Element | Detail |
|---|---|
| **Input** | Cleared summary from Node 7 |
| **Output** | Structured underwriter-facing decision-support memo |
| **Control Point 1** | Memo structure follows a defined template: (1) Applicant Risk Profile, (2) Key Risk Signals, (3) Contextual Factors, (4) Flagged Items for Underwriter Attention. No section may contain approval or rejection language. |
| **Control Point 2** | Every memo closes with the mandatory disclaimer: *"This memo is for underwriter decision support only. It does not approve or reject the applicant."* |
| **Control Point 3** | Memo is timestamped and linked to the application record for audit trail purposes. |

---

### Node 9 — Prioritization

| Element | Detail |
|---|---|
| **Input** | Completed credit memo |
| **Output** | Prioritized review queue assignment |
| **Control Point** | Cases are assigned a review priority based on signal complexity, unresolved governance flags, and need for senior human judgment — not on a single score or automated ranking. High-risk and borderline cases are designed to be surfaced to senior underwriter review. Cases with unresolved flags from Node 6 or Node 7 are always escalated regardless of other signals. |

---

### Node 10 — Underwriter Final Review

| Element | Detail |
|---|---|
| **Input** | Prioritized credit memo and supporting application record |
| **Output** | Underwriter decision with documented rationale |
| **Control Point 1** | The underwriter reviews the AI-assisted memo as decision support evidence — not as a recommendation. The underwriter retains full authority to agree with, override, or disregard any AI-assisted summary or signal. |
| **Control Point 2** | The underwriter documents the rationale for the final decision, including any instances where AI-assisted signals were set aside. |
| **Control Point 3** | The decision, rationale, reviewer ID, and timestamp are logged to the audit trail. No credit decision is recorded without a documented human review. |

---

## Design Principles

These principles govern the workflow design at every node.

```
1. AI summarizes — humans decide
2. Direct identifiers are designed to be removed before LLM processing
3. Every AI output is designed to be checkable and auditable
4. High-risk cases should escalate to human review
5. The system cannot approve or reject — only assist
```

---

## Governance Control Summary

| Control Area | Node | Governance Reference |
|---|---|---|
| Field selection — approved set only | Node 3 | NIST AI RMF — MAP |
| `TARGET` leakage prevention | Node 3, Node 5 | OWASP LLM Top 10 — Sensitive Information Disclosure |
| `DAYS_EMPLOYED` anomaly normalization | Node 3 | NIST AI RMF — MAP / MANAGE |
| `EXT_SOURCE_2/3` methodology gate | Node 3, Node 5 | NIST AI RMF — MEASURE |
| `NAME_INCOME_TYPE` proxy restriction | Node 3, Node 5 | NIST AI RMF — MEASURE / MANAGE; fair lending principles |
| Direct identifier removal | Node 4 | NIST AI RMF — GOVERN / MAP; PDPA-aware data handling |
| Sensitive attribute generalization | Node 4 | NIST AI RMF — MEASURE / MANAGE; fair lending principles |
| LLM prompt scope control | Node 5 | OWASP LLM Top 10 — Prompt Injection |
| Groundedness check | Node 6 | OWASP LLM Top 10 — Hallucination / Overreliance |
| PII scan | Node 7 | OWASP LLM Top 10 — Sensitive Information Disclosure |
| Discriminatory language check | Node 7 | NIST AI RMF — MEASURE / MANAGE; fair lending principles |
| Approval / rejection language block | Node 7, Node 8 | NIST AI RMF — GOVERN |
| Audit trail | Node 8, Node 10 | NIST AI RMF — MANAGE |
| Human final decision | Node 10 | IAPP AIGP — human oversight and accountability |

---

## Relationship to Other Artifacts

| Artifact | Relationship |
|---|---|
| `04_data_reference/04_dataset_mapping.md` | Field-level governance baseline — defines approved field set, carry-forward controls, and exclusion rules referenced in Nodes 3–5 |
| `02_sample_credit_memo/02_sample_ai_credit_memo.md` | Illustrates Node 8 output — synthetic examples of AI-assisted underwriter decision-support memos |
| `03_governance_controls/03_responsible_ai_control_mapping.md` | Maps workflow controls to AI risk categories — expands on the Governance Control Summary table above |

---

*This workflow design is a governance artifact. It is not a system specification, a deployment blueprint, or a regulatory compliance document.*
