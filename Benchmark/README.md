# Benchmark Pipeline for Mutation Fitness Prediction

This folder contains the **Benchmark** evaluation workflow for the **ZSaML** framework. The pipeline implements dataset splitting guided by ensemble ESM scores, extraction and concatenation of diverse sequence encodings, cross-evaluation of 10 machine learning regression algorithms, and statistical aggregation across multiple random seeds.

> 📌 **Data Availability Statement**  
> All raw datasets, input features, and pre-computed model outputs are permanently hosted on Zenodo:  
> 👉 **[https://doi.org/10.5281/zenodo.23550236](https://doi.org/10.5281/zenodo.23550236)**  
> Please download the required input files from Zenodo and place them in the corresponding input directories prior to running the pipeline.

---

## 🛠️ Environment Requirements & Dependencies

This codebase was developed and tested using **Python 3.8+**. The following Python libraries are required:

```bash
pip install numpy pandas scipy scikit-learn torch transformers tqdm aaindex matplotlib
```
## 📂 Repository & File Structure

```text
Benchmark/
├── data_split/
│   └── data_split.py                       # Splitting dataset based on ensemble ESM score ranking
├── feature_generation/
│   ├── extract_prott5.py                   # Extracting ProtT5 embeddings via Hugging Face model
│   ├── extract_AAindex_onehot_georgiev.py  # Generating AAindex, One-hot, and Georgiev feature matrices
│   └── combine_features.py                 # Appending 16 ESM zero-shot score dimensions to base representations
├── benchmark_models_evaluation.py          # Training and evaluating 10 regression algorithms across feature sets
├── merge_aggregated_results.py             # Aggregating metrics (Mean ± Std) across multiple random seeds
├── run_pipeline_for_seeds.sh               # Automated bash pipeline runner for multi-seed experiments
└── README.md                               # Benchmark documentation file
```

## 📑 Core Script Descriptions

*   **`data_split/data_split.py`**
    *   Computes ensemble ESM zero-shot fitness scores across ESM models.
    *   Splits each dataset into a training set (N=96: 28 variants with the highest scores, 28 with the lowest scores, and 40 randomly sampled variants) and a test set to ensure phenotypic coverage and sequence diversity under limited-data conditions.
*   **`feature_generation/extract_prott5.py`**
    *  Uses Rostlab/prot_t5_xl_half_uniref50-enc to extract high-dimensional protein language embeddings via mean pooling.
*   **`feature_generation/extract_AAindex_onehot_georgiev.py`**
    *   Generates classical sequence-based numerical representations including AAindex physicochemical descriptors, One-hot encodings, and Georgiev property vectors.
*   **`feature_generation/combine_features.py`**
    *   Concatenates 16 ESM zero-shot fitness score dimensions horizontally (np.hstack) onto the four baseline representations (AAindex, One-hot, Georgiev, ProtT5) to generate zero-shot fitness-augmented representations.
*   **`benchmark_models_evaluation.py`**
    *   Evaluates 10 machine learning regression algorithms (PLSRegression, Ridge, Lasso, ElasticNet, BayesianRidge, Huber, KNN, DecisionTree, RandomForest, ExtraTrees).
    *  Computes evaluation metrics including Spearman's ρ, RMSE, R², as well as virtual screening metrics (Top-10% Hit Rate and Top-10% Recall).
*   **`merge_aggregated_results.py`**
    *   Aggregates simulation logs across multiple random seeds and calculates average performance and standard deviations (Mean ± Std).
*   **`run_pipeline_for_seeds.sh`**
    *  End-to-end Shell script executing data splitting, feature generation, model benchmarking, and statistical aggregation across seeds.
      
 ## 🚀 Quick Start Guide

To run the full benchmark pipeline locally:

1.  **Download Data:** Download the required input files from Zenodo (DOI: `10.5281/zenodo.23550236`) and restore the expected directory paths (`../Data_for_input/`).

2.  **Run Pipeline:** Execute the shell script in Linux, macOS, or Git Bash:

    ```bash
    bash run_pipeline_for_seeds.sh
  
