---
layout: single
hidden: true
title: ""
excerpt: "Experimenting with feature engineering to improve loan risk submodels"
header:
  teaser: "/images/songpopularity/teaser.png"
classes: wide
author_profile: true
list_skills: "feature engineering, hypothesis testing, production model experimentation, prediction sensitivity analysis"
list_technical: "AWS S3, Databricks, JIRA, Confluence, python (pyspark, mlflow, scikit-learn, xgboost, pandas, numpy, plotnine)"
---

# What I Did

## Context
This was a project that I did as an intern at Kabbage, a part of American Express, over the summer of 2022. I worked on a risk modeling team for Kabbage's Funding product, which provides automated small business loans, enabled by automatic underwriting based on risk modeling that my larger team peformed.

My direct teammates were concentrated on the models which approximated default risk for new (applying) customers. This process had many data channels, and I was tasked with investigating the use of a submodel to predict a customer's bank activity, which could in turn be used as input to the ensemble models which estimated risk of default.

## Objective
First, we wanted to understand if a customer's bank history could be used to predict their expected account credits and debits in the 90 days following their application. Then, we wanted to understand if those outputs (or some aggregation / compution using them) would provide additional signal or benefit to existing risk models.

## Approach
The overall approach for this was fairly straightforward, but the devil was in the model testing, experimentation, and definition of 'value' of the outputs. I won't be able to provide many graphics, but will give summaries of what was performed in each step below.

XX - Chevrons

### Summary of Steps
1. **Data Collection**
2. **Data Transformation**
3. **Feature Downselection & Clustering**
4. **Preliminary Gridsearch**
5. **Experimentation** - Log transformation, % DV
6. **Output Analysis - Predictive Performance**
7. **Output Analysis - Value as a Feature**

### Additional Achievements
* Recommendation for WoE

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

