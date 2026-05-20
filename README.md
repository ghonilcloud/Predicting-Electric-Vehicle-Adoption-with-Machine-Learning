# Predicting Electric Vehicle Adoption

This repository contains the notebook workflow used to build the county-level dataset and machine learning models for the research paper, [*Predicting Electric Vehicle Adoption: A Machine Learning Approach Using Socioeconomic and Infrastructure Data*](paper/B5_Electric_Vehicle_Population.pdf).

The project studies how strongly EV adoption is associated with four county-level factors:

- `GDP`
- `Population`
- `Population Density`
- `Total Chargers`

The prediction target is `Number of Vehicles`, representing the EV population at county level.

## Repository Contents

- `FODS_EV_Wash_California_Florida.ipynb`
  Preprocesses and merges the Washington, California, and Florida datasets into a unified machine learning table.
- `FODS_EV_Texas_NY.ipynb`
  Preprocesses and merges the Texas and New York datasets into a second machine learning table.
- `FODS_EV_Models.ipynb`
  Combines the processed tables, removes duplicates, trains the regression models, and evaluates results.
- `paper/B5_Electric_Vehicle_Population.pdf`
  Research paper based on this analysis.

## What The Data Pipeline Does

The notebooks do more than simple loading and plotting. They standardize several public datasets from different U.S. states so they can be analyzed together at the county level.

For each state, the workflow builds a county-level table by merging:

- EV population or registration data
- charger or charging station counts
- county GDP data
- county population, land area, and population density data

The merged output is normalized into the same schema across states:

`County`, `GDP`, `Population`, `Land Area`, `Population Density`, `Total Chargers`, `Number of Vehicles`

The two preprocessing notebooks export intermediate datasets:

- `ML_TABLE.csv`
- `ML_TABLE2.csv`

The modeling notebook then concatenates both tables, sorts by EV count, drops duplicates, fills missing values, and trains the final models on the combined dataset. Based on the saved notebook output, the final modeling table contains `479` county rows after concatenation and deduplication.

## How The Data Was Used In The Research Paper

This repository is the implementation behind the paper, not a separate side project.

The paper uses the processed county-level dataset created in these notebooks to test whether EV adoption can be predicted from socioeconomic and infrastructure variables. The merged table is the core experimental dataset used for:

- feature construction
- cross-state comparison
- regression model training
- model performance reporting
- feature importance analysis

In the paper, the same four predictors used in the notebook code are the variables used to explain EV adoption:

- `GDP`
- `Population`
- `Population Density`
- `Total Chargers`

The modeling section in `FODS_EV_Models.ipynb` directly supports the paper's results tables, prediction comparisons, and feature-importance discussion. The paper's central finding is also reflected in the notebook outputs: charger availability is consistently the strongest predictor across the best-performing models.

## Models Used

The project evaluates four regression approaches:

- XGBoost
- Random Forest Regressor
- Linear Regression
- Support Vector Regression

The evaluation metrics used in the notebook and reported in the paper are:

- Mean Squared Error (`MSE`)
- Root Mean Squared Error (`RMSE`)
- `R^2`
- Mean Absolute Error (`MAE`)
- Spearman correlation
- accuracy within a `1000`-vehicle tolerance

## Reported Results

The paper and saved notebook outputs report the following main model results on the combined dataset:

| Model | RMSE | R^2 | MAE | Accuracy within 1000 vehicles |
| --- | ---: | ---: | ---: | ---: |
| XGBoost | 2600.53 | 0.841 | 1069.65 | 89.58% |
| Random Forest | 3354.95 | 0.817 | 1110.46 | 84.38% |
| Linear Regression | 25956.83 | 0.820 | 5544.77 | 51.04% |
| SVR (best tuned output in notebook) | 10859.75 | 0.532 | 2601.23 | 83.33% |

## Main Findings

- XGBoost produced the strongest overall predictive performance in the paper, with `R^2 = 0.841` and `89.58%` accuracy within a 1000-vehicle tolerance.
- Random Forest also performed well and captured the general EV adoption pattern across counties.
- Linear Regression identified a positive relationship for all four features, but its predictions were less stable for counties with low EV counts.
- EV charger availability was the most influential feature across the strongest models, indicating that infrastructure is a major driver of EV adoption at county level.

## Notes On Reproducibility

These notebooks were authored in Google Colab and currently reference source files from `/content/...`. The raw CSV source files are not included in this repository, so the notebooks are a record of the implemented pipeline and analysis logic rather than a one-click reproducible package.

To rerun the project, you will need to:

1. collect the original state EV, GDP, population, and charger datasets used in the notebooks
2. place them in the paths expected by the notebook cells or update the file paths
3. run the two preprocessing notebooks first
4. run `FODS_EV_Models.ipynb` after the machine learning tables are generated

## Research Paper

- Paper PDF: [B5_Electric_Vehicle_Population.pdf](paper/B5_Electric_Vehicle_Population.pdf)
