---
title: "Recipe Exploration Tool"
excerpt: "Recipe recommendation for those looking to get 'outside their comfort zone' - exploration of similary measurements, scoring heuristics, and front-end design"
header:
  teaser: "/images/fooddotcom/teaser.png"
layout: single
classes: wide
list_skills: "recommendation engine, similarity measurement, graph-based clustering, UI design & build"
list_technical: "Spark, MLLib, AWS Sagemaker, python (pyspark, plotly, dash, scikit-learn, pandas, numpy, seaborn)"
---

# What I Did

## Context

While surfing Kaggle for data on my favorite topics (food, beer, music), I came across [this food.com dataset](https://www.kaggle.com/datasets/shuyangli94/food-com-recipes-and-user-interactions) which had a number of interesting data sources. It contained "180K+ recipes and 700K+ recipe reviews covering 18 years of user interactions and uploads on Food.com". At the time, I was in a group of 5 doing a group project for a Data & Visual Analytics class. We were tasked with finding a project that combined visual design and user interaction, analyzing large datasets (defined as more than 100k records), and analytical technqiues we were learning at the time.

The world was pretty much our oyster with the dataset. Given that it had recipe information, ingredient information, and user rating interactions, a common approach from others on Kaggle was to attempt to build a recommendation engine using collaborative filtering or other methods. We opted for something a bit more off-the-wall: What if we could build a UI where an existing user could explore recipes that were intentionally **different** than their current cooking?


## Objective
Our goal was to create a user experience to inform the user of their cooking habits and promote exploration of new foods.

The idea was that someone who already had a food.com account would follow the general three-step flow below:

> First, a user could review their cooking habits against the general population along four dimensions.
> 
> 1. **Ingredients**: What are my most common ingredients? Do I cook with more or fewer ingredients than the typical food.com user?
> 2. **Techniques**: What techniques do I often use? How does that compare to the profile of a typical user?
> 3. **Cuisine**: How often do I try different cuisines? What other cuisines are out there?
> 4. **User Community**: Am I just like others at food.com?
> 
> Then, a user would provide two sets of inputs to the recommendation engine. One set would provide boundaries on the recommendations such as nutrition, meal of the day, time to cook, etc. The second set would allow the user to indicate whether the recommendations should be the same, different, or not considered along the four dimensions above - essentially telling the algorithm what to explore.
> 
> Finally, the algorithm would generate recommendations and show how the recommendations made the user a more diverse chef.

## My Role
Because this was a team project, we tried to define clear roles - data wrangling & EDA, algorithm development, visualization development, project management, and technical writing. My role was technically two-fold: project manager and algorithm developer. However, as project manager and the team member with the most experience with `plotly` and `dash`, I ended up supporting the visualization development heavily and filling in elsewhere as needed to meet deadlines. 

## Approach
After outlining the user experience above, the team came up with the project plan below.

<p align="center">
    <img src="/images/fooddotcom/gantt.png"  width="80%" height="80%">
    <br>
    <span class="figure-caption"> Gantt Chart </span>
</p>

This was a fairly straightforward set of steps, although due to a short timeline, many things had to run in parallel. I was tasked with keeping streams of communication open between teams. This was especially important during algorithm development, as much of our experimentation required open communication with the owners of EDA and data transformation workflows.

## Outcome

Overall, the final product of this project was a success. The recommendation engine performed well in terms of recommendation performance, application performance (response time), quasi-functional/unit tests, and user interaction tests. I'm also just proud of the way the final UI turned out - see screenshots below for examples, or feel free to check out the 'Demo' section below if you want to clone and run the algorithm yourself!

|:---:|:---|
| ![S1](/images/fooddotcom/screenshot_p1s1.png) | *First page - summary statistics for a given user* |
| ![S2](/images/fooddotcom/screenshot_p1s2.png) | *First page - summary statistics for a given user (cont.)* |
| ![S3](/images/fooddotcom/screenshot_p1s3.png) | *Second page - user input and recommendation output* |
| ![S4](/images/fooddotcom/screenshot_p1s2.png) | *Second page - comparisons of recommendations to behaviors* |

Additional detail on the work is included in the 'Detail' section at the bottom of the page.

---

# What I Took Away

## Technology & Techniques

This was an exciting one to work on because I got to actively develop user interface in something other than Tableau, as well as learn some algorithms for similarity measurement and graph analysis that I hadn't before.

**Algorithm**
* *word2vec & cosine similarity for ingredient similarity* - we used the `gensim` library to implement Word2Vec, which we used to score the similarity of all 15k ingredients to each other, based on the degree to which they shared similar recipes. From there, we could look at the top rated recipes for a user, sum up the ingredient vector for all of thoese recipes, and get a blended / overall view of what ingredients are most like the ingredients in their top recipes. Finally, we used cosine similarity to measure the distance from that vector to any given recipe, to be able to rank recipes as either 'similar to' existing ingredients or 'different from'.
* *Spark & MLlib for community analysis* - analyzing recipes by their shared user base with other recipes pretty quickly led us to generating a graph matrix (weighted, where nodes are recipes and edges are weighted by the % of total users that are shared between the two) and performing graph analysis. It turns out that Spark, and MLlib specifically, have a growing amount of functionality to handle this, and are even compatible with `networkx`! I used a variety of functions from that library, including their impleentation of Djikstra's algorithm, hierarchical clustering and bisection capabilities.
* *Amazon Sagemaker for graph scaling* - In order to do some of the graph analysis, I needed to scale above my local machine. Opting for the quick and dirty approach (and because I had some credits), I spun up an Amazon Sagemaker instance to do this.

**UI and Implementation**
* *git for version control* - This seems obvious, but our school initially had us using the Sharepoint-based file system in Microsoft Teams to share and work on files. We quickly switched to git, which was great. We especially liked being able ot deploy our Heroku app from the Team's repository, which leads us to...
* *Heroku for public hosting* - We deployed the app from our github repo, which was great because it gave us a way to easily demo the capability!
* *dash, html, and css for app hosting* - This was probably the thing I most enjoyed working in, although its application to real-world / enterprise scenarios is questionable. I spent a lot of time understanding the guts of the layout - callback system, as well as thoroughly customizing the pages using html and css.
* *plotly for dynamic plotting* - Although it was a bit of pain compared to more intuitive visualization libraries and softwares, `plotly` is highly ocmpatible with `dash`, allowing us to make efficient, effective visualizations which changed as inputs such as the user name and the recommendation parameters were updated by a user.
* *networkx for graph manipulation* - After we did the heavy lifting for graph analysis in Spark, we used networkx to store and manipulate graph objects for the user interface and visualization

## Lessons Learned

* **Data Wrangling is Iterative** - This is obvious, and I definitely knew it beforehand, but our team collectively learned this lesson repeatedly. It was not uncommon that, as the algorithm developers tested new theories and had ideas, they had to go back to the EDA and transformation team and ask for updates to data schemas, filters, etc. Often, those requests had a cascading dependency to other code, which often required careful changes.
* **UI Design is Not Easy** - We built an "app" on static html that is very basic and by no means sophisticated. I think going forward, it will be important for me to distinguish a quick dashboard-based app like this or something in Tableau from more intensely developed products which require teams of developers. 
* **Unsupervised Modeling is Ambiguous** - Another 'duh' moment, but because we wanted to intentionally recommend a 'different' recipe, we didn't have a great way to test for effectiveness. We had user ratings, but that wouldn't reward the recommender if a recipe was 'different' in the right way. While we did perform a variety of functional and gutcheck tests, we simply couldn't evaluate the exact performance of the interface without user testing.
* **Graph Analysis can get BIG** - When I was working with the graph analysis for clustering recipes based on their shared users, I realized just how quickly the graph size can scale. I ended up working with a graph with ~150k nodes and ~12M edges, requiring me to spin up an Amazon Sagemaker instance just to scale the compute power appropriately. 

---

# Notebooks and Demo

More detail on the project can be found in the form of annotated notebooks on my Github site.

[Github Repo](https://github.com/astaylor92/fooddotcom)

NOTE - Please read the README file in the repo for instructions on how to quickly deploy a demo of the app.

---

# Detail

Feel free to download the report with more procedural detail [here](/images/fooddotcom/report.pdf)