# CropWise: Predictive Modeling for Sustainable Agriculture

## Project Overview

- CropWise is a machine learning project designed to analyze agricultural data and generate predictive insights for Indian crop production.

- The system integrates crop yield records, climate data (temperature and rainfall), and soil nutrient information to model agricultural trends across different states.

- Machine learning techniques are used to reconstruct missing climate data, predict crop yields, and identify potential crop failure risks.

- The goal of the project is to support data-driven agricultural planning and improve decision-making for farmers, researchers, and policy planners.

## Project Pipeline

Data Sources  
→ Data Cleaning & Climate Imputation  
→ Feature Engineering  
→ Feature Selection (SelectKBest)  
→ Yield Prediction (GPR)  
→ Crop Failure Risk Classification

## Data Sources

The project integrates multiple government datasets to model agricultural productivity in India:

- Crop yield data from agricultural statistics portals  
- Historical temperature and rainfall data from meteorological datasets  
- Soil nutrient information from national soil health databases  

These datasets were merged to build a unified dataset combining climate, soil, and agricultural production variables.

## Dataset

The final dataset combines agricultural, climatic, and soil variables for Indian states between 1997–2019.

Key features include:
- Seasonal temperature and rainfall
- Soil nutrient indicators (N, P, K, micronutrients)
- Crop type and state information
- Cultivated area and production

Target variable:
- Crop Yield (tonnes per hectare)

## Data Preprocessing

Several preprocessing steps were applied:

- Standardizing state and district identifiers  
- Handling missing climate observations  
- Aligning monthly climate data with agricultural seasons  
- Removing inconsistent and duplicate records

## Handling Missing Climate Data

Climate datasets contained missing years for temperature and rainfall.

- Temperature values were reconstructed using machine learning models (Linear Regression, Random Forest, XGBoost).
- XGBoost achieved the best performance and was used for final imputation.
- Rainfall gaps were filled using interpolation due to smoother temporal trends.

This step ensured consistent climate data from 1997–2019.

## Final Feature Set

After preprocessing, the final dataset contained:

- Seasonal temperature and rainfall
- Soil nutrient composition
- Crop and state identifiers
- Cultivated area and production
- Derived crop yield values

These features capture the interaction between climate variability, soil conditions, and agricultural productivity.

## Dataset Structuring

Instead of training a single global model, the dataset was divided into crop–state subsets.

Only crop–state combinations with complete records from 1997–2019 were retained.  
This resulted in ~119 high-quality datasets used for crop-specific yield prediction models.

## Crop Failure Label Generation

To detect abnormal yield drops, a failure label was created using a 5-year moving average.

If a year’s yield dropped below **75% of the previous five-year average**, it was labeled as a crop failure.

This allowed the system to train classification models to detect potential production risks.

## Modeling Workflow

The modeling pipeline was designed to capture the regional variability of agricultural production across India. Instead of building a single nationwide model, the dataset was segmented into **119 crop–state combinations**, with each combination treated as an independent modeling problem.

This approach allows the models to learn localized relationships between climate conditions, soil properties, and crop yield patterns without generalizing across unrelated regions.

Each subset contained multi-year records of:

- Crop yield
- Rainfall
- Temperature
- Soil nutrient indicators
- Agricultural season

This segmentation ensured that each model focused on the **temporal behavior of a specific crop in a specific state**.

## Feature Selection

To improve model performance and reduce irrelevant inputs, feature selection was applied independently to each crop–state dataset.

The **SelectKBest** method from `scikit-learn` was used to identify the most informative predictors for yield prediction.

Because agricultural conditions vary across regions, the selected features differed for each crop–state combination.

The feature selection process involved:

1. Loading the crop–state dataset
2. Separating features (X) and target yield (y)
3. Applying **SelectKBest** to rank features based on statistical relevance
4. Selecting the top performing features for model training

The selected feature sets for all **119 crop–state combinations** are stored in the repository under:
`selected_features\`

Each file documents the final feature subset used for the corresponding model.

---

## Yield Prediction (Gaussian Process Regression)

Crop yield prediction was implemented using **Gaussian Process Regression (GPR)**. This probabilistic model was selected because the available datasets for each crop–state pair contained relatively small time-series samples.

### Modeling Process

For each crop–state dataset:

1. Load the segmented dataset for the crop–state pair  
2. Separate features (**X**) and target variable (**yield**)  
3. Split the dataset into training and testing sets  
4. Train the **Gaussian Process Regression model**  
5. Generate predictions for unseen years  
6. Evaluate prediction performance using regression metrics  

### Evaluation Metrics

Model performance was evaluated using:

- **RMSE (Root Mean Square Error)** – measures prediction error magnitude  
- **MAE (Mean Absolute Error)** – average absolute difference between predicted and actual yield  
- **MAPE (Mean Absolute Percentage Error)** – percentage error relative to true yield values  
- **R² Score** – indicates how well the model explains variability in yield  

Although GPR captured smooth yield trends effectively, experiments showed that agricultural datasets can introduce **noise and overfitting**, highlighting the challenges of fine-grained crop forecasting.

## Prediction Visualization

To analyze model performance over time, prediction plots were generated for the **test period (2016–2019)**.

For each crop–state combination, the following were visualized:

- Actual crop yield values
- Predicted crop yield values
- **95% confidence intervals** generated by the Gaussian Process Regression model

These plots provide a visual comparison between observed and predicted yields and help evaluate how well the model captures temporal yield trends.

The prediction plots are stored in the repository for all crop–state combinations.

## Model Evaluation Results

Model performance was evaluated across all **119 crop–state combinations** using standard regression metrics.

For each trained model, the following metrics were recorded:

- **ME (Mean Error)**
- **RMSE (Root Mean Square Error)**
- **R² Score**
- **Relative RMSE**

A consolidated metrics file summarizing performance across all combinations is included in the repository:
`model_metrics.csv`

This file allows easy comparison of prediction accuracy across crops and regions.

---

## Crop Failure Risk Prediction

In addition to predicting yield values, a classification model was developed to estimate **crop failure risk**.

### Target Label Construction

A binary risk label was created using historical yield trends:

- A **5-year rolling average yield** was computed for each crop–state pair.
- If the current year's yield fell below **75% of the previous five-year average**, it was labeled as **High Risk (1)**.
- Otherwise, the year was labeled **Low Risk (0)**.

This approach captures abnormal drops in productivity relative to historical performance.

### Classification Models

Three ensemble classifiers were evaluated:

- **Random Forest**
- **XGBoost**
- **LightGBM**

Hyperparameters were optimized using **RandomizedSearchCV with stratified 5-fold cross-validation**, with **ROC-AUC** used as the primary optimization metric.

### Evaluation Metrics

Risk classification models were evaluated using:

- **Accuracy**
- **Precision**
- **Recall**
- **F1 Score**
- **ROC–AUC**

High **recall** was prioritized to ensure that potential crop failure years were detected reliably.

---

## Model Comparison

The classification models produced comparable performance across the dataset.

- **LightGBM** provided the most balanced performance and computational efficiency.
- **XGBoost** achieved strong probability-based discrimination capability.
- **Random Forest** achieved higher recall when using a lower probability threshold.

These models collectively provide a decision-support framework for identifying **potentially high-risk agricultural seasons**.

---

## Future Work

Potential improvements for future iterations of this project include:

- Integrating irrigation and fertilizer usage datasets  
- Exploring additional ensemble models for yield prediction  
- Improving spatial resolution using district-level climate data  
- Incorporating remote sensing and satellite-derived agricultural indicators