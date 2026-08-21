# SENTIENT — Technical Overview

**Public PoC showcase | Explainable Enterprise Payment Fraud Detection**

## 1. Architecture & Product Concept

Sentient is a payment-fraud detection PoC that combines a deterministic, explainable risk engine with a supervised machine-learning layer.

The design separates risk signals into four intelligence domains:

- **Transaction Intelligence**
- **Behaviour Intelligence**
- **Device Intelligence**
- **Merchant Intelligence**

This allows deterministic risk signals and analyst-oriented reasoning to remain explainable while the ML layer learns relationships and interactions across candidate features.

### High-level flow

```text
Transaction Data
      ↓
Intelligence & Feature Generation
      ↓
 ┌───────────────────────────────┐
 │                               │
 ▼                               ▼
Rule Engine                  ML Pipeline
Deterministic Risk           Learned Fraud
Assessment                   Probability
 │                               │
 └───────────────┬───────────────┘
                 ↓
       Risk / Analyst View
```

---

## 2. Rule Engine — Current Capability

The Rule Engine is the mature deterministic layer of the PoC. It evaluates transaction context and historical merchant behaviour and returns domain-level risk contributions together with human-readable reasoning.

### Transaction Intelligence

Current transaction-level intelligence includes:

- Amount risk
- Time risk
- MCC risk
- Response-code risk

### Behaviour Intelligence

Current behavioural signals include:

- Merchant velocity
- Amount velocity
- Failure rate
- Rolling average ticket size
- Active hours
- Hourly transaction pattern
- Merchant dormancy
- Daily transaction-volume spike

These signals provide historical context around the merchant rather than relying only on the current transaction.

### Device Intelligence

Current device signals include:

- Device trust
- Rooted/jailbroken status
- Device integrity

These provide additional confidence/security context when device information is available.

### Merchant Intelligence

Current merchant signals include:

- Merchant age
- Merchant risk tier
- Transaction versus declared ATS

These capture merchant maturity, profile and deviation from expected transaction behaviour.

### Explainability

The engine aggregates these signals into a consolidated risk assessment and produces human-readable reasoning. The design intentionally retains explainability rather than replacing the deterministic risk layer with an opaque score.

### Run locally

```bash
uv run -m engine.risk_engine
```

The public showcase does **not** expose proprietary scoring weights, detailed rule formulas or implementation source. A representative Rule Engine output is included in the `demo/` folder.

---

## 3. ML Engine — Current Capability

The ML layer is being developed as a parallel capability on top of the Feature Store.

The first supervised model is **Logistic Regression**, selected as an interpretable baseline before moving to tree/boosting models and later SHAP/XAI.

### Current training data

- **5,000 synthetic transactions**
- **233 seeded fraud transactions**
- **4,767 genuine transactions**
- Approximately **4.66% fraud rate**

### ML preprocessing

The current preprocessing pipeline includes:

- Cyclical encoding of transaction hour
- Cyclical encoding of transaction month
- Categorical treatment of MCC and PINCODE
- One-hot encoding
- Explicit handling of meaningful missingness
- Numerical imputation
- Numerical scaling
- Binary/availability features retained as 0/1
- Stratified train/test splitting

The resulting processed feature matrix currently contains **618 ML features**.

### First ML result

The first all-feature Logistic Regression experiment achieved perfect separation on the current synthetic test set.

Investigation showed that the current synthetic fraud generator primarily represents **high-value fraud**, making transaction amount strongly associated with the fraud label.

When the dominant amount-driven signals were removed, the diagnostic baseline produced:

| Metric | Result |
|---|---:|
| Fraud recall | **30%** |
| Fraud precision | **6%** |
| Fraud F1 | **10%** |
| ROC-AUC | **0.5303** |

### Interpretation

This result is deliberately treated as a **diagnostic PoC**, not as production-level model performance.

The experiment demonstrated that the ML pipeline is operational, while also showing that the synthetic fraud data needs richer and more realistic fraud scenarios before model quality can be meaningfully assessed.

### Run locally

```bash
uv run -m feature_engineering.model_training
```

---

## 4. What We Learned from the First ML Baseline

The first ML experiment provided an important data-generation insight.

The existing synthetic fraud pattern is dominated by high-value fraud. Because the fraud injector changes transaction amount substantially for seeded fraud transactions, amount becomes highly predictive of the target.

This creates strong synthetic separability:

```text
High transaction amount
        ↓
Strong fraud-label association
        ↓
Very high / perfect model performance
```

Removing the dominant amount-driven signals causes performance to fall substantially.

This means the current 1.0 result should **not** be interpreted as evidence of real-world fraud-detection accuracy.

### Next modelling milestone

The planned improvement is **scenario-based synthetic fraud generation**, including:

- Velocity-burst fraud
- Dormant-merchant activity
- Device-compromise fraud
- Merchant-profile deviation

The objective is to create coherent transaction conditions and allow the feature layer to calculate the resulting behavioural signals naturally rather than randomly modifying individual ML features.

---

## 5. Scope, Demonstration & IP Boundary

This repository is a curated public showcase of the Sentient concept and selected PoC outputs.

The protected engineering repository retains:

- Proprietary implementation details
- Detailed scoring weights and formulas
- Full feature-generation logic
- Fraud-injection implementation
- Master data
- Private development history

The public repository is intentionally limited to architecture, methodology, selected outputs and demonstration material.

### PoC disclaimer

Sentient is based on **synthetic payment data** and has not been validated on production bank/payment data.

It should not be used for live fraud decisions without substantial additional validation, model governance, monitoring, security, operational controls and regulatory review.

---

**Synthetic-data PoC • Demonstration of payment-fraud product and ML engineering concepts**
