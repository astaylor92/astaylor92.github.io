---
layout: single
title: "Song Popularity Prediction"
hidden: true
excerpt: "Marrying up last.fm, Spotify, and Musicbrainz data and understand if/how sonic features make a song popular"
header:
  teaser: "/images/songpopularity/teaser.png"
classes: wide
author_profile: true
list_skills: "API calling, data merging, relational schema interpretation, regression, feature importance analysis"
list_technical: "scikit-learn, pandas, numpy, seaborn, requests, time"
---

# What I Did

## Context
When my wife and I first moved in together, in 2017, one of our first.. "sprited debates" was, of course, over personal finance. During this debate, I realized that most of the uneasiness was coming from the fact that neither of us budgeted. 

I had always been a conservative spender by nature - I don't do much personal shopping, and I was pretty careful with what I used for entertainment, groceries, etc. My wife was the opposite - she got a lot of joy out of the "hunt" experience of vintage shopping, for example, and was an excellent gift-giver. However, when we moved in together we also combined checking accounts, and any budgeting just become emotional reactions to account balances and credit card statement amounts.

We both wanted to save money, particularly for a house down payment, but it was hard to even see our spending against what it should be to know if we were on track. We traveled frequently, so our discretionary spending was not consistent. Some months we brought home a decent percentage as savings, others we did not. 

I realized that we needed a budgeting tool specific to our situation. We had both used Mint before, but it had a pretty rigid set of categories and limited budget-setting capabilities. Particularly, we wanted to feel like we had some money that was 'ours alone'. Coincidentally, I also had a colleague who joined the team at Plaid, and mentioned a developer API that allows you to pull down transactions and balances. So armed with that knowledge, I set out to build a budget.

## Objective
Build a budgeting tool that worked for both my wife and I, and allowed us to set and achieve savings goals. 


## Approach

This was not a super structured project - I fiddled with it over a winter break when I wanted to ~~escape my family~~ have some alone time. However, I roughly followed the below steps.

XX - Add chevrons for below:
1. Requirements Collection
2. MVP Mockup
3. API Development
4. Testing & Refinement
5. Feature Addition - big expense planning, automatic categorization, 


### Requirements Collection

Up front, we just wrote out some things we wanted, then separated them into must-have vs nice-to-have. (My wife is a software designer, and taught me a thing or two about design thinking and agile development). This was basically to outline what would go into an 'MVP'.

**Must-Haves**
* Annually review income vs monthly budget categories
* Review monthly budget amounts throughout the year and adjust
* Continually refresh bank and credit card transactions, as well as bank balances
* Manually categorize transactions in a "form" type interface
* Flag business expenses and track against expense forms
* Split out budgeted discretionary spending separately for me and my wife
* Set annual savings goals and review monthly bank balances against them

**Nice-to-Haves**
* Plan 'big expense' categories annually, in addition to monthly categories
* Automatically categorize transactions based on description
* Roll over savings for specific categories month-to-month
* XX - add additional


## Outcome
**We use this every month, four years after I first made it**. That's the biggest compliment I can pay any system I've set up for myself. 

There have been continuous improvements, and I've had to maintain and update the backend script (particularly the API call and token mechanism), but we still use it! 


# What I Took Away

## Technology & Techniques
* 

## Lessons Learned
* 

---

# Code

---

# Detail - Ongoing Data Refreshment