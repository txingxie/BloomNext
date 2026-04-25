# BloomNext: Early Prediction of Harmful Algal Blooms

BloomNext is a machine learning project for predicting harmful algal blooms caused by *Karenia brevis* in Florida coastal waters.

## Overview

Harmful algal blooms, or HABs, can damage marine ecosystems, harm fisheries, and affect public health. In Florida, blooms of *Karenia brevis* are responsible for red tide events, which can lead to fish kills, beach closures, and respiratory irritation.

This project uses NOAA HABSOS field observations to build a binary classification model called **BloomNext**, which predicts whether a bloom will occur in the next weekly observation.

## Dataset

Source:
- NOAA HABSOS, the Harmful Algal Blooms Observing System

Filtering and preprocessing:
- Filtered to Florida observations
- Filtered to *Karenia brevis*
- Raw dataset size: 211,834 observations
- Final filtered size: 205,168 observations
- Date range: 1953 to 2024

Aggregation:
- Spatial binning into 0.25 degree grid cells
- Weekly aggregation of observations
- Resulting weekly dataset: 53,998 cell-week records
- Unique grid cells: 498

Final modeling dataset:
- 52,090 samples
- Positive class rate: 7.3%

## Target Variable

We define a binary target called **BloomNext**:

- `1` if the maximum *K. brevis* cell count exceeds 100,000 cells/L in the next weekly observation
- `0` otherwise

## Features

We engineered 13 features from the weekly aggregated data:

- current-week max log cell count
- current-week mean log cell count
- current-week bloom indicator
- previous-week max log cell count
- previous-week bloom indicator
- rolling 4-week max log cell count
- rolling 4-week mean log cell count
- rolling 4-week bloom count
- week-over-week change in max log cell count
- water temperature
- month sine encoding
- month cosine encoding
- neighbor bloom count

These features were designed to capture recent bloom persistence, short-term temporal trends, seasonality, and spatial spread across neighboring grid cells.

## Models Compared

We trained and evaluated the following models:

- Logistic Regression
- Decision Tree
- SVM with linear kernel
- SVM with RBF kernel
- Random Forest
- Gradient Boosting
- Neural Network (MLP)
- Random baseline

## Evaluation

We used a time-based split to reflect a real forecasting setting:

- Training years: 1954 to 2022
- Test years: 2023 to 2024

Primary evaluation metric:
- **PR-AUC**: Precision-Recall Area Under the Curve

Additional metrics:
- F1 score
- Precision
- Recall
- MAE
- RMSE

PR-AUC was chosen because bloom events are relatively rare, making this an imbalanced classification problem.

## Results Summary

Main findings:
- All trained models outperformed the random baseline
- Random baseline test PR-AUC: 0.051
- Best cross-validation PR-AUC: Gradient Boosting, 0.565
- Best test PR-AUC: Decision Tree, 0.492
- Gradient Boosting test PR-AUC: 0.491
- Best test F1 score: Gradient Boosting, 0.486

Interpretation:
- Gradient Boosting provided the best balance of precision and recall
- Decision Tree slightly edged out Gradient Boosting on test PR-AUC
- Tree-based models generally outperformed the neural network on this structured tabular dataset
- Current-week max log cell count was the strongest predictive feature
- Neighbor bloom count and seasonal signals were also informative

## Repository Structure

```text
.
├── archive/
│   ├── BloomNext_Final.ipynb
│   ├── BloomNext_Final_OLD.ipynb
│   └── BloomNext_HAB_Project.ipynb
├── data/
│   └── habsos_20240430.csv
├── figures/
│   ├── eda_plots.png
│   ├── spatial_plot.png
│   ├── pca_umap.png
│   ├── model_comparison.png
│   ├── pr_curves.png
│   ├── confusion_matrices.png
│   └── feature_importance.png
├── reports/
│   ├── CS4262 Group 15 Final Report Backup.docx
│   ├── Project Proposal.docx
│   └── Project Update.docx
├── BloomNext_Final.ipynb
└── README.md
```

## Installation

Clone the repository and install dependencies:

```bash
git clone https://github.com/ConnorBrug/CS4262Group15Project.git
cd CS4262Group15Project
pip install pandas numpy matplotlib scikit-learn seaborn umap-learn
```

## How to Run

1. Download the HABSOS CSV file.
2. Place `habsos_20240430.csv` inside the `data/` folder.
3. Open the main notebook:
   - `BloomNext_Final.ipynb`
4. Run all cells in order.

The notebook will:
- load and clean the raw data
- aggregate observations into weekly grid-cell records
- engineer temporal and spatial features
- train and evaluate multiple machine learning models
- save generated plots into the `figures/` folder

## Outputs

Running the notebook produces:
- exploratory data analysis plots
- spatial distribution plot
- PCA and UMAP feature-space projections
- PR-AUC and F1 comparison plots
- precision-recall curves
- confusion matrices
- feature importance plots
- summary tables for the final report

## Limitations

Some important limitations of the current version:
- only HABSOS field observations were used in the final model
- satellite-derived variables such as chlorophyll and sea surface temperature were not incorporated
- temporal sampling is sparse and irregular in some regions
- some environmental variables have missing values

## Future Work

Potential next steps include:
- incorporating satellite features such as chlorophyll-a and sea surface temperature
- testing richer spatiotemporal features
- experimenting with different grid sizes
- improving handling of missing and irregularly sampled data
- trying ensemble stacking of top-performing models

## Contributors

- Trevor Xing-Xie: Data acquisition and initial inspection of the HABSOS dataset
- Nirmay Bhanderi: Feature engineering, exploratory analysis, PCA and UMAP visualization
- Connor Brugger: Model training pipeline, hyperparameter tuning, evaluation framework, and report writing

## Resources

This project used:
- scikit-learn for machine learning models and evaluation
- umap-learn for dimensionality reduction visualization
- Claude to assist with code generation, formatting, and workflow support

All generated code was reviewed, tested, and adapted by the project team.

## License

Add a license here if you want the repository to be open source.
