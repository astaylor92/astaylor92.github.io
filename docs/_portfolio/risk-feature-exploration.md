---
layout: single
# hidden: true
title: "Loan Risk Submodel Experimentation"
excerpt: "Experimenting with feature engineering to improve loan risk submodels, with an emphasis on real-world ML Ops, model experimentation, and production-scale data"
header:
  teaser: "/images/kabbage/mlflow_databricks.png"
classes: wide
author_profile: true
list_skills: "feature engineering, hypothesis testing, model experimentation, prediction sensitivity analysis"
list_technical: "AWS S3, Databricks, JIRA, Confluence, python (pyspark, mlflow, scikit-learn, xgboost, plotnine)"
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

<p align="center">
    <img src="/images/kabbage/chevrons.png">
    <br>
    <span class="figure-caption"> Project Steps </span>
</p>

| :---: | :--- |
| <img src="/images/kabbage/extract.png"  width="70%" height="70%"> | <span class="table-steps">**Data Collection**<br>An in-house database querying and feature extraction layer was used to collect the feature set and DV. The feature set was ~2k features generated from transaction-level bank extracts.</p> |
| <img src="/images/kabbage/merge.png"  width="70%" height="70%"> | <span class="table-steps">**Data Transformation & Merge**<br> Data from multiple queries (features from the time leading up to application and time after) was merged, and gaps were noted. DV outliers were removed from the dataset. Model population was carefully noted and summarized.|
| <img src="/images/kabbage/cluster.png"  width="70%" height="70%"> | <span class="table-steps">**Feature Downselection & Clustering**<br> Aggregate metrics such as null count, mean, median, standard deviation, correlation with the DV, weight-of-evidence, and others were generated. Features were removed based on metric thresholds, and remaining features were clustered based on their correlation with DV and collinearity. |
| <img src="/images/kabbage/mlflow_databricks.png"  width="70%" height="70%"> | <span class="table-steps">**Experimentation**<br> An intial xgboost gridsearch was run to determine parameters to use during experimentation. After initial model run, experiments were performed to improve performance and tail behavior. Experiments included log transformation of the DV, standardization of the DV, and further outlier removal. |
| <img src="/images/kabbage/iceplot.png"  width="70%" height="70%"> | <span class="table-steps">**Output Analysis - Predictive Performance**<br> Performance metrics were evaluated and fed to a standard Excel file. Model accuracy by DV value, actual value, and other features are summarized. Sensitivity of key metrics was evaluated using ICE plots and summaries. A standard set of performance metrics was used for comparison. |
| <img src="/images/kabbage/corrplot.png"  width="70%" height="70%"> | <span class="table-steps">**Output Analysis - Value as a Feature**<br> Predicted DVs' correlation and gini index was evaluated against risk model predictions, actuals, and residuals to understand the value of the outputs. |


## Outcome

Overall, the project was a success, although the results on both fronts weren't extremely promising. From a performance standpoint, with only bank history to work on, prediction of the change in account transactions was OK. The features didn't provide much insight - the most influential feature was the total transactions from the period leading up. 

From a value as features standpoint, we found a few hypotheses to explore, but nothing that looked like it would bring enough signal to the models to chase down further. This was OK, and made some sense - the features used in this model were many of the same features evaluated for the other submodels. It seemed that would take a model with other significant features or more encoding (e.g. deep learning) to provide different enough signal to enrich the other models.

# What I Took Away

## Technology & Techniques
* *Jira* for agile team management, including creation of epics, stories, and issues for my project
* *Confluence* for documentation and sharing results
* *AWS S3* for file storage
* *Databricks* for all mdeling and analysis
* *mlflow* for experiment management and model selection
* *Pyspark* for data extract and processing
* *xgboost* for boosted regressor development
* *xlswriter* for report writing

## Lessons Learned
* **Enterprise standardization of metrics calculations and model reporting are wonderful** - The team at Amex predefined a number of commonly used metrics for different families of estimators, and created custom helper libraries to make reporting very fast. Our direct team also used `xlsxwriter` and a pretty slick set of helper functions to create a number of valuable performance summaries across multiple aggregations.
* **Code reviews are great** - Our team instituted weekly rotating rubber ducky programming sessions that were extremely helpful, particularly for quick debugs as well as learning new coding techniques.
* **Custom libraries for feature selection & extraction are a gift and a curse** - The team had standardized libraries for generating aggregate features from transactional data and downselecting them. This was great from a repeatibility standpoint, but it also was initially built for models with a different purpose then mine. This meant that some of the performance challenges we faced were buried in a 'black box' of prebuilt analysis.
* **Weight of evidence and information value are cool metrics for boosting** - I hadn't used weight of evidence to evaluate features before this job, but it was a great way to measure a feature's relationship to the DV. It helped for continuous and categorical features, which we bucketed and calculated WoE.

