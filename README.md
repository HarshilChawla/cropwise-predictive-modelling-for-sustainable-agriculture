# CropWise: Predictive Modeling for Sustainable Agriculture

## Project Overview

- CropWise is a machine learning project designed to analyze agricultural data and generate predictive insights for Indian crop production.

- The system integrates crop yield records, climate data (temperature and rainfall), and soil nutrient information to model agricultural trends across different states.

- Machine learning techniques are used to reconstruct missing climate data, predict crop yields, and identify potential crop failure risks.

- The goal of the project is to support data-driven agricultural planning and improve decision-making for farmers, researchers, and policy planners.

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