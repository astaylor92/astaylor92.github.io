---
layout: single
title: "Beer Classification"
excerpt: An exploration of multiclass classification algorithms to predict a beer's style by its characteristics
classes: wide
skills: scikit-learn pipelines, multiclass algorithms and scoring, multiple model family selection, classification, visualization
---

# What I Did

## Context

I don't know much about beer, but I like it. I was poking around at beer data and came across a pretty clean dataset on Kaggle with flavor and aroma profiles, along with the beer source information (name, type, brewery). It got me wondering, how easy is it to mathematically tell different beer style apart?

Some are clearly different (an IPA vs a light lager, for example), and others are not (like a porter and a stout). Maybe machine learning can do better than I would, especially after a few.

## Objective

Train a multiclass classifier to predict a beer's style from its characteristics. That's it.

Selfishly, I also wanted to get a few reps with `scikit-learn`'s `Pipeline` object and to dive deeper into multiclass classification scoring and metrics, since most of my experience has dealt with the binary case.

## Approach

I took a fairly standard approach to this problem, going through the following steps:

1. **Data Collection, Cleaning & Transformation**: Get the data. Make sure the data was there (enough). Check the data had the structure and types I wanted, and transform it if not. Perform veracity and distribution checks on predictors and response.
3. **Exploratory Data Analysis**: Check the correlation of predictors, understand their relationship to the classes, and get an idea of each beer style's characteristics.
4. **Model Selection**: Define initial model families (e.g. "logistic regression"), outline a model selection scheme, develop evaluation pipelines, and select model family.
5. **Final Model Tuning & Output Analysis**: Fit the final model on the full dataset, analyze overall performance, evaluate feature performance, and deep-dive misses.

**Note** - A more detailed process walkthrough can be found in the last section of this post.

## Outcome

After surveying six different families of models, each tested for their best hyperparameters, I ended up with a Histogram-Based Gradient Boosting Classifier with relatively sttrong a balanced accuracy score of .866.
**Note**: Balanced accuracy here refers to sklearn's `balanced_accuracy_score`, an average of the class accuracy across all classes.

<p align="center">
    <img src="/images/beercharacteristics/model_balanced_accuracy.png"  width="65%" height="65%">
    <br>
    <span class="figure-caption"> Model Results </span>
</p>

The tree-based models (Random Forest and Histogram-based Gradient Boosting Classifier) far outperformed non-tree-based counterparts (Logistic Regression and RBF-Kernel Support Vector Classifier). Considering the nature of the data and number of classes, this was not too surprising. Tree-based classifiers are all the rage due to their ability to pick up signal while mitigating overfitting, plus with 41 classes, a model which can detect nuanced, nonlinear relationships between predictor and class was likely to do well.

Given that the classes weren't balanced, I was concerned that we may have achieved strong accuracy just by predicting the larger classes. Reviewing the results, I was pleased to see that the classifier seemed to place a balanced amount of recommendaitons into small and large classes alike.

<p align="center">
    <img src="/images/beercharacteristics/prediction_counts.png">
    <br>
    <span class="figure-caption"> Class Predictions vs Actuals </span>
</p>

In fact, the classes that struggled the most made a lot of sense. For example, the Lager class was very often mistaken for a Pilsner, something that I do all the time!

<h5 align="center">Lager - 17 Misclassifications of 142</h5>
<img src="/images/beercharacteristics/miss_summary_lager.png">
<br>
<p align="center">
<span class="figure-caption"> Misclassification Summary - Lager </span>
</p>

Overall, the HGBT classifier provided a strong result (balanced accuracy of 0.86) while classifying well against large and small classes alike.

---

# What I Took Away

## Technology & Techniques
This was an opportunity for me to explore a number of things on the technical side:

* Extensive use of `sklearn`
    * `Pipeline` to formulate
    * Nested CV using `GridsearchCV` and `cross_validate_score`
    * `StratifiedKFold` and stratification in `test_train_split`
    * Custom scoring using individual scores from the `scoring` library
