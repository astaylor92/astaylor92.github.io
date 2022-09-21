---
layout: single
title: "Song Popularity Prediction"
excerpt: Marrying up last.fm, Spotify, and Musicbrainz data and understand if/how sonic features make a song popular
classes: wide
skills:
---

# What I Did

## Context
This project was something I'd been interested in for a while. I've toyed around with Spotify's API here and there, but most of my machine-learning ideas require having some sort of 'database', and the majority of Spotify's API is on-demand. I'd have to know the artist, song, playlist, etc. that I wanted to search before querying the API.

Enter [musicbrainz](https://musicbrainz.org/). This site's main goal is to "become the ultimate source of all music information". This includes taking down a record of all music recordings, cataloguing them, and creating unique IDs. The reason this was interesting to me is because I'd **also** found out that last.fm's API will provide a track's song listens as well as the musicbrainz id associated with them. Also, you can download a mirror of this database, and effectively access close to every song ever made.

Finally, I'd always been fascinated with using Spotify API's [audio analysis](https://developer.spotify.com/documentation/web-api/reference/#/operations/get-audio-analysis) to see if the audio features of a track can help us understand its popularity. However, Spotify's API does not provide a real measure of popularity - just their vague 1-100 scale of popularity, which is point-in-time. 

This led us to the question - could we marry up Spotify audio features and last.fm play count data, using musicbrainz as a database and cross-reference key, to analyze popularity?

## Objective
Join together Spotify and last.fm datasets and analyze the importance of audio features in song plays.

## Approach
The overall approach for this was fairly straightforward, but the devil was in the details of the data collection and transformation. I'll provide an overview of approach I took here, and I'll dive into some of the challenges in the 'What I Took Away' section.

<p align="center">
    <img src="/images/songpopularity/steps.png"  width="80%" height="80%">
    <br>
    <span class="figure-caption"> Project Steps </span>
</p>

**Goals of Each Step**
1. *Data Collection & Merging* - Develop 'database' of tracks from musicbrainz filtered to a sample year (2019), call Spotify and last.fm APIs to collect dependent variable and features, merge datasets using fuzzy logic as needed
2. *EDA & Data Preparation* - Evaluate data cleanliness, conduct univariate and multivariate analyses, analyze multicollinearity, encode categorical variables, and split data in test-train sets
3. *Model Selection* - Score and evaluate model families with hyperparameter tuning, select a go-forward model family for predictive power, select a go-forward model for feature comparison
4. *Model Evaluation - Predictive Power* - Evaluate best model family for predictive power, understand how well popularity can be predicted from audio and artist features
5. *Model Evaluation - Feature Importance* - Evaluate best model family for feature importance, understand which features have the most significant relationships with song popularity.

**Note** - a detailed PDF report on this is included at the bottom, in the 'Detail' section.

## Outcome
Ultimately, the model strengths were all fairly weak, from a performance standpoint, but we're able to learn a thing or two about audio features of popular songs.

**Performance**

<p align="center">
    <img src="/images/songpopularity/model_performance.png"  width="80%" height="80%">
    <br>
    <span class="figure-caption"> Model Scores, Test Data </span>
</p>

When we look at the most preditive model family, Gradient Boosted Regression, we see that it still has a fairly low R-squared value. However, this result might also tell us simply that it's far more complex to predict popularity than we thought. More specifically, this might tell us that trying to regress an exact play count (as opposed to 'hit or not' categorizations which are often used in this type of analysis), is very complex.

**Features**

To analyze features, we looked at our LASSO model output.

<p align="center">
    <img src="/images/songpopularity/feature_scores.png"  width="50%" height="50%">
    <br>
    <span class="figure-caption"> Feature Scores, LASSO model </span>
</p>

* **Ignored Features** - Valence, whether or not a song has a discernable time signature, and a number of keys (D, Eb, E, F\#, and G) did not have significant enough impact to be kept by the Lasso algorithm's regularization
* **Significant Feature - Artist Followers** -  The number of followers an artist has was highly significant, with a positive coefficient indiciating that each 1 additional follower may lead to as much as 0.04 additional listeners to a particular song
* **Significant Feature - Artist Popularity** - This similarly tells us that Spotify's measure of artist popularity (vaguely described as calculated from the popularity of the artists' tracks)
* **Significant Feature - Speechiness** - This showed a negative relationship, meaning that the more talking a track contains, the fewer plays it gets
* **Significant Feature - Liveness** - Interestingly, this showed a positive relationship, meaning the more live a track was, the more popular it was
* **Significant Feature - Acousticness** - This showed a negative relationship, meaning that the more acoustic a track is, the fewer plays it gets
* **Significant Feature - Danceability** - This showed a negative relationship, which was sur- prising - one might think that a more ‘danceable’ track is more likely to be popular
* **Significant Feature - Key of C#** - This showed a negative relationship, which makes some sense. This was the only significant feature related to keys - many others (especially popular keys) were excluded from the model. It seems that this is a less popular key to compose in (fewer songs were in C# than many other keys), and led to lower play count
* **Significant Feature - Instrumentalness** - This showed a negative relationship, meaning that the more interumental a track is, the fewer plays it gets. This might make some sense - it does seem that many people enjoy tracks with lyrics

# What I Took Away

## Technology & Techniques
* *API calling using `requests` and `time`* - Although I was vaguely aware of pre-existing libraries for API calls, I wanted to get my hands dirty with `requests`. All API calls were written there, using `time` to add waiting periods as-needed.
* *Fuzzy matching using `fuzzywuzzy`* - I was aware of fuzzy matching logic from my time using Alteryx, and `fuzzywuzzy`'s implementation was very useful when joining up the Spotify and last.fm data. Unfortunately, this was necessary, since last.fm didn't end up having many compatible musicbrainz song-level IDs, forcing me to match album IDs then fuzzymatch the track names.
* *Flowcharts with diagrams.net* - There's probably better apps out there, but for a free Google Drive-compatible Microsoft Visio knockoff, this was pretty dang good
* *scikit-learn for model stuff* - Classic. I used HistGradientBoostingRegressor, RandomForest, Lasso, Ridge, and Linear regressions. I also used GridSearchCV and KFolds to write up a consistent nested CV for the inital model eval.
* *pandas pandas pandas* - Enough said. One new thing this time around was leveraging `.apply()` and dictionaries within rows to iterate through the dataframe and apply fuzzy matching logic.

## Lessons Learned
* **Data science is sometimes more like 98% data wrangling** - Look, I knew most projects require 80% data wrangling, cleaning, etc. However - each step of the ETL process here came with like 5 curveballs, so by the end of the day, the vast majority of my time was spent just trying to get the data together.
* **Proper database schema documentation is great** - Musicbrainz had fantastic documentation of the different terms in their database, as well as documentation of the schema, [here](https://musicbrainz.org/doc/MusicBrainz_Database/Schema). This made the joining, while tedious, WAY easier than fumbling around in tables I knew nothing about.
* **Helper functions help** - Since I did so much joining and reviewing, I wrote up a few dataframe summary helper functions that proved super useful throughout ETL.
* **Still left bias on the table** - Because there was a fair amount of loss between my initial dataset and records which were returned from all APIs and survived fuzzy-matching, there's pretty much no way the final dataset isn't biased in some way. More work would be needed to investigate, and even more to rectify this.
* **Complexity of human decision making** - All that work for a 0.3 R-squared?? Jokes aside, I actually wasn't too surprised to find that it's hard to predict human behavior (play counts), especially to a degree of magnitude. 
* **APIs are always tougher than they seem** - I do this every time. "Oh, they've got an API - it'll be super easy to get the data". That's true, when you're not marrying up multiple data sources with codes and ID fields that may or may not match. Woof.

---

# Notebooks

[Github Repo](https://github.com/astaylor92/songpopularity)

---

# Detail

For this one, I wanted to do a bit of a deep-dive on the ETL process, to give a better idea of the steps involved in collecting and joining the data. This covers, in detail, the steps taken to complete step 1 - Data Collection & Merging. I'll walk through the steps taken, in sequence, and comment on some of the challenges.

## Musicbrainz - Building the Database

First, I needed to "build the database". This was where musicbrainz's database came in handy. Technically, I could have set up the full PostgreSQL database, but that would've taken quite a bit of time, and at first, I only thought I'd need a few tables. Their database schema is below:

<p align="center">
    <img src="/images/songpopularity/mb_schema.png"  width="80%" height="80%">
    <br>
    <span class="figure-caption"> Musicbrainz Database Schema </span>
</p>

The number of tables, I soon learned, grew from a single 'tracks' table with track IDs to a combination of 'album'-level and 'track'-level tables. I drew out a full, field-level diagram, but it's... not legible in this format. So a set of major steps with simplified diagrams is below.

### Step 1 - Develop album-level dataset, starting with 'release' table

<p align="center">
    <img src="/images/songpopularity/album_diagram.png"  width="80%" height="80%">
    <br>
    <span class="figure-caption"> Step 1 - Album-Level </span>
</p>

1. Join with artist information on the release ID to get the name of the artist for later searching
2. Join with release country information on release ID to add in year of release
3. Join to medium information (think CD vs tape vs digital) on release ID to get medium ID

**Note on Loss** - The only place during the database creation with loss was the join to release country information, which resulted in about 2% record loss.

### Step 2 - Develop track-level dataset, starting with 'track' table

<p align="center">
    <img src="/images/songpopularity/song_diagram.png"  width="80%" height="80%">
    <br>
    <span class="figure-caption"> Step 2 - Song-Level </span>
</p>

1. Join with recording to get recording info (think individual recording session) and recording ID
2. Join with ISRC table to get ISRC for API calls

### Step 3 - Join and filter

<p align="center">
    <img src="/images/songpopularity/final_join.png"  width="70%" height="70%">
    <br>
    <span class="figure-caption"> Step 3 - Final Join </span>
</p>

1. Join album & track-level datasets together
2. Filter for 2019 songs, and take 10% sample

### Output of Database Creation

The result of these joins was 900kn records, representing a random sample of songs released in 2019.

## Spotify - API Calls

Using the Spotify API was the real limiter. Many of the APIs had to be done in batches of 50-100 or individual album/track. There was also an invisible rate limit, so code had to be written to read the response headers RETRY statement and make sure pauses were introduced to avoid total timeouts. The diagram below shows the calls and their responses. 

<p align="center">
    <img src="/images/songpopularity/spotify_api.png"  width="80%" height="80%">
    <br>
    <span class="figure-caption"> Spotify API Calls </span>
</p>

**Note on Loss** - Although 900k records were introduced, due to rate limits, only 50k songs were retrieved from the first API call (ISRC > TRACK URI). From there, due to Spotify's URI system, very minimal loss was experienced in the remaining calls.

## last.fm - API Calls

This was an even trickier call. Initially, I had understood that the last.fm database had musicbrainz IDs for each song. However, I came to find out that their capability for that was discontinued, and they only match MBIDs for albums. This required me to first pull each album, then query for their songs based on the album and track name, which resulted in some loss.

<p align="center">
    <img src="/images/songpopularity/lastfm_api.png"  width="65%" height="65%">
    <br>
    <span class="figure-caption"> last.fm API Calls </span>
</p>

**Note on Loss** - All 50k records from the Spotify API were used, which resulted in 44k albums. The first step, MBID > ALBUM INFO, resulted in about 14% loss. Further, ALBUM INFO > SONG PLAYS resulted in 20% loss. Unfortunately, it looks like last.fm's API and database storage were not as strong as Spotify's.


## Final Join and Fuzzy Match

Finally, I got to marry it all together! First, I joined the query results together using release ID. Then, I used `fuzzywuzzy` to match the track names. At long last, I had a full dataset. 

<p align="center">
    <img src="/images/songpopularity/api_join.png"  width="75%" height="75%">
    <br>
    <span class="figure-caption"> Final Join - All Data </span>
</p>

**Note on Loss** - The final dataset was ~29k records - the 50k records from the Spotify call, reduced by ~40% from the last.fm API.