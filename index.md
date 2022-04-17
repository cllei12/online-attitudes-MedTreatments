---
layout: default
title: Characterizing Online Attitudes, Expectations, and Concerns about Novel Medical Treatments
description: USC CKIDS Datafest 2022
---

## Motivation

## Problems

## Data

**Source of Data**

Manually serched in reddit for topics regarding male birth control, and downloaded the post/thread submissions with the comments in it. Also, downloaded the user history of those who commented in it.

**Data files**

- submissions : 74 posts/threads (in .pkl files)

- users: 21627 user history of those who commented on the submissions (in .pkl files)

**Data exploration** (*should be in Results section???)*

Histogram of users commenting in more than one reddit submission. 

Vader Sentiment analysis of comments for each submission. 

Box plot and trend line for overall sentiment over time.

## Methods

**Tasks in Week1 slides** 

- Histogram of users commenting on multiple posts (Jae, submissions)
  - Log scale
- Topic model of comments (Lei, gensim)
  - 5, 10, 20 topics
- Timeline (Sanjana, by year)
  - Users posts by yearComments in submissions by year
  - How long people are engaging
- Locations in comments (Sanjana)
  - spaCy NER
  - How many users mention a location
  - Histogram of locations by user 
    Starting from 0
- Gender from comments (Michael)

## Results

### Data Exploration

**Histogram**

![histogram](images/data-exploration/Histogram.png)

**Sentiment Analysis**

![histogram](images/data-exploration/negative_wordcloud.png)
![histogram](images/data-exploration/positive_wordcloud.png)
![histogram](images/data-exploration/Sentiment_scores.png)

### Timeline 



### Topic Model

Topic modeling is a method for unsupervised classification of documents, similar to clustering on numeric data, which finds some natural groups of items (topics).

**Output of Topic Model**

```
Topic 1: 0.057*"female" + 0.034*"pill" + 0.029*"male" + 0.023*"effect" + 0.021*"birth_control" + 0.011*"think" + 0.009*"want" + 0.009*"taking" + 0.008*"control" + 0.007*"like"

Topic 2: 0.039*"male" + 0.038*"female" + 0.021*"effect" + 0.014*"birth_control" + 0.010*"condom" + 0.009*"risk" + 0.009*"control" + 0.008*"option" + 0.008*"people" + 0.007*"use"

Topic 3: 0.028*"male" + 0.025*"yes" + 0.021*"child" + 0.020*"condom" + 0.020*"want" + 0.016*"female" + 0.009*"think" + 0.009*"kid" + 0.008*"people" + 0.008*"option"

Topic 4: 0.018*"effect" + 0.015*"male" + 0.014*"testosterone" + 0.012*"hormone" + 0.010*"pill" + 0.009*"people" + 0.009*"drug" + 0.009*"female" + 0.008*"like" + 0.008*"level"

Topic 5: 0.019*"like" + 0.018*"iud" + 0.017*"year" + 0.012*"time" + 0.011*"pain" + 0.011*"pill" + 0.010*"day" + 0.010*"period" + 0.009*"got" + 0.008*"month"

Topic 6: 0.015*"like" + 0.010*"comment" + 0.010*"thing" + 0.010*"male" + 0.010*"know" + 0.009*"people" + 0.008*"right" + 0.007*"question" + 0.007*"sure" + 0.007*"fucking"

Topic 7: 0.023*"control" + 0.018*"sperm" + 0.017*"study" + 0.015*"removed" + 0.014*"male" + 0.013*"pill" + 0.013*"like" + 0.013*"effect" + 0.012*"trial" + 0.012*"male_birth"

Topic 8: 0.048*"vasectomy" + 0.014*"procedure" + 0.012*"reversible" + 0.009*"vasalgel" + 0.008*"year" + 0.008*"think" + 0.007*"absolutely" + 0.007*"yeah" + 0.007*"surgery" + 0.007*"kid"
```

**Topic Model Visualization**

<iframe id="lda_vis" 
	title="Topic Model Visualization" 
	width="100%" height="700px" scrolling="yes" frameborder="0"
	src="lda_8.html">
</iframe>


### Locations in comments



## Conclusion