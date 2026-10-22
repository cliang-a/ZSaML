# Machine Learning Workflow for Single Mutants (NHAB)

This folder contains the source code and two-stage feature selection pipelines for predicting the fitness of single-point protein mutants in the **NHAB** dataset using Partial Least Squares Regression (PLSR), Fast Fourier Transform (FFT) encoding of physicochemical descriptors (AAindex), and ESM-derived zero-shot fitness scores.

> 📌 **Data Availability Statement**  
> All raw datasets, input CSVs, pre-computed feature embeddings, and output model files are permanently archived on Zenodo:  
> 👉 **[https://doi.org/10.5281/zenodo.23550236](https://doi.org/10.5281/zenodo.23550236)**  
> Please download the required input files from Zenodo and restore them in the `Data_for_input/` folder before running the scripts.

---

## 🛠️ Environment Requirements & Dependencies

This workflow was developed using **Python 3.8+**. The following libraries are required:

```bash
pip install numpy pandas scipy scikit-learn matplotlib tqdm aaindex
```

## 📂 Repository & Directory Structure
```text
ML_Single_mutant/
├── FFT.py                          # Fast Fourier Transform (FFT) sequence encoding with AAindex
├── aaindex_plsr_selection.py       # Stage 1: Iterative AAindex property selection via PLSR
├── esm_plsr_selection.py           # Standalone ESM feature forward selection via PLSR
├── aaindex_ESM_pls.py              # Stage 2: Combined greedy selection (Fixed AAindex + ESM)
├── prediction.py                   # Automated prediction on unseen single mutants using trained PLS models
├── Data_for_input/                 # Input datasets directory (from Zenodo)
├── Data_for_output/                # Output directory for checkpoints, metrics, and plots
└── README.md                       # Documentation file
```

## 📑 Core Script Descriptions

*   **`FFT.py`**
    *   Computes Fast Fourier Transform (scipy.fftpack.fft) spectrum encodings on protein sequences using specified aaindex1 physicochemical properties with mean subtraction.
*   **`aaindex_plsr_selection.py`**
    *   **Stage 1 Selection:** Performs forward greedy feature selection over 566 AAindex physicochemical properties using Partial Least Squares Regression (PLSR).
    *   Optimizes the number of principal components via 5-fold cross-validation (CV_MSE) and saves round caches (aaindex_round_cache.pkl) alongside test set metrics.
*   **`esm_plsr_selection.py`**
    *   **Standalone ESM Selection:** Executes Sequential Forward Selection (Greedy Search) across 16 ESM score dimensions to identify optimal ESM combinations independently.
*   **`aaindex_ESM_pls.py`**
    *   **Stage 2 Selection:** Loads pre-selected AAindex subsets from Stage 1 and incorporates 16 ESM-derived zero-shot fitness scores through greedy forward selection.
    *   Exports progress plots ( R² curves) in PNG and PDF formats and updates test set prediction logs.
*   **`prediction.py`**
    *   Loads trained PLS model checkpoints (e.g., best_model_round1.pkl), reads selected ESM feature lists (best_feats_round1.txt), constructs combined input matrices (AAindex-FFT + ESM), and generates predicted fitness scores (expm1 inverse log transformed).   


## 🚀 Quick Start Guide

1.  **Prepare Data:** Download dataset files from Zenodo (DOI: `10.5281/zenodo.23550236`) and place `Train_set.csv` and `Test_set.csv` into `./Data_for_input/`.

2.  **Run Feature Selection Pipelines:**

    ```bash
    # Stage 1: Greedy AAindex selection via PLSR
    python aaindex_plsr_selection.py --n_rounds 20

    # Stage 2: Combine fixed AAindex rounds with greedy ESM feature selection
    python aaindex_ESM_pls.py --start_round 1 --end_round 20
     ```
3.  **Run Single Mutant Fitness Prediction:**

    ```bash
    python prediction.py

     ```
