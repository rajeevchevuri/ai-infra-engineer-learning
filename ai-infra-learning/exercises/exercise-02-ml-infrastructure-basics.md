Here are the complete solutions to the **Practical Exercise** and the **Self-Check Questions** based on the lesson material.

---

## Practical Exercise: Spam Detection System

Using **Email Spam Detection** as our target ML use case, here is a breakdown of the requirements and considerations across the infrastructure lifecycle:

### 1. Data Requirements

* **Data Needed:** Raw email body text, subject lines, sender metadata (IP address, domain age, authentication headers like SPF/DKIM), attachment types, and historical user flags ("Report Spam" actions).
* **Data Sources:** Real-time email gateway logs, user action streams via message brokers (e.g., Apache Kafka), and historical email archives stored in object storage (e.g., AWS S3).
* **Processing & Versioning:** Batch extraction via PySpark for historical baseline datasets; real-time feature generation for metadata via a Feature Store. Datasets versioned using **DVC**.

### 2. Compute Needs

* **Training Resources:** Moderate GPU compute (e.g., single or dual NVIDIA T4/A10G instance) or CPU clusters if using lighter models (e.g., TF-IDF + DistilBERT or XGBoost). Training run durations range from minutes to a few hours.
* **Serving Resources:** High-throughput, low-latency CPU instances running inside Kubernetes (EKS/GKE) scaled using Horizontal Pod Autoscalers (HPA) behind an API gateway.

### 3. Success Metrics

* **Offline ML Metrics:** Precision (critical to avoid marking legitimate emails as spam), Recall, F1-Score, and Area Under the ROC Curve (ROC-AUC).
* **Business Metrics:** Reduction in user-reported spam, false positive rate (legitimate emails sent to spam), and average end-to-end processing delay per email.

### 4. Production Monitoring Plan

* **System Metrics:** Request latency ($p_{95} < 50\text{ ms}$), throughput (emails processed per second), CPU/Memory utilization, and API HTTP error rates.
* **Model Metrics:** Shift in predicted spam vs. ham ratios, confidence score distribution, and input data drift (e.g., changes in vocabulary or email size length over time).
* **Alerting:** Real-time alerts via Prometheus/Grafana if $p_{99}$ latency exceeds $100\text{ ms}$ or if the false positive rate spikes on sample checks.

### 5. Failure Modes & Mitigation

| Stage | Potential Failure Mode | Prevention / Mitigation |
| --- | --- | --- |
| **Data** | Poisoned labels or breaking changes in email header formats | Implement strict schema and data validation (e.g., Great Expectations) |
| **Training** | Model overfits to a seasonal spam campaign | Use automated validation on historical time-split test sets |
| **Deployment** | High latency spike delays incoming user emails | Use asynchronous queueing (Kafka) and set strict inference timeouts |
| **Monitoring** | Concept Drift (spammers change tactics to bypass filters) | Continuous drift detection triggering automated retraining pipelines |

---

## Answers to Self-Check Questions

**1. What are the six stages of the ML lifecycle?**
The six interconnected stages are:

1. Data Collection & Preparation
2. Model Training & Experimentation
3. Model Evaluation & Validation
4. Model Deployment
5. Monitoring & Observability
6. Model Retraining & Updates

**2. Why is model versioning important?**
Model versioning ensures **reproducibility**, **auditability**, and **reliability**. It allows teams to trace a deployed model directly back to the exact code, hyperparameters, and dataset split used to train it. Additionally, it enables rapid rollbacks to a known good state if a newly deployed model exhibits bugged or degraded behavior in production.

**3. What's the difference between system metrics and model metrics?**

* **System Metrics** measure operational health, performance, and resource usage of the underlying infrastructure (e.g., CPU/GPU utilization, memory, API request rate, error rate, $p_{95}/p_{99}$ latency).
* **Model Metrics** measure the quality, behavior, and accuracy of the model's predictions over time (e.g., prediction distributions, confidence levels, feature drift, classification precision/recall, and concept drift).

**4. What are three deployment strategies for ML models?**

* **Blue-Green Deployment:** Maintaining two identical environments (Old/Blue and New/Green) and instantly switching 100% of incoming traffic to the new environment once verified, allowing instant rollback.
* **Canary Deployment:** Gradually routing a small percentage of production traffic (e.g., 5%) to the new model while monitoring key metrics before rolling it out to 100% of users.
* **Shadow Deployment:** Routing real production traffic to both the old and new model simultaneously, but only returning the old model's predictions to users. This allows risk-free evaluation of performance and latency on live data.

**5. Why is monitoring critical for production ML systems?**
Unlike traditional software that fails loudly with explicit bugs or server errors, ML models can **fail silently**. A model's infrastructure can run perfectly (low latency, zero code crashes) while its predictions become completely inaccurate due to shifting real-world data (data/concept drift) or upstream schema changes. Continuous monitoring is the only way to detect performance degradation in real time.

**6. What infrastructure is needed for the training stage?**

* **Compute:** GPU/TPU clusters, distributed training frameworks, resource schedulers (Kubernetes, SLURM), and spot instances for cost optimization.
* **Experiment Tracking:** Tools to log parameters, metrics, and code states (MLflow, Weights & Biases, TensorBoard).
* **Storage:** Model Registries for model artifacts, object storage for training checkpoints, and metadata databases for experiment parameters.

**7. What is data drift and why does it matter?**
**Data Drift** (or Feature Drift) occurs when the statistical distribution of input data in production changes over time compared to the data used during model training. It matters because machine learning models rely on the assumption that real-world inputs match the distribution of their training sets; significant data drift causes model accuracy and reliability to degrade in production.