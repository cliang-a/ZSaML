# Benchmark Pipeline for Mutation Fitness Prediction

This folder contains the **Benchmark** workflow of the **ZSaML** project. The pipeline implements dataset splitting guided by ensemble ESM scores, extraction and combination of diverse sequence/embedding features, cross-evaluation of 10 machine learning regression models, and statistical aggregation across multiple random seeds.

> 📌 **Data Availability Statement**  
> All raw datasets, input features, and pre-computed model outputs are permanently hosted on Zenodo:  
> 👉 **[https://doi.org/10.5281/zenodo.23550236](https://doi.org/10.5281/zenodo.23550236)**  
> Please download the required input files from Zenodo and place them in the corresponding input directories prior to running the pipeline.

---

## 🛠️ Environment Requirements & Dependencies

This codebase was developed and tested using **Python 3.8+**. The following Python libraries are required:

```bash
pip install numpy pandas scipy scikit-learn xgboost torch transformers tqdm
```
## 📂 Repository & File Structure

```text
Benchmark/
├── data_split/
│   └── data_split.py                       # Splitting dataset based on ensemble ESM score ranking
├── feature_generation/
│   ├── extract_prott5.py                   # Extracting ProtT5 embeddings via Hugging Face model
│   ├── extract_AAindex_onehot_georgiev.py  # Generating AAindex, One-hot, and Georgiev features
│   └── combine_features.py                 # Appending 16-dimensional ESM scores to base feature matrices
├── benchmark_models_evaluation.py          # Training and evaluating 10 regression models across feature sets
├── merge_aggregated_results.py             # Aggregating metrics (mean/std) across multiple random seeds
├── run_pipeline_for_seeds.sh               # Automated bash pipeline runner for multi-seed experiments
└── README.md                               # Documentation file
```

## 📑 Core Script Descriptions

*   **`data_split/data_split.py`**
    *   Computes ensemble ESM-1v scores across 5 models.
    *   Splits the dataset into training (\(N=96\): Top 30%, Bottom 30%, and remaining random 40%) and test sets to ensure sampling coverage and diversity.
*   **`feature_generation/extract_prott5.py`**
    *   Uses `Rostlab/prot_t5_xl_half_uniref50-enc` to extract high-dimensional protein language representations via mean pooling.
*   **`feature_generation/extract_AAindex_onehot_georgiev.py`**
    *   Generates classical sequence-based numerical representations including PCA-derived AAindex (19-dim), One-hot encoding, and Georgiev physicochemical property vectors.
*   **`feature_generation/combine_features.py`**
    *   Concatenates 16 ESM score dimensions horizontally (`np.hstack`) onto base feature matrices (aaindex, onehot, prott5, georgiev) to produce ESM-augmented representations.
*   **`benchmark_models_evaluation.py`**
    *   Evaluates 10 machine learning regression algorithms (Linear, Ridge, Lasso, ElasticNet, RandomForest, XGBoost, KNN, PLSRegression, SVR, ExtraTrees).
    *   Computes Spearman rank correlation, MSE, \(R^2\), and top-20 enrichment metrics (hit rate and recall).
*   **`merge_aggregated_results.py`**
    *   Aggregates simulation logs across multiple seeds and computes average performance and standard deviations (Mean ± Std).
*   **`run_pipeline_for_seeds.sh`**
    *   End-to-end Shell script executing data splitting, feature generation, model benchmarking, and statistical aggregation across seeds (default: 1, 42, 100).
 ## 🚀 Quick Start Guide

To run the full benchmark pipeline locally:

1.  **Download Data:** Download the required input files from Zenodo (DOI: `10.5281/zenodo.23550236`) and restore the expected directory paths (`../Data_for_input/`).

2.  **Run Pipeline:** Execute the shell script in Linux, macOS, or Git Bash:

    ```bash
    bash run_pipeline_for_seeds.sh
  
