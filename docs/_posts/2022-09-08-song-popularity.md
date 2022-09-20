---
layout: single
title: "Song Popularity Prediction"
excerpt: Marrying up last.fm, Spotify, and Musicbrainz data and understand if/how sonic features make a song popular
classes: wide
skills:
---

# TODO - Fill in all 'XX's
---
# What I Did

## Context
This project was something I'd been interested in for a while. I've toyed around with Spotify's API here and there, but most of my machine-learning ideas require having some sort of 'database', and the majority of Spotify's API is on-demand. I'd have to know the artist, song, playlist, etc. that I wanted to search before querying the API.

Enter musicbrainz. I'd heard of the 'Million Song Dataset' from echonest before, but hadn't done of a lot of research ever since looking for it once and finding out it had been taken down (Spotify acquired their IP and eventually the site went away). This time around, when I was looking for it, I came across [musicbrainz](https://musicbrainz.org/). This site's main goal is to "become the ultimate source of all music information". This includes but is definitely not limited to taking down a record of all music recordings, cataloguing them, and creating unique IDs. The reason this was interesting to me is because I'd **also** found out that last.fm's API will provide a track's song listens as well as the musicbrainz id associated with them. Also, you can download a mirror of this database, and effectively access close to every song ever made.

Finally, I'd always been fascinated with using Spotify API's [audio analysis](https://developer.spotify.com/documentation/web-api/reference/#/operations/get-audio-analysis) to see if the audio features of a track can help us understand its popularity. However, Spotify's API does not provide a real measure of popularity - just their vague 1-100 scale of popularity, which is point-in-time. 

This led us to the question - could we marry up Spotify audio features and last.fm play count data, using musicbrainz as a database and cross-reference key, to analyze popularity?

## Objective
Join together Spotify and last.fm datasets and analyze the importance of audio features in song plays.

## Approach
The overall approach for this was fairly straightforward, but the devil was in the details of the data collection and transformation. I'll provide an overview of approach I took here, and I'll dive into some of the challenges in the 'What I Took Away' section.

XX - Approach chevron boxes

**Goals of Each Step**
XX - add goals

## Outcome
XX - add outcome

# What I Took Away

## Technology & Techniques

## Lessons Learned

---

# Notebooks