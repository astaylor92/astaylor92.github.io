---
layout: single
# hidden: true
title: "Loan Risk Submodel Experimentation"
excerpt: "Experimenting with feature engineering to improve loan risk submodels"
header:
  teaser: "/images/songpopularity/teaser.png"
classes: wide
author_profile: true
list_skills: "feature engineering, hypothesis testing, production model experimentation, prediction sensitivity analysis"
list_technical: "AWS S3, Databricks, JIRA, Confluence, python (pyspark, mlflow, scikit-learn, xgboost, pandas, numpy, plotnine)"
---

# What I Did

**Note** - *Because I can't discuss exact details, visualizations and detailed walkthroughs will be limited for this post.*

## Context
This was a project that I did as an intern at Kabbage, a part of American Express, over the summer of 2022. I worked on a risk modeling team for Kabbage's Funding product, which provides automated small business loans, enabled by automatic underwriting based on risk modeling that my larger team peformed.

My direct teammates were concentrated on the models which approximated default risk for new (applying) customers. This process had many data channels, and I was tasked with investigating the use of a submodel to predict a customer's bank activity, which could in turn be used as input to the ensemble models which estimated risk of default.

## Objective
First, we wanted to understand if a customer's bank history could be used to predict their expected account credits and debits in the 90 days following their application. Then, we wanted to understand if those outputs (or some aggregation / compution using them) would provide additional signal or benefit to existing risk models.

## Approach
The overall approach for this was fairly straightforward, but the devil was in the model testing, experimentation, and definition of 'value' of the outputs. I won't be able to provide many graphics, but will give summaries of what was performed in each step below.

XX - Chevrons

| :---: | :--- |
| <img src="/images/kabbage/extract.png"  width="70%" height="70%"> | **Data Collection** - An in-house database querying and feature extraction layer was used to collect the feature set and DV. The feature set was ~2k features generated from transaction-level bank extracts. |
| <img src="/images/kabbage/merge.png"  width="70%" height="70%"> | **Data Transformation & Merge** - Data from multiple queries (features from the time leading up to application and time after) was merged, and gaps were noted. DV outliers were removed from the dataset. Model population was carefully noted and summarized.|
| <img src="/images/kabbage/cluster.png"  width="70%" height="70%"> | **Feature Downselection & Clustering** - Aggregate metrics such as null count, mean, median, standard deviation, correlation with the DV, weight-of-evidence, and others were generated. Features were removed based on metric thresholds, and remaining features were clustered based on their correlation with DV and collinearity. |
| <img src="/images/kabbage/mlflow_databricks.png"  width="70%" height="70%"> | **Experimentation** - An intial xgboost gridsearch was run to determine parameters to use during experimentation. After initial model run, experiments were performed to improve performance and tail behavior. Experiments included log transformation of the DV, standardization of the DV, and further outlier removal. |
| <img src="/images/kabbage/iceplot.png"  width="70%" height="70%"> | **Output Analysis - Predictive Performance** - Performance metrics were evaluated and fed to a standard Excel file. Model accuracy by DV value, actual value, and other features are summarized. Sensitivity of key metrics was evaluated using ICE plots and summaries. A standard set of performance metrics was used for comparison. |
| <img src="/images/kabbage/corrplot.png"  width="70%" height="70%"> | **Output Analysis - Value as a Feature** - Predicted DVs' correlation and gini index was evaluated against risk model predictions, actuals, and residuals to understand the value of the outputs. |


## Outcome

# What I Took Away

## Technology & Techniques
* Jira
* Confluence
* AWS S3
* Databricks
* mlflow
* Pyspark
* xgboost
* pandas and numpy
* xlswriter

## Lessons Learned
* Code review
* Presentation of materials
* Standardization of metrics calculations
* Standardization of reporting
* Custom libraries for feature selection & extraction
* WoE

