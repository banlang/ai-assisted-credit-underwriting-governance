# Dataset Mapping — AI-Assisted Credit Underwriting Governance Workflow

> **Reference only.** This document maps selected fields from the Home Credit Default Risk dataset (Kaggle) to their intended role in the governance workflow design. No model is trained. No customer-level records from the dataset are included in this repository. All applicant examples are synthetic.

---

## Dataset Reference

**Source:** [Home Credit Default Risk](https://www.kaggle.com/c/home-credit-default-risk/data) (Kaggle)
**Primary reference table:** `application_train.csv`
**Comparable structure:** `application_test.csv` — excludes `TARGET` field
**Purpose:** Ground the workflow design in realistic applicant data fields — not for model training or statistical analysis.

---

## Field Mapping by Governance Group

### Group 1 — Core Underwriting Signals

These fields represent primary risk evidence that may be summarized in the AI-assisted underwriter decision-support memo. They are used to support human review of repayment capacity, credit exposure, and employment stability.

| Field | Description | Underwriting Use | Governance Note |
|---|---|---|---|
| `AMT_INCOME_TOTAL` | Total income of the applicant | Repayment capacity assessment | Low direct identifier risk; retain at necessary granularity for repayment capacity assessment — apply data minimization if finer precision is not required |
| `AMT_CREDIT` | Credit amount requested | Loan exposure sizing | Retain at necessary granularity for exposure assessment — do not enrich or combine with external data sources outside the approved field set |
| `AMT_ANNUITY` | Monthly loan annuity (installment) | Monthly debt burden calculation | Combine with `AMT_INCOME_TOTAL` to derive debt-to-income signal; do not use standalone as adverse reason |
| `DAYS_EMPLOYED` | Days before application since current employment started | Employment stability indicator | Negative value = currently employed. Value **365,243** is a known special placeholder indicating a data quality anomaly — must be normalized or flagged before reaching the LLM summary layer; do not interpret directly |
| `EXT_SOURCE_2` | Normalized score from external credit data source | External risk signal | Normalized 0–1; directionality and source methodology must be verified before use in underwriter memo; treat as corroborating signal only — must not be used as sole or standalone adverse decision reason |
| `EXT_SOURCE_3` | Normalized score from external credit data source | External risk signal | Same governance treatment as `EXT_SOURCE_2`; use both as corroborating signals only; black-box methodology risk applies until source is verified |
| `AMT_REQ_CREDIT_BUREAU_YEAR` | Credit bureau enquiries in the past year | Recent credit-seeking behavior | High enquiry count may indicate financial stress; context and recency required before use as adverse reason — do not apply mechanically |
| `NAME_INCOME_TYPE` | Type of income (Working, Pensioner, Commercial associate, etc.) | Income stability context | Use as context label only — do not rank income types as proxies for creditworthiness; must not appear as adverse rationale in underwriter memo |

---

### Group 2 — Sensitive / Proxy Attributes

These fields may carry fairness risk if used directly as decision reasons. They may be used **only with strict governance controls** — not as standalone adverse decision reasons. Where used, they must be generalized, contextualized, or restricted to supporting narrative only.

| Field | Governance Concern | Permitted Handling |
|---|---|---|
| `DAYS_BIRTH` | Age discrimination risk | Convert to age band (e.g., "35–45 years") if contextually necessary; never use raw age as adverse reason |
| `NAME_EDUCATION_TYPE` | Socioeconomic proxy | Include as context only; not a standalone decision driver; avoid framing as "lower education = higher risk" |
| `NAME_FAMILY_STATUS` | Demographic / household proxy | Restrict use; may appear in affordability context only — not as risk rationale |
| `CNT_CHILDREN` | Family status / affordability proxy | Use only as affordability context (e.g., household obligations); avoid discriminatory framing |

---

### Group 3 — Excluded from Decision Reason

These fields are excluded from the AI-assisted underwriter decision-support memo and must not appear as decision reasons, risk signals, or narrative justifications at any stage of the workflow.

| Field | Reason for Exclusion |
|---|---|
| `CODE_GENDER` | Fair lending violation risk — gender must not be used as a credit decision factor |
| `REGION_POPULATION_RELATIVE` | Geography / population density proxy — may introduce indirect discrimination based on region |
| `DEF_30_CNT_SOCIAL_CIRCLE` | Social-circle proxy risk — penalizing an applicant based on default behavior of their social network is unfair and unsupported by individual creditworthiness |
| `TARGET` | Historical repayment label (dataset-specific) — represents past outcomes and must not appear in applicant-facing or underwriter support narrative; inclusion at any stage of the workflow would constitute data leakage |

---

## Design Principles Reflected in This Mapping

```
1. AI summarizes risk signals — humans interpret and decide
2. Sensitive attributes require governance controls before use
3. Proxy discrimination risks are identified and restricted at data level
4. No field excluded from governance review simply because it is "available"
5. Data leakage prevention begins at field selection, not model design
```

---


## Carry-Forward Controls for Workflow Design

The following controls must be reflected in downstream workflow and memo artifacts:

| Control Topic | Carry-Forward Requirement |
|---|---|
| `EXT_SOURCE_2` / `EXT_SOURCE_3` methodology risk | Workflow design must include a methodology verification control before these fields are summarized in an underwriter memo. Memo wording must treat them as corroborating signals only, not standalone adverse reasons. |
| `DAYS_EMPLOYED` anomaly handling | Workflow design must include normalization or flagging for the special placeholder value **365,243** before any LLM summary step. |
| `NAME_INCOME_TYPE` proxy control | Workflow design must restrict this field to income stability context only. It must not be ranked or used as an adverse rationale. |
| `TARGET` leakage prevention | Workflow design must exclude `TARGET` from applicant-level review, LLM prompts, underwriter memo content, and audit narrative. |

---

## Governance Framework Alignment

| Control Layer | Reference |
|---|---|
| Privacy and data minimization | NIST AI RMF — GOVERN / MAP |
| Fairness and non-discrimination | NIST AI RMF — MEASURE / MANAGE; fair lending principles; PDPA-aware sensitive data handling |
| Explainability of risk signals | NIST AI RMF — MEASURE; explainability expectations in FSI review context |
| Data anomaly handling | NIST AI RMF — MAP / MANAGE; workflow normalization control before LLM processing |
| Leakage prevention | OWASP LLM Top 10 — Sensitive Information Disclosure |

---

*This mapping informs the workflow design in `01_architecture/01_underwriting_governance_workflow.md` and the synthetic applicant examples in `02_sample_credit_memo/02_sample_ai_credit_memo.md`.*
