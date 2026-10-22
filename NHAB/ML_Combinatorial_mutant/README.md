# Machine Learning Workflow for Combinatorial Mutants (NHAB)

This folder contains the source code and prediction pipelines for higher-order combinatorial mutants in the **NHAB** dataset using machine learning models, Fast Fourier Transform (FFT) encoding of physical-chemical properties (AAindex), and ESM-based evolutionary representation screening.

> 📌 **Data Availability Statement**  
> All raw datasets, input CSVs, pre-computed feature embeddings, and output model files are permanently archived on Zenodo:  
> 👉 **[https://doi.org/10.5281/zenodo.23550236](https://doi.org/10.5281/zenodo.23550236)**  
> Please download the necessary input files from Zenodo and restore them in the `Data_for_input/` folder before running the scripts.

---

## 🛠️ Environment Requirements & Dependencies

This workflow was developed using **Python 3.8+**. The following libraries are required to run the scripts:

```bash
pip install numpy pandas scipy scikit-learn matplotlib tqdm aaindexx
```

## 📂 Repository & Directory Structure**
```text
ML_Combinatorial_mutant/
├── FFT.py                          # Fast Fourier Transform (FFT) sequence encoding with AAindex
├── AAindex_model_screening.py      # Stage 1: Model-wise greedy AAindex feature selection
├── AAindex_esm_screening.py        # Stage 2: Nested greedy screening incorporating ESM features
├── prediction.py                   # Memory-optimized batch prediction & distribution analysis
├── Data_for_input/                 # Directory for input datasets (from Zenodo)
├── Data_for_output/                # Output directory for models, logs, and prediction results
└── README.md                       # Documentation file
```

## 📑 Core Script Descriptions

*   **`FFT.py`**
    *   Implements numerical sequence spectrum transformations via Fast Fourier Transform (scipy.fftpack.fft).
    *   Encodes protein amino acid sequences using specified aaindex1 physicochemical property indices with zero-mean normalization.
*   **`AAindex_model_screening.py`**
    *   **Stage 1 Selection:** Performs greedy feature selection over all AAindex1 property codes across 14 regression models (PLSRegression, Ridge, Lasso, ElasticNet, BayesianRidge, Huber, KNN, DecisionTree, RandomForest, ExtraTrees, AdaBoost, GradientBoosting, SVR, LinearRegression).
    *   Evaluates feature subsets using 5-fold cross-validation (CV_MSE) to identify optimal physicochemical representations (e.g., using 3 optimized AAindex descriptors: TANS770107, GEIM800111, PALJ810105).
*   **`AAindex_esm_screening.py`**
    *   **Stage 2 Selection:** Evaluates whether incorporating ESM-derived zero-shot fitness scores improves combinatorial variant predictions.
    *  Performs a nested greedy search over 16 pre-computed Evolutionary Scale Modeling (ESM) features and outputs benchmark reports.
*   **`prediction.py`**
    *   Loads trained pipelines (e.g., Gradient Boosting best models) and executes memory-optimized batch predictions (batch_size=500) on unseen multi-point mutant libraries (Combinatorial_mutant_squence.csv).
    *  Performs inverse log transformations ($\text{expm1}$), ranks predicted variant fitness, and exports statistical distribution plots along with top-performing candidate lists (top_20_predictions.csv, top_100_predictions.csv).
 
## 🚀 Quick Start Guide

1.  **Prepare Data:** Download the dataset files from Zenodo (DOI: 10.5281/zenodo.23550236) and place Train_set.csv and Test_set.csv inside ./Data_for_input/.

2.  **Run Feature Selection & Model Screening:**

    ```bash
    # Step 1: Greedy AAindex feature selection & model screening
    python AAindex_model_screening.py

    # Step 2: Nested ESM feature screening
    python AAindex_esm_screening.py
    ```

3.  **Run Batch Prediction:**

    ```bash
    python prediction.py
    ```
    