* All of the `pandas` (as usual)
* Radar plots in `matplotlib`
* `seaborn` for all other vis
* `textwrap` library for custom plot text wrapping (it's the little things)

## Lessons Learned

* **Deep-diving convergence errors can take a while** - Both the SVC and logistic regression struggled to converge at various points during parameter search. This was somewhat alleviated by increasing the number of iterations, but it turned out that if the regularization penalty was too small, the models often had trouble keeping coefficients stable. In addition, having too few principal components also caused instability.
* **Radar charts ain't always right** - Now, I know radar plots [can be contentious](http://www.thefunctionalart.com/2012/11/radar-graphs-avoid-them-999-of-time.html) due to the difficulty in comparing observations, BUT this seemed like a time where they make sense. In this case, since we never really needed to know to the decimal point if one attribute was higher than another for a given beer, that was fine. I also liked how they showed the 'shape' of a beer here (larger = "louder" flavor and aroma). However, I had to move to faceted bar charts if I wanted to easily compre across beer styles.
* **Getting to know scoring metrics is useful if a little confusing** - Deep-diving precision, recall, and f1 score in a multiclass setting was interesting. The binary versions of these metrics are fairly easy to understand, but once more than one class is introduced, we also have to decide a way to average (micro, macro, or weighted). It turns out, you end up needing to look at all metrics to get a comprehensive view of how the model performs.
* **Pickle & parquet files are my friend** - Because model fitting took significant time on my local machine (up to 20 minutes for the nested CV for HGBT), I needed to save out models and dataframes often. Saving gridsearch objects (including fitted estimators) as pickle files was useful, as was saving pandas DataFrames with multiple data types as parquet, to avoid the need for datatype definition on read-in.
* **Good results are good** - It was satisfying to have a decently performing classifier, especially on micro-averaged metrics, with so many classes! I'll be honest, I was not optimistic - I figured the model might say 'throw a dart at a beer and it's probably a lager', rather than learn any of the nuance between styles.

---

# Notebooks

More detail on the project can be found in the form of annotated notebooks on my Github site.

[Github Repo](https://github.com/astaylor92/beercharacteristics/tree/master)

---

# Process Detail

A more detailed procedural walkthrough is below.

## Data Collection, Cleaning & Transformation


**Goals**: Get the data. Make sure the data was there (enough). Check the data had the structure and types I wanted, and transform it if not. Perform veracity and distribution checks on predictors and response.


**Collection**: Collection was fairly straightforward. The data came from Kaggle [here](https://www.kaggle.com/datasets/ruthgn/beer-profile-and-ratings-data-set), and I could see even from the Kaggle summary that it was pretty clean. The dataset contained 3,197 unique beers across 934 breweries.

| Field | Description |
| :--: | :-- |
| beer_name | Primary key - identifies the specific beer |
| style_l1 | Custom 'level 1' style description - the dependent variable in our analysis |
| min_ibu | Minimum IBU  of the beer |
| max_ibu | Maximum IBU of the beer |
| astringency | Astringency of the beer (similar to tannins in wine tasting) |
| body | Body in terms of mouthfeel |
| alcohol | Perceived booziness |
| bitter | Bitterness of the taste |
| sweet | Sweetness of the taste |
| sour | Sourness of the taste |
| salty | Saltiness of the taste |
| fruits | Fruitiness of the aroma and flavor |
| hoppy | Hoppiness of the aroma and flavor |
| spices | Spiciness of the aroma and flavor |
| malty | Maltiness of the aroma and flavor |


**Transformation**: As I mentioned, the data was in pretty good shape - no nulls or invalid data types. The main work here was transforming the 'style' column, which contained 111 categories, some of which had as few as 3-4 instances. However, there was a common structure of "[Beer Style] - [Specific Beer Type]" throughout the set, so splitting the name brought us to 41 beer 'styles'. I also did some minor text formatting here to remove capital letters and spaces for easy indexing and reference in pandas structures.


**Cleaning**: Veracity checks revealed no nulls (thanks, Kaggle contributor!). From a distribution standpoint, most were consistently between 0 and 100.

<p align="center">
    <img src="/images/beercharacteristics/histograms_predictors.png"  width="75%" height="75%">
    <br>
    <span class="figure-caption"> Feature Distributions </span>
</p>

However, the ABV column had apparent outliers.

<p align="center">
    <img src="/images/beercharacteristics/boxplot_abv.png">
    <br>
    <span class="figure-caption"> ABV Boxplot </span>
</p>

After investigating these and realizing they were more or less gag beers (e.g. the [Brewmeister Snake Venom](https://www.htfw.com/brewmeister-snake-venom-world-s-strongest-beer-free-branded-glass-beer-lager)), I removed them from the dataset to avoid misrepresentation of beer types. Theoretically, it may also have been appropriate to create their own category, but with only 3 observations, it would be tough to train and test.

This left us with 41 categories, the smallest of which had 20 observations, and a clean dataset for further analysis

<p align="center">
    <img src="/images/beercharacteristics/count_styles_lowremoved.png"  width="80%" height="80%">
    <br>
    <span class="figure-caption"> Beer Style Counts </span>
</p>


## Exploratory Data Analysis

**Goals**: Check the correlation of predictors, understand their relationship to the classes, and get an idea of each beer style's characteristics.


**Correlation**: The correlation analysis showed a fairly signficant amount of multicollinearity in general. There were the obvious pairs - min IBU and max IBU, alcohol and ABV. There were also other relationships that weren't too surprising once revealed - sour beers tended also to be fruity, and malty beers tended to have a lot of body, for example.

<p align="center">
    <img src="/images/beercharacteristics/corrplot.png"  width="80%" height="80%">
    <br>
    <span class="figure-caption"> Correlation Heatmap </span>
</p>

Although the tree-based algorithms shouln't have too much of an issue with this, I eventually needed to take this into with other models such as logistic regression and SVC.


**Predictor-Class Relationships**: I took a look at each predictor, and plotted boxplots of the predictor values vs each class. The hope was to see differentiation amongst the classes in at least some of the predictors to provide signal. And we did! The most apparent might've been the IBU plots, shown below.

<p align="center">
    <img src="/images/beercharacteristics/boxplot_dv_max_ibu.png"  width="80%" height="80%">
    <br>
    <span class="figure-caption"> Boxplots of Style vs Max IBU </span>
</p>


**Beer Style Characteristics**: Essentially, here I wanted to take a look at the general profile of each beer. See my 'Lessons Learned' about radar plots. I know they're contentious, but this seemed like an appropriate application.

For example, if we looked at the radar chart of an IPA, below, we can see that it's generally very malty and bitter, with a lot of body. It's also more sweet than hoppy. We can't really tell if the value for malty is more than bitter, but in this case, we don't care about those values to that level of detail.

<p align="center">
    <img src="/images/beercharacteristics/spider_Stout.png"  width="50%" height="50%">
    <br>
    <span class="figure-caption"> Radar Chart, Stout </span>
</p>

We can also look across the beers and see that the 'louder' beers (e.g. barleywine) have larger total shaded area due to the intensity of their flavor profiles across all 11 dimensions.

<p align="center">
    <img src="/images/beercharacteristics/spiders_all.png"  width="50%" height="50%">
    <br>
    <span class="figure-caption"> Radar Charts, All Styles </span>
</p>

However, if we wanted to compare across beers (e.g. which beers were sweetest), we would take a look at something like the bar chart below.

<p align="center">
    <img src="/images/beercharacteristics/sweetness_by_style.png"  width="65%" height="65%">
    <br>
    <span class="figure-caption"> Sweetness by Style </span>
</p>


## Model Selection

**Goals**: Define initial model families (e.g. "logistic regression"), outline model selection scheme, develop evaluation pipelines, and select model family.

**Model Families Explored**: Snce I wanted to try out a variety of classification models, I decided to evaluate a few flavors of logistic regression (regularized and non-regularized), a support vector classifier (SVC), random forest, and a histogram-based boosting classifier. 

**Model Selection Scheme**: In terms of the overall data and model selection scheme, I opted to first split the data into 'train/validate' and 'test' sets. The 'train/validate' set would be used to select a model from the model family, the the 'test' set would be available for generalization performance and output evaluation. To select the model family, I opted for a nested cross-validation scheme. In the inner loop, a gridsearch would be performed for the best hyperparameters for each model. Then, in the outer loop, the inner loop would be repeated to get an idea of generalization error and stability of hyperparameters.

The results of the nested CV would allow me to pick between the model families in an unbiased way. Then, I would take the selected model family and run a final gridsearch with the same parameter grid, this time on the full 'train/validate' set. This would give me the final model, which could then be evaluated on the held-out 'test' set.

It's worth noting that I used stratification in both the data split and the CVs, in order to ensure our smaller classes were represented in each split.

Finally, I had to pick a metric to use to determine the 'best', both for the gridsearches and the overall choice. After doing some splashing around in learning the various multiclass metrics, I landed on balanced accuracy (`balanced_accuracy_score` in sklearn), as this was most fair to our many classes. It essentially takes the average recall across all classes, regardless of their size.

**Evaluation Pipelines**: This was the fun part for me! `sklearn`'s `Pipeline` objects were super useful. For me, this mattered primarily for the logistic regression and SVC estimators, both of which required scaling (required for the models to perform appropriately) and principal components analysis (to reduce multicollinearity). A sample of the code for this is below, shown for SVC.

```
# Create the scaler
scaler = StandardScaler()

# Create the PCA object
pca = PCA()

# Create the estimator object
svc_estimator = SVC(max_iter=1000)

# Create the Pipeline object
svc_pipe = Pipeline(
    steps=[
        ('scaler', scaler),
        ('pca', pca),
        ('estimator', svc_estimator)
        ])

# Create the parameter grid for searching
svc_param_grid = [
    {
        "pca__n_components": [4, 6],
        "estimator__kernel": ['rbf', 'linear'],
        "estimator__C": [0.01, 1, 100, 10000],
    }
]
```

**Model Selection Outcomes**: Maybe unsurprisingly, the tree-based estimators performed much better than logistic regression and SVC. The HGBT model performed the best, and is what was selected for final review.

<p align="center">
    <img src="/images/beercharacteristics/model_scores.png">
    <br>
    <span class="figure-caption"> Performance Scores by Model </span>
</p>

In addition, I learned some interesting things about each model's selections:

**Logistic Regression**

The best logistic regression models are either without regularization or with l2 regularization. This makes sense - we've already reduced to 4-6 principal components, so we shouldn't need much penalization to model complexity, especially removal of variables using l1. We can also see that C-values of 10,000 were most often selected, which supports the idea that little regulariztion is needed. There was instability in the parameter selection, especially in LASSO, which could be further investigated if it becomes relevant.

**SVC**

Unsurprisingly, the more flexible kernel (RBF) provided best results. All 10 folds selcted a C value of 100.

**HGBT**

Interestingly, the higher depth was selected (8), which isn't necessarily needed in a boosting algorithm. In addition, the faster learning rate was also chosen every time. In one instance, the lower number of boosters (250) was picked.

**Random Forest**

This one was not incredibly consistent - ultimately, a depth of 16 was picked, along with the most trees in the forest. The lowest min_samples_split was also picked to allow for the deepest trees and smallest leaves.

## Final Model Tuning & Output Analysis

**Goals**: Fit the final model on the full dataset, analyze overall performance, understand feature importance, deep-dive misses

**Final Model Fit**: First, I refit HGBT hyperparameters to the full test/validation dataset using `GridSearchCV`. The final parameters were:

```
{'estimator__learning_rate': 0.04,
 'estimator__max_depth': 8,
 'estimator__max_iter': 500}
```

**Performance Analysis**: Then, I looked at the final performance metrics. I was pleasantly surprised - I saw both high and consistent numbers across precision, recall, and f1 scores averaged in the three ways mentioned above.

| Accuracy |               0.861286 |
| Balanced Accuracy |      0.861348 |
| Precision (Micro) |      0.861286 |
| Precision (Macro) |      0.869391 |
| Precision (Weighted) |   0.864044 |
| Recall (Micro) |         0.861286 |
| Recall (Macro) |         0.861348 |
| Recall (Weighted) |      0.861286 |
| F1 (Micro) |             0.861286 |
| F1 (Macro) |             0.860936 |
| F1 (Weighted) |          0.859950 |

**Feature Importance**: Using permutation importance, I examined which features were most critical to the HGBT model. Permutation importance essentially runs multiple permutations / random shuffling of each predictor and measures the impact on a performance metric (balanced accuracy, in this case). 

<p align="center">
    <img src="/images/beercharacteristics/feature_importance.png"  width="50%" height="50%">
    <br>
    <span class="figure-caption"> Permutation Feature Importance </span>
</p>

Interestingly, both the IBU metrics acccounted for most of the model! It turns out the beer styles are nearly seperably defined by their bitterness. Next, the alcohol level, hoppiness, and body were the next differentiators. This is interesting - I would've expected to see a more balanced output, incorporating body and maltiness, but maybe this isn't surprising. IBU is also our most detailed metric, on a different scale than the others, and it's an 'official' number. It may just provide more granularity than others.

**Misclassification Deep-Dive**: Lastly, I deep-dove some of the top missed categories, examining the profiles of the top 3 beers they were mistaken for. Results here were not surprising, especially in our larger categories. For example, stouts were most commonly confused for porters, which makes sense - both are dark, bitter beers. 


<h5 align="center">Stout - 9 Misclassifications of 64</h5>
<img src="/images/beercharacteristics/miss_summary_stout.png">
<br>
<p align="center">
    <span class="figure-caption"> Misclassification Summary, Stout </span>
</p>