# Sentient — Explainable Payment Fraud Detection PoC

Sentient is a payment fraud detection Proof of Concept exploring how a modern fraud platform can combine **deterministic risk intelligence** with **machine learning** while keeping fraud decisions explainable.

The project is designed around four intelligence domains:

- **Transaction Intelligence** => transaction-level payment signals
- **Behaviour Intelligence** => merchant activity and historical behaviour
- **Device Intelligence** => device trust and integrity signals
- **Merchant Intelligence** => merchant profile and transaction deviation

The current work demonstrates an end-to-end PoC using synthetic payment transaction data.

> **PoC / research notice:** Sentient is not a production banking fraud system. Current model results are based on synthetic data and are intended to demonstrate architecture, feature engineering and ML workflow rather than production fraud performance.

## Why Sentient?

Payment fraud detection requires more than a single rules engine or a single ML model. A practical platform needs to combine transaction context, behavioural history, merchant context and device signals, while giving investigators understandable reasons for a decision.

Sentient explores that architecture incrementally:

Transaction > Transaction Intelligence > Behaviour Intelligence > Device Intelligence > 

 Merchant Intelligence > Explainable Risk Assessment > ML Feature Layer > Fraud Probability / Classification

## Current PoC

### Rule Engine

The deterministic Rule Engine combines transaction, behavioural, device and merchant signals into an explainable risk assessment.

The current PoC demonstrates signals such as:

- Transaction amount and timing
- Merchant category
- Response outcome
- Merchant transaction velocity
- Amount velocity
- Failure behaviour
- Historical ticket-size context
- Merchant operating patterns
- Dormancy
- Device trust and integrity
- Merchant age and risk profile
- Transaction versus declared merchant ATS

The output is designed to provide both a risk decision and human-readable reasoning.

### ML Engine

The ML layer is built as a parallel layer over the feature pipeline.

The current PoC includes:

- Feature engineering
- Feature Store generation
- Supervised fraud label creation
- Dataset audit
- Cyclical time representation
- Categorical encoding
- Missing-value handling
- Numerical scaling
- Stratified train/test split
- Logistic Regression baseline

The current synthetic training dataset contains **5,000 transactions**, including **233 fraud-labelled transactions** and **4,767 genuine transactions**.

The first processed ML matrix contains **618 model features** after preprocessing.

## First ML Result

The first Logistic Regression baseline produced fantastic separation on the current synthetic test set.

The current synthetic fraud generation is dominated by a high-value fraud scenario. As a result, transaction amount creates a very strong relationship with the fraud label. When the amount-related features are removed, model performance drops substantially.

This is an intentional finding from the PoC: it demonstrates that the ML pipeline is functioning, while also highlighting the need for richer and more realistic synthetic fraud scenarios before model quality can be assessed meaningfully.

## Architecture — High Level




                     ┌──────────────────────┐
                     │  Synthetic Payments  │
                     └──────────┬───────────┘
                                │
                                ▼
                     ┌──────────────────────┐
                     │ Transaction Context  │
                     └──────────┬───────────┘
                                │
              ┌─────────────────┼─────────────────┐
              ▼                 ▼                 ▼
       Behaviour             Device           Merchant
       Intelligence        Intelligence      Intelligence
              └─────────────────┼─────────────────┘
                                ▼
                     ┌──────────────────────┐
                     │ Explainable Rules    │
                     └──────────┬───────────┘
                                │
                                ▼
                     ┌──────────────────────┐
                     │ Feature Engineering  │
                     └──────────┬───────────┘
                                │
                                ▼
                     ┌──────────────────────┐
                     │ ML Preprocessing     │
                     └──────────┬───────────┘
                                │
                                ▼
                     ┌──────────────────────┐
                     │ ML Baseline          │
                     │ Logistic Regression  │
                     └──────────────────────┘
```

## Demonstration Entry Points

The private engineering repository contains the complete implementation.

The two primary runtime entry points are:

```bash
# Sentient deterministic Rule Engine
uv run -m engine.risk_engine

# Sentient ML Engine
uv run -m feature_engineering.model_training
```

The public showcase repository intentionally does **not** contain the complete proprietary implementation.

## What This Public Repository Contains

This repository is a curated technical showcase intended for:

- Product and engineering discussions
- Technical due diligence conversations
- payment-domain and fraud-platform 
- Demonstrating progression from rules to ML

The public materials focus on architecture, methodology, decisions and PoC outcomes rather than exposing the underlying implementation.

## What Is Deliberately Not Published

The complete engineering repository remains private.

The following are intentionally excluded from this public showcase:

- Detailed scoring formulas and rule weights
- Full Feature Builder / Feature Store implementation
- Synthetic data & fraud-injection implementation
- Internal master data
- Complete training datasets
- Full development history
- Internal implementation details that could expose product IP


## Roadmap

### Completed / Demonstrated

- Explainable payment risk engine
- Synthetic transaction generation
- Behaviour intelligence
- Device intelligence
- Merchant intelligence
- Feature engineering and Feature Store
- Fraud labels and training dataset
- ML preprocessing pipeline
- Logistic Regression baseline



## Technology

Current PoC technology includes:

- Python
- Pandas
- NumPy
- scikit-learn
- DuckDB
- openpyxl
- uv

## Important Caveat

The current dataset is synthetic and the fraud-generation methodology is intentionally simplified. Performance results should therefore be interpreted as **engineering and modelling milestones**, not as evidence of live fraud-detection effectiveness.

A production deployment for a bank or payment institution would require substantially more work across data quality, realistic fraud labels, model validation, threshold calibration, monitoring, explainability, security, governance, resilience, regulatory requirements and operational integration.

## About the Project

Sentient is an independent payment-fraud technology PoC exploring the evolution from explainable deterministic risk rules toward supervised ML-based fraud detection.
