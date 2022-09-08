---
layout: post
title: "Beer Classification"
excerpt: An exploration of multiclass classification algorithms to predict a beer's style by its characteristics
skills: scikit-learn pipelines, multiclass algorithms and scoring, multiple model family selection, classification, visualization
---

# TODO FILL IN ALL "XX"s

# What I Did

## Context

This one was just for fun! I was poking around at beer data and came across a pretty clean dataset on Kaggle with characteristics of beer tastes, along with the beer source information (name, type, brewery). It got me wondering, how easy is it to mathematically tell different beer types apart?

For those that drink beer, some types are very clearly different (an IPA vs a light lager, for example), and others are not (like a porter and a stout). However, with this dataset providing XX different features, maybe we can classify beers pretty well!

## Objective

The goal was simply to train a multiclass classifier to predict a beer's style from its characteristics. 

Selfishly, I also hadn't used `scikit-learn`'s `Pipeline` object too much yet, and I also wanted to dive deeper into multiclass classification scoring and metrics, since most of my experience dealt with the binary case.

## Approach

I took a fairly standard approach to this problem, going through the following steps:

1. **Data Collection, Cleaning & Transformation**
3. **Exploratory Data Analysis**
4. **Model Selection**
5. **Final Model Tuning & Output Analysis**


### Data Collection, Cleaning & Transformation

**Goals**: Get the data. Make sure the data was there (enough). Check the data had the structure and types I wanted, and transform it if not. Perform veracity and distribution checks on predictors and response.


**Collection**: Collection was fairly straightforward. The data came from Kaggle at XX, and I could see even from the Kaggle summary that it was pretty clean. I downloaded the file and pretty quickly moved.

XX - data fields & descriptions


**Transformation**: From a transformation standpoint, again, the data was in pretty good shape. The main work here was transforming the 'style' column, which contained 111 categories, some of which had as few as 3-4 instances. However, there was a common structure of "[Beer Style] - [Specific Beer Type]" throughout the set, so splitting the name brought us to 41 beer 'styles'. I also did some minor text formatting here to remove capital letters and spaces for easy indexing and reference in pandas structures.


**Cleaning**: Veracity checks revealed no nulls (thanks, Kaggle contributor!). From a distribution standpoint, most were consistently between 0 and 100.

XX - Distribution plots

However, the ABV column had apparent outliers.

XX - outlier vis from ABV

After investigating these and realizing they were more or less gag beers (see this link XX), I removed them from the dataset to avoid misrepresentation of beer types. Theoretically, it may also have been appropriate to create their own category, but with only 3 observations, it would be tough to train and test.

This left us with 41 categories, the smallest of which had 20 observations, and a clean dataset for further analysis

XX - category distribution countplot


### Exploratory Data Analysis

**Goals**: Check the correlation of predictors, understand their relationship to the classes, and get an idea of each beer style's characteristics.


**Correlation**: The correlation analysis showed a fairly signficant amount of multicollinearity in general. There were the obvious pairs - min IBU and max IBU, alcohol and ABV. There were also other relationships that weren't too surprising once revelated - sour beers tended also to be fruity, and malty beers tended to have a lot of body, for example.

XX - correlation plot

Although the tree-based algorithms shouln't have too much of an issue with this, I eventually needed to take this into with other models such as logistic regression and SVC.


**Predictor-Class Relationships**: I took a look at each predictor, and plotted boxplots of the predictor values vs each class. The hope was to see differentiation amongst the classes in at least some of the predictors to provide signal. And we did! The most apparent might've been the IBU plots, show below.

XX - IBU boxplots


**Beer Style Characteristics**: Essentially, here I wanted to take a look at the general profile of each beer. Now, I know radar plots (can be contentious)[http://www.thefunctionalart.com/2012/11/radar-graphs-avoid-them-999-of-time.html] due to the difficulty in comparing observations, BUT this seemed like a time where they make sense. The idea would be that radar charts give us a feel for each beer's characteristics without really needing a direct comparison across attributes. Then, we could use a few bar charts to compare beers along the different attributes.

For example, if we looked at the radar chart of an IPA, below, we can see that it's generally very XX, XX, and XX. It's also more XX than XX. We can't really tell if the value for XX is more than XX, but in this case, we don't care about those values to that level of detail.

XX - radar charts, sort by cluster correlation plot

XX - all radar charts

However, if we wanted to compare across beers (e.g. which beers were sweetest), we would take a look at something like the bar chart below.

XX - bar chart


### Model Selection

**Goals**: Define initial model families (e.g. "logistic regression"), outline model selection scheme, develop evaluation pipelines, and select model family.

**Model Families Explored**: Snce I wanted to try out a variety of classification models, I decided to evaluate a few flavors of logistic regression (regularized and non-regularized), a support vector classifier (SVC), random forest, and a histogram-based boosting classifier. 

**Model Selection Scheme**: In terms of the overall data and model selection scheme, I opted to first split the data into 'train/validate' and 'test' sets. The 'train/validate' set would first be used to select a model from the model family. To do this, I opted for a nested cross-validation scheme. In the inner loop, a gridsearch would be performed for the best hyperparameters for each model. Then, in the outer loop, the inner loop would be repeated to get an idea of generalization error and stability of hyperparameters.

The results of the nested CV would allow me to pick between the model families in an unbiased way. Then, I would take the selected model family and run a final gtidsearch with the same parameter grid, this time on the full 'train/validate' set. This would give me the final model, which could then be evaluated on the held-out 'test' set.

It's worth noting that I used stratification in both the data split and the CVs, in order to ensure our smaller classes were in each data set and fold.

Finally, I had to pick a metric to use to determine the 'best', both for the gridsearches and the overall choice. After doing some splashing around in learning the various multiclass metrics, I landed on balanced accuracy (`balanced_accuracy_score` in sklearn), as this was most fair to our many classes. It essentially takes the average recall across each class.

**Evaluation Pipelines**: This was the fun part for me! `sklearn`'s `Pipeline` objects are super useful. For me, this mattered primarily for the logistic regression and SVC estimators, both of which required scaling (required for the models to perform appropriately) and principal components analysis (to reduce multicollinearity). A sample of the code for this is below.

XX - code clock

**Model Selection Outcomes**: Maybe unsurprisingly, the tree-based estimators performed much better than logistic regression and SVC. The HGBT model performed the best, and is what was selected for final review.

XX - Model results

In addition, I learned some interesting things about each model's selections:

XX - Model observations

### Final Model Tuning & Output Analysis

**Goals**: Fit the final model on the full dataset, analyze overall performance, deep-dive misses



# What I Took Away

## Technology & Techniques

## Lessons Learned

## Thing to Explore Further

# What I Enjoyed

# Notebooks
