# Zero-shot fitness-augmented machine learning enables data-efficient enzyme engineering

This repository contains the source code, prospective engineering workflows, and benchmark pipelines for the paper:  
**"Zero-shot fitness-augmented machine learning enables data-efficient enzyme engineering"**

## 📖 Overview

Machine learning (ML) can accelerate enzyme engineering, but its performance under data-limited conditions depends strongly on sequence representations. Protein language models (PLMs) provide evolutionary information through zero-shot prediction fitness scores, yet the extent to which these scores improve supervised prediction across different levels of mutational complexity remains unclear. 

We developed the **Zero-shot Fitness-Augmented Machine Learning (ZSaML)** framework, which integrates PLM-derived zero-shot fitness scores with physicochemical properties for protein encodings. This repository is structured into three main components:
1. **`NHAB/ML_Single_mutant/`**: Prospective single-variant engineering of nitrile hydratase (NHAB) using a two-stage greedy feature selection framework (FFT-PLSR).
2. **`NHAB/ML_Combinatorial_mutant/`**: Combinatorial variant prediction and local/global virtual screening of a 331,776 sequence space using optimized physicochemical representations and Gradient Boosting regression.
3. **`Benchmark/`**: A systematic evaluation pipeline benchmarking zero-shot fitness augmentation across 10 public Deep Mutational Scanning (DMS) datasets, 3 combinatorial datasets, and 10 regression algorithms.

---

## 📌 Data Availability Statement

Due to GitHub repository size limits, raw experimental datasets, pre-computed PLM embeddings, intermediate feature matrices, and trained model checkpoints are permanently archived on **Zenodo**:

👉 **Zenodo DOI: [https://doi.org/10.5281/zenodo.23550236](https://doi.org/10.5281/zenodo.23550236)**

*Please download the input files from Zenodo and place them into the respective `Data_for_input/` directories before executing the code.*

---

## 🛠️ Environment & Requirements

Recommended runtime: **Python 3.8+**. You can install all required dependencies using:

```bash
pip install numpy pandas scipy scikit-learn xgboost torch transformers tqdm aaindex matplotlib
```

📂 Repository Architecture

```text
ZSaML/
├── NHAB/                               # Prospective engineering workflows for NHAB
│   ├── ML_Single_mutant/               # Single-point mutant fitness prediction & screening
│   │   ├── FFT.py                      # Fast Fourier Transform encoding for AAindex descriptors
│   │   ├── aaindex_plsr_selection.py   # Stage 1: Greedy AAindex descriptor selection via PLSR
│   │   ├── aaindex_ESM_pls.py          # Stage 2: Combined PLSR greedy selection with ESM scores
│   │   ├── esm_plsr_selection.py       # Standalone ESM forward selection via PLSR
│   │   ├── prediction.py               # Single mutant fitness inference and ranking
│   │   └── README.md                   # Single mutant workflow documentation
│   │
│   └── ML_Combinatorial_mutant/        # Combinatorial mutant fitness prediction & virtual screening
│       ├── FFT.py                      # FFT encoding module
│       ├── AAindex_model_screening.py  # Stage 1: Model & AAindex descriptor screening (14 algorithms)
│       ├── AAindex_esm_screening.py    # Stage 2: Nested ESM zero-shot feature screening
│       ├── prediction.py               # Memory-optimized batch prediction (Gradient Boosting)
│       └── README.md                   # Combinatorial mutant workflow documentation
│
├── Benchmark/                          # Benchmark evaluation pipeline across public DMS datasets
│   ├── data_split/                     # Dataset splitting guided by ESM zero-shot scores
│   ├── feature_generation/             # Baseline encodings (ProtT5, AAindex, One-hot, Georgiev) & ESM fusion
│   ├── benchmark_models_evaluation.py  # Evaluation of 10 ML regression algorithms
│   ├── merge_aggregated_results.py     # Multi-seed result statistical aggregation (Mean ± Std)
│   ├── run_pipeline_for_seeds.sh       # Automated pipeline runner for multi-seed experiments
│   └── README.md                       # Detailed Benchmark documentation
│
└── README.md                           # Main repository README (this file)
```

## 🚀 Overview of Sub-Modules

### 1. NHAB Single Mutants (/NHAB/ML_Single_mutant)

*   **Purpose:** Predicts and prioritizes beneficial single substitutions in NHAB under data-constrained conditions.
*   **Key Features:** Implements a two-stage greedy feature selection under the FFT-PLSR framework, combining AAindex physicochemical descriptors with 16 ESM-derived zero-shot fitness scores. This hybrid representation achieved a 53.3% success rate in identifying top-30 variants, yielding high-performance variants such as T156K (2.59-fold improvement in acrylamide tolerance)

      **Quick Run:**
      ```bash
      cd NHAB/ML_Single_mutant
      python aaindex_plsr_selection.py --n_rounds 20
      python aaindex_ESM_pls.py --start_round 1 --end_round 20
      python prediction.py
      ```

### 2. NHAB Combinatorial Mutants (/NHAB/ML_Combinatorial_mutant)

*   **Purpose:** Optimizes representations and conducts virtual screening for higher-order combinatorial variants
*   **Key Features:** Combines AAindex descriptors optimized via model screening (using 3 descriptors: TANS770107, GEIM800111, PALJ810105) with a Gradient Boosting (GB) regression model. Used to screen a theoretical search space of 331,776 sequences, successfully discovering the highly tolerant quintuple mutant M1 (H10Q/G83A/T156R/K306N/V403D) with a 5.63-fold improvement in tolerance

      **Quick Run:**
      ```bash
      cd NHAB/ML_Combinatorial_mutant
      python AAindex_model_screening.py
      python AAindex_esm_screening.py
      python prediction.py
      ```

### 3. Benchmark Pipeline (/Benchmark)

*   **Purpose:** Systematically evaluates the generalizability of zero-shot fitness augmentation across 10 public DMS datasets and 3 combinatorial datasets.
*   **Key Features:** Evaluates Spearman's ρ, RMSE, R², Top-10% Hit Rate, Top-10% Recall, and UMAP-based local neighborhood functional coherence across 10 machine learning regression algorithms (PLSRegression, Ridge, Lasso, ElasticNet, BayesianRidge, Huber, KNN, DecisionTree, RandomForest, ExtraTrees)

    **Quick Run:**
      ```bash
      cd Benchmark
      bash run_pipeline_for_seeds.sh
      ```

## 📖 Citation

If you use this codebase or the associated datasets in your research, please cite our paper and Zenodo repository:

```bibtex
@article{cheng2026zeroshot,
  title={Zero-shot fitness-augmented machine learning enables data-efficient enzyme engineering},
  author={Cheng, Liang and Song, Zhongdi and Zhang, Hongyu and Wu, Jianping and Yang, Lirong and Jiang, Ling and Yu, Haoran},
  journal={Submitted},
  year={2026}
}
