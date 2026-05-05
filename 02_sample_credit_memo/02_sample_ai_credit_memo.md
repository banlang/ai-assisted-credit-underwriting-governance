# Sample AI-Assisted Credit Memo — Underwriter Decision Support
> **Design artifact only.** The applicant profiles and data values in this document are entirely synthetic. They do not represent real individuals or real credit applications. This document illustrates the Node 8 output of the AI-Assisted Credit Underwriting Governance Workflow.

---

## How to Read This Memo

Each memo is structured in four sections:

1. **Applicant Risk Profile** — summary of key financial signals
2. **Key Risk Signals** — evidence relevant to repayment capacity and credit exposure
3. **Contextual Factors** — supporting signals that require underwriter interpretation
4. **Flagged Items for Underwriter Attention** — unresolved items requiring human judgment

Memos are designed to follow the LLM Judge and Output Guardrail stages. They are designed to support underwriter review — not to recommend a credit decision.

---

# Case A — Medium Risk Profile

**Application Reference:** APP-2024-00412
**Review Priority:** Standard — Assign to available underwriter
**Memo Type:** Synthetic AI-assisted memo example | Illustrates post-LLM Judge and Output Guardrail output

---

## 1. Applicant Risk Profile

| Signal | Value | Governance Treatment |
|---|---|---|
| Total Income | THB 45,000 / month | Core Underwriting Signal |
| Credit Amount Requested | THB 540,000 | Core Underwriting Signal |
| Monthly Annuity (Installment) | THB 18,500 | Core Underwriting Signal |
| Employment Duration | 3 years 2 months (currently employed) | Core Underwriting Signal |
| Income Type | Salaried employee | Core Underwriting Signal — context label only |
| Credit Bureau Enquiries (Past Year) | 4 enquiries | Core Underwriting Signal |
| External Score 2 | 0.61 | Core Underwriting Signal — corroborating only |
| External Score 3 | 0.58 | Core Underwriting Signal — corroborating only |
| Age Band | 35–44 years | Sensitive Attribute — generalized |
| Household Obligations | Married; 1 dependent | Sensitive Attribute — affordability context only |

---

## 2. Key Risk Signals

**Debt-to-Income Ratio**
Monthly annuity of THB 18,500 represents approximately 41% of declared monthly income of THB 45,000. This indicates elevated debt burden and warrants underwriter attention regarding residual income after obligations.

**Employment Stability**
Applicant has been continuously employed for over three years in a salaried role. No anomalous employment data values were detected. Employment duration supports income stability assessment.

**Credit Bureau Enquiries**
Four credit bureau enquiries in the past twelve months. This volume may indicate active credit-seeking behavior. Context and recency of enquiries should be considered before treating this as an adverse signal — underwriter review is recommended.

**External Scores**
External Score 2: 0.61 | External Score 3: 0.58

Both scores are presented as corroborating signals only. Source methodology and directionality for these scores have not been independently verified. They should not be used as standalone adverse reasons. Underwriter should treat these as one input among several.

---

## 3. Contextual Factors

**Household Obligations**
Applicant is married with one dependent. This context is provided for affordability assessment only — it does not constitute a risk rationale and must not be used as an adverse decision reason.

**Age Band**
Applicant falls within the 35–44 age band. This context is provided for completeness only and must not be used as an adverse reason.

**Income Type**
Salaried employment is noted as income stability context. Income type must not be ranked or used as a risk rationale.

---

## 4. Flagged Items for Underwriter Attention

| Flag | Description | Recommended Action |
|---|---|---|
| Elevated debt burden | Monthly obligation represents ~41% of income | Verify residual income and existing liabilities before proceeding |
| Credit bureau enquiry volume | 4 enquiries in 12 months | Review recency and purpose of enquiries |
| External score methodology unverified | EXT_SOURCE_2 and EXT_SOURCE_3 used as corroborating signals only | Do not apply as standalone adverse reason; treat as supporting context |

---

*This memo is for underwriter decision support only. It does not approve or reject the applicant.*

---

# Case B — High Risk Profile

**Application Reference:** APP-2024-00589
**Review Priority:** Escalate to Senior Underwriter — multiple risk signals require human judgment
**Memo Type:** Synthetic AI-assisted memo example | Illustrates post-LLM Judge and Output Guardrail output

---

## 1. Applicant Risk Profile

