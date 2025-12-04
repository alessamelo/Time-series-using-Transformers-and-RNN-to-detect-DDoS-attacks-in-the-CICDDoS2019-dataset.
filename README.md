#  Time-Series Modeling of Network Traffic for DDoS Detection

### Using Transformers and RNNs on the CICDDoS2019 Dataset

##  Project Overview

This project evaluates **RNN (LSTM)** and **Transformer Encoder** architectures to detect **Distributed Denial of Service (DDoS)** attacks using the **CICDDoS2019** dataset.

Network traffic is processed as a **multivariate time series**, with each model trained to classify sequences as **benign** or **attack**.
All experiments were conducted inside **Jupyter notebooks**:

* `EDA.ipynb` → Data cleaning, visualization, feature selection
* `Training.ipynb` → Sequence construction, model training, metrics & confusion matrices

This README summarizes the entire pipeline and key findings.
Full paper referenced:


---

## 📂 Repository Structure

```
├── data/
│   └── processed/         # Cleaned CSV 
├── notebooks/
│   ├── EDA.ipynb          # Exploratory data analysis + feature pruning
│   └── Training.ipynb     # LSTM & Transformer training + evaluation
└── README.md
```

---

##  Dataset: CICDDoS2019

This dataset provides real network traffic flows labeled as **benign** or specific **DDoS subtypes**.

### Why this dataset?

* High-dimensional flow-level statistics
* Time-dependent variables (IAT, packet rates, flow bursts)
* Realistic attack simulation
* Large-scale (400k+ rows)

More details appear in the paper:


---

##  Workflow Overview

###  **1. Exploratory Data Analysis (`EDA.ipynb`)**

#### **Data Cleaning**

* Removed index/“Unnamed” columns
* Dropped zero-only or constant TCP flag features
* Eliminated low-variance features
* Prevented data leakage by removing `Label` and future-derived variables

#### **Correlation Analysis**

* Generated correlation heatmap
* Identified redundant groups (packet lengths, segment sizes, subflow metrics, idle times)
* Reduced multicollinearity

---

###  **2. Model Training (`Training.ipynb`)**

This notebook includes:

#### **Sequence Construction**

* Sliding window segmentation
* Final input shape: `(batch_size, T, d)`
* Standardization of all numerical features
* Balanced sampling of benign vs attack sequences

#### **Models Implemented**

* **RNN (LSTM)**
* **Transformer Encoder**

#### **Training Setup**

| Parameter      | Value                     |
| -------------- | ------------------------- |
| Optimizer      | Adam                      |
| Loss           | Categorical Cross-Entropy |
| Early Stopping | Enabled                   |
| Hardware Used  | NVIDIA RTX 4060           |
| Window Size T  | configurable              |

---

##  Model Descriptions

###  **RNN (LSTM)**

Captures short- and medium-term sequential patterns typical in repetitive attack bursts.

**Advantages:**

* Lower computational cost
* Fast convergence
* Lower false negatives
* Very stable validation loss

---

###  **Transformer Encoder**

Uses self-attention to capture global dependencies across the entire sequence.

**Advantages:**
* Parallelizable
* Good for long-range temporal structure

**Challenges in this dataset:**

* Higher training time
* More false negatives
* Requires stronger regularization / hyperparameter tuning

---

##  Results

###  **RNN (LSTM)**

| Metric         | Score                     |
| -------------- | ------------------------- |
| Accuracy       | **0.9998**                |
| F1-score       | **0.9998**                |
| Inference Time | ~1–2 ms                   |
| Training Time  | 1m 28s                    |
| Error Pattern  | Almost no false negatives |

###  **Transformer**

| Metric         | Score                                               |
| -------------- | --------------------------------------------------- |
| Accuracy       | 0.9981                                              |
| F1-score       | 0.9981                                              |
| Inference Time | 1–7 ms                                              |
| Training Time  | 6m 11s                                              |
| Error Pattern  | More false negatives (attacks classified as benign) |

---

##  Confusion Matrix Insights

* **RNN** → highly stable, nearly perfect classification
* **Transformer** → good accuracy but weaker recall for attack class

This is critical:
⚠ **False negatives in cybersecurity are dangerous**, since undetected attacks continue.

---

##  Limitations

* Only CICDDoS2019 dataset tested
* No oversampling (SMOTE not applied)
* Dataset generated in a controlled environment
* Fixed window size
* Transformer not exhaustively tuned

---

##  Future Work

* Test models on CICIDS2017, MAWI, UNSW-NB15
* Apply SMOTE or ADASYN
* Try:

  * Bidirectional LSTM
  * TCN (Temporal Convolutional Networks)
  * Hybrid CNN–LSTM or CNN–Transformer
* Use attention-efficient Transformer variants:

  * LogSparse
  * Informer
  * Performer

---

## 👩‍💻 Authors

**Alessa N. Melo-Limaico**
Yachay Tech University
ORCID: 0009-0003-5715-8482

**Daniel I. Troya-Atancuri**
Yachay Tech University
ORCID: 0000-0001-6939-6339

---

##  Reference Paper

Full paper:
