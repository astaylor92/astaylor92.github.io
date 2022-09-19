---
layout: single
title: "Recipe Exploration Tool"
excerpt: Recipe recommendation for those looking to get 'outside their comfort zone' - exploration of similary measurements, scoring heuristics, and front-end design
classes: wide
skills: similary measurement, dash, plotly, 
---

# TODO FILL IN ALL "XX"s

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

If you're interested in more detail, see the 'Detail' section at the bottom of the page.

---

# What I Took Away

## Technology & Techniques
* word2vec
* Spark
* hierarchical clustering
* Scoring 
* git
* Heroku
* dash
* plotly
* networkx
* Louvain's algorithm

## Lessons Learned

* XX - data wrangling is never done
* XX - page design
* XX - unsupervised testing

---

# Notebooks and Demo

More detail on the project can be found in the form of annotated notebooks on my Github site.

XX - add link to notebooks

XX - note on demo

---

# Writeup

XX - add embedded report

---

# Detail

## Algorithm Chosen

## Visualization Approach & Techniques

## Testing and Evaluation