| Signal | Value | Governance Treatment |
|---|---|---|
| Total Income | THB 28,000 / month | Core Underwriting Signal |
| Credit Amount Requested | THB 630,000 | Core Underwriting Signal |
| Monthly Annuity (Installment) | THB 22,800 | Core Underwriting Signal |
| Employment Duration | Data quality flag — see Section 4 | Core Underwriting Signal — anomaly detected |
| Income Type | Self-employed / Commercial associate | Core Underwriting Signal — context label only |
| Credit Bureau Enquiries (Past Year) | 9 enquiries | Core Underwriting Signal |
| External Score 2 | 0.29 | Core Underwriting Signal — corroborating only |
| External Score 3 | 0.31 | Core Underwriting Signal — corroborating only |
| Age Band | 45–54 years | Sensitive Attribute — generalized |
| Household Obligations | Single; 3 dependents | Sensitive Attribute — affordability context only |

---

## 2. Key Risk Signals

**Debt-to-Income Ratio**
Monthly annuity of THB 22,800 represents approximately 81% of declared monthly income of THB 28,000. Residual monthly income after the proposed obligation would be approximately THB 5,200 before other living expenses. This represents a significant repayment stress indicator and requires senior underwriter review.

**Employment Duration — Data Quality Flag**
The employment duration field contains a special placeholder value that has been identified as a data quality anomaly. This field has been excluded from the risk summary and replaced with a flag. Underwriter should verify employment duration directly from supporting documentation before proceeding.

**Credit Bureau Enquiries**
Nine credit bureau enquiries in the past twelve months. This elevated volume may indicate sustained credit-seeking behavior or financial stress. Context, recency, and purpose of enquiries require underwriter investigation before a credit review can be completed.

**External Scores**
External Score 2: 0.29 | External Score 3: 0.31

Both scores are presented as corroborating signals only. Source methodology and directionality have not been independently verified. The underwriter should not infer risk direction from these values without methodology confirmation.

---

## 3. Contextual Factors

**Household Obligations**
Applicant is single with three dependents. This context is provided for affordability assessment only — combined with the debt-to-income signal, the underwriter may wish to consider the household expenditure profile as part of the repayment capacity review. This factor must not be used as a standalone adverse reason.

**Age Band**
Applicant falls within the 45–54 age band. This context is provided for completeness only and must not be used as an adverse reason.

**Income Type**
Income is categorized as commercial associate / self-employed. This is noted as income stability context only. Income type must not be ranked or used as a risk rationale — income stability assessment should be based on supporting documentation.

---

## 4. Flagged Items for Underwriter Attention

| Flag | Description | Recommended Action |
|---|---|---|
| Significant repayment stress indicator | Monthly obligation represents ~81% of declared income | Senior underwriter review required — verify income documentation and existing liabilities |
| Employment duration anomaly | Special placeholder value detected in DAYS_EMPLOYED field | Verify employment duration from supporting documents before proceeding — do not use AI-assisted summary value |
| Elevated credit bureau enquiries | 9 enquiries in 12 months | Investigate recency, purpose, and outcome of enquiries |
| External score methodology unverified | EXT_SOURCE_2 and EXT_SOURCE_3 included as corroborating signals only | Do not infer risk direction or apply as standalone adverse reasons; treat as supporting context only after methodology review |
| Income documentation | Self-employed / commercial associate income requires verification | Request supporting income documents before credit review proceeds |

---

*This memo is for underwriter decision support only. It does not approve or reject the applicant.*

---

## Memo Design Notes

These synthetic memos illustrate how the workflow is designed to operate at Node 8. Key design choices reflected in both cases:

| Design Choice | Rationale |
|---|---|
| No approval or rejection language | Consistent with workflow principle: AI summarizes — humans decide |
| External scores presented as corroborating signals only | Methodology and directionality unverified — carry-forward control from `04_dataset_mapping.md` |
| `DAYS_EMPLOYED` anomaly surfaced as a flag | Special placeholder value normalized before LLM summary — underwriter directed to source documents |
| Income type used as context label only | Not ranked or used as adverse rationale — carry-forward control from `04_dataset_mapping.md` |
| Sensitive attributes generalized | Age presented as band; household obligations framed as affordability context only |
| Mandatory disclaimer on every memo | Required control at Node 8 — no memo may be issued without it |

---

*These applicant profiles are entirely synthetic. No real customer data has been used. This document is a governance design artifact illustrating the AI-assisted underwriter decision-support workflow.*
