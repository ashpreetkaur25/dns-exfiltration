# CSI5388 / ELG5271 – Assignment 2  
## DNS Data Exfiltration Detection (Static & Dynamic Models)

**University of Ottawa | Winter 2025/2026**

This repository contains the implementation and evaluation for **DNS data exfiltration detection** using:
- **Part I:** Static binary classification (Benign vs Attack) on batch DNS data  
- **Part II:** Dynamic/online learning with Kafka streaming and window-based retraining  

Evaluation matrices and detailed results are included in the Jupyter notebooks and in any accompanying report document.

---

## Project Overview

| Component | Description |
|-----------|-------------|
| **Static model** | Train Random Forest and Gradient Boosting on `Static_dataset.csv`; evaluate with F1, ROC-AUC, PR-AUC, and confusion matrices. |
| **Dynamic model** | Consume DNS records from Kafka in windows; compare a fixed static model vs a model that retrains when F1 drops (e.g. > 2%). |
| **Data pipeline** | Producer notebook pushes `Kafka_dataset.csv` into Kafka; consumer (or dynamic model notebook) reads from the topic. |
| **Infrastructure** | Docker Compose runs Zookeeper and Apache Kafka for streaming. |

---

## Repository Structure

```
Assignment_2/
├── README.md
├── AshpreetKaur_300411645_Assignment2_Report copy.docx   # Report with evaluation matrices & results
├── setup_docs/
│   ├── Static_model copy.ipynb    # Part I: static classification & evaluation
│   ├── Dynamic_model copy.ipynb    # Part II: online learning with Kafka
│   ├── dns_kafka_producer.ipynb    # Push CSV data to Kafka topic
│   ├── dns_kafka_consumer.ipynb    # Consume from Kafka (simple consumer)
│   ├── docker-compose.yml          # Zookeeper + Kafka
│   ├── docker_script.sh            # Start Docker and create topics (Unix)
│   ├── docker_script.bat           # Start Docker and create topics (Windows)
│   ├── requirements.txt           # Python dependencies
│   ├── Static_dataset.csv          # Batch dataset for Part I (if included)
│   └── Kafka_dataset.csv           # Streaming dataset for Part II (if included)
```

---

## Evaluation Metrics & Results (Part I – Static Model)

Metrics are chosen for an imbalanced binary task (Benign vs Attack):

- **Primary:** F1-Score (macro) – balances precision and recall  
- **Secondary:** ROC-AUC, PR-AUC (Average Precision)  
- **Display:** Confusion matrix, ROC curves, Precision–Recall curves  

### Summary (from notebook runs)

| Model              | F1 (macro) | ROC-AUC | PR-AUC | Accuracy (test) |
|--------------------|------------|---------|--------|------------------|
| Random Forest      | **0.863**  | 0.805   | 0.758  | ~0.83            |
| Gradient Boosting  | **0.862**  | 0.802   | 0.753  | ~0.82            |

- **Best CV F1-Score:** 0.8632 (from grid search).  
- Train/test split: 80% / 20% (~53K test samples).  
- Full classification reports, confusion matrices, ROC and PR curves are in **`setup_docs/Static_model copy.ipynb`**.  

The accompanying **report** (if provided separately) contains the same evaluation matrices and a written analysis of these results.

---

## Setup

### 1. Python environment

```bash
cd setup_docs
pip install -r requirements.txt
```

Main dependencies: `pandas`, `kafka-python`, `kafka`, `tqdm`, plus standard ML stack used in the notebooks (`scikit-learn`, `matplotlib`, `seaborn`, `joblib`, etc. – install as needed).

### 2. Kafka (Docker)

From `setup_docs/`:

**Linux/macOS:**
```bash
./docker_script.sh
```

**Windows:**
```cmd
docker_script.bat
```

This starts Zookeeper and Kafka and creates topics `ml-raw-dns` and `ml-dns-predictions`.

### 3. Run order

1. Start Kafka (Docker scripts above).  
2. **Part I:** Run `Static_model copy.ipynb` to train and evaluate; it saves `best_static_model.pkl`, `scaler.pkl`, `selected_features.pkl`.  
3. **Part II:** Run `Dynamic_model copy.ipynb` (loads the static artefacts and consumes from Kafka).  
4. To feed data: run `dns_kafka_producer.ipynb` to push `Kafka_dataset.csv` into the Kafka topic; the dynamic notebook or `dns_kafka_consumer.ipynb` can then consume it.

---

## Datasets

- **Static_dataset.csv** – Used for Part I (batch training/evaluation).  
- **Kafka_dataset.csv** – Used for Part II (streaming via producer notebook).  

Place these in `setup_docs/` if they are not already there. Dataset descriptions and feature lists are in the notebooks and in the report.

---

## Report

The **evaluation matrices and results** are:

- In the notebook outputs: **`setup_docs/Static_model copy.ipynb`** (and **`Dynamic_model copy.ipynb`** for Part II).  
- In the written report: **`AshpreetKaur_300411645_Assignment2_Report copy.docx`** – full analysis and evaluation matrices.

---

## Push to GitHub

From the project root (`Assignment_2/`):

1. Create a **new repository** on [GitHub](https://github.com/new) (e.g. `dns-exfiltration-assignment2`). Do **not** initialize with a README (this repo already has one).

2. Add the remote and push:

   ```bash
   git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
   git branch -M main
   git push -u origin main
   ```

   Replace `YOUR_USERNAME` and `YOUR_REPO_NAME` with your GitHub username and repository name.

---

## License & Citation

Course assignment – University of Ottawa. Use according to your course and institutional policies.
