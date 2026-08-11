# System Call Intrusion Detection

## Overview

This project implements a process-level intrusion detection approach using
system call sequences. The goal is to learn normal process behavior and
identify potentially intrusive processes based on their similarity to normal
processes.

The project is implemented as a Jupyter/Google Colab notebook and uses a
TF-IDF-inspired representation, cosine similarity, and k-Nearest Neighbors
(kNN) anomaly scoring.

## Objective

The model is designed as an anomaly detection system:

- Normal processes are used for training.
- The test set contains both normal and intrusive processes.
- Each process is represented using its system-call sequence.
- Similarity to normal training processes is used to identify anomalous
  behavior.

## Methodology

The project follows this pipeline:

1. Load and validate the process-level dataset.
2. Standardize process labels and dataset split values.
3. Separate normal training processes from the test set.
4. Tokenize space-separated system-call sequences.
5. Build a system-call vocabulary using normal training processes.
6. Calculate TF-IDF-inspired syscall weights.
7. Convert each process into a weighted feature vector.
8. Calculate cosine similarity between test and training processes.
9. Generate a kNN-based anomaly score using the most similar normal
   processes.
10. Evaluate multiple values of `k`.
11. Evaluate intrusion detection using ROC-AUC, TPR, FPR, Precision,
    Recall, and F1 score.
12. Analyze score distributions and threshold sensitivity.
13. Examine unseen system calls in the test data.
14. Compare alternative IDF formulations.

## Dataset

The dataset contains process-level system call sequences with labels and
train/test split information.

The required columns are:

- `process_id`
- `label`
- `split`
- `syscalls`

The training population consists of normal processes only. The test
population contains both normal and intrusive processes.

The notebook currently reports 380 normal training processes, 500 test
processes, and 95 distinct system calls in the training vocabulary.

> **Dataset note:** The dataset itself is not included in this repository
> unless it is explicitly permitted for redistribution. The notebook expects
> the file `process_syscalls_final_k_separated.csv` to be available in the
> working directory.

## Feature Representation

System-call sequences are tokenized into individual syscall tokens.

The project supports two IDF-style weighting approaches:

- `collection_count`
- `doc_freq`

Term frequency is L2-normalized before the IDF weighting is applied.

The resulting vectors are compared using cosine similarity.

## Anomaly Detection

The kNN anomaly score is based on the average cosine similarity between a
test process and its `k` most similar normal training processes.

Interpretation:

- Higher similarity score → more similar to normal behavior
- Lower similarity score → more anomalous behavior
- A process below the selected threshold is classified as intrusive

## Evaluation

The notebook evaluates the model using:

- ROC-AUC
- True Positive Rate (TPR)
- False Positive Rate (FPR)
- Precision
- Recall
- F1 Score
- Confusion Matrix

Additional analyses include:

- Process label distribution
- Data quality checks
- Unseen syscall rates
- Score distributions for normal and intrusive processes
- Performance across different `k` values
- Threshold sensitivity
- Comparison of IDF methods

## Repository Structure

```text
system-call-intrusion-detection/
│
├── README.md
├── MLProject.ipynb
│
└── data/
    └── README.md
```

## Technologies

- Python
- NumPy
- Pandas
- Matplotlib
- Scikit-learn
- Google Colab
- Jupyter Notebook

## How to Run

### Option 1: Google Colab

1. Open `MLProject.ipynb`.
2. Upload or provide access to `process_syscalls_final_k_separated.csv`.
3. Run the notebook from top to bottom.

### Option 2: Jupyter Notebook

Install the required Python packages:

```bash
pip install numpy pandas matplotlib scikit-learn
```

Then open:

```text
MLProject.ipynb
```

and run the cells sequentially.

## Important Note About Evaluation

The test set contains the labels used to measure final detection performance.
Metrics such as ROC-AUC, TPR, FPR, Precision, Recall, and F1 should therefore
be interpreted as evaluation results on the available test set.

For a production-quality anomaly detection system, model and threshold
selection should ideally be calibrated using a separate validation procedure
rather than optimizing directly on the final test labels.

## Project Status

Completed experimental implementation of a system-call-based anomaly
detection pipeline with multiple evaluation and analysis components.
