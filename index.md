---
layout: default
title: Characterizing Online Attitudes, Expectations, and Concerns about Novel Medical Treatments
description: USC CKIDS Datafest 2022
---

## Motivation

Novel, or hypothesized medical treatments, such as COVID-19 vaccines and male contraception, are regularly discussed on social media. For example, on the AskReddit subreddit, questions of the form “”Would you take [x] if it existed?”” Aside from willingness to use these novel treatments, the answers to these questions contain important clues to peoples' latent concerns and barriers to adoption of novel medications. Understanding them can provide crucial information about how to introduce, communicate, and counsel about new medications when they come to market. 

- Novel medical treatments are regularly discussed in social media
- Analyzing this data will help us understand:
  - Concerns about the treatment
  - Barriers to adoption
  - Evolution of concerns over time
- This information will help us to:
  - Learn how to communicate about novel treatments
  - Understand how to address concerns
  - Counsel patients about novel treatment options


## Data

**Source of Data:**
- The data was collected by searching for relevant queries such as `male birth control` on Google, with the filter `site:reddit.com`.
- Around **74** posts over **10** years were identified, and the data was exported using [PRAW](https://praw.readthedocs.io/en/stable/_).
- The data consists of the submissions with the comments in it. Also, the user history of the commenters was downloaded.

**Data files**
- submissions : 74 posts (in .pkl files)
- users: 21627 user history of those who commented on the submissions (in .pkl files)


## Problem

We set out to answer the following questions:
- What years were users most engaged in the topic of male contraception?
- What general topics can we identify in the discussions on Reddit?
- What are the sentiments of the users when they write about male contraception?
- Are there users who engaged with the topic of male birth control over multiple years?
- Has their sentiment shifted or remained the same over the years?
- Do users mention specific locations?
- What is the context around which users mention a location?


## Methods

We attempted to answer these questions through the following methods:
- Creating a histogram of users commenting on multiple posts (Jae)
- Sentiment analysis through the years (Jae)
- Topic model of comments (Lei)

  Topic Models, in a nutshell, are a type of statistical language models used for uncovering hidden structure in a collection of texts, which is used to classify text in a document to a particular topic.

  Tools: `nltk` and `gensim`

- Timeline (Sanjana)
  - Users posts by year
  - Comments in submissions by year
- Locations in comments using Named Entity Recognition (Sanjana)
  - How many users mention a location
  - Histogram of locations by user
- Predicting gender from comments (Michael)

## Results

### Data Exploration

**Number of posts by subreddits** <br>
![Number of posts by subreddits](images/data-exploration/posts_by_subreddit.png)

**Number of posts by year** <br>
![Number of posts by year](images/data-exploration/posts_by_year.png)

**Number of comments by year** <br>
![Number of comments by year](images/data-exploration/comments_by_year.png)

**Users commenting in multiple posts**

![histogram](images/data-exploration/Histogram.png)

**Sentiment Analysis**

![histogram](images/data-exploration/negative_wordcloud.png)
![histogram](images/data-exploration/positive_wordcloud.png)
![histogram](images/data-exploration/Sentiment_scores.png)

### Topic Model

#### Text Preprocessing

As part of preprocessing, we will:

- Tokenize documents (split the documents into tokens) and remove noise. 
- Lemmatize the tokens. `nltk.stem.wordnet.WordNetLemmatizer`
- Compute bigrams. `gensim.models.Phrases`
- Compute a bag-of-words representation of the data. `gensim.corpora.Dictionary`


<details>
  <summary>Click to expand for more details on text preprocessing</summary>

  **Tokenize and Remove Noise**

  First, we tokenized the text using the tokenizer `gensim.utils.tokenize()` from `Gensim`. We removed the following tokens or comments as they don’t tend to be useful, and the comments contain a lot of them.

  - stopwords: `gensim.parsing.preprocessing.STOPWORDS`
  - single character tokens and numeric tokens 
  - URLs: regular expression `r'http\S+'`
  - common and rare words: filter out words that occur less than 5 documents, or more than 30% of the documents.
  - [deleted] comments

  **Lemmatize the Tokens**

  We found some words with the same meaning could occur in one topic, especially gender words. For example, our topic model could generate a topic containing `female`, `women`, and `woman` at the same time. Gender words are important for our model because we are studying topics like birth control, but words with the same meaning could appear in a topic, which will harm the informativeness of our topic model.  

  We use the WordNet lemmatizer from `NLTK`. A lemmatizer could produce more readable words and help our topic model generate more informative topics. This is very desirable in topic modeling.

  **Bigrams**

  We find bigrams in the documents(comments). Bigrams are sets of two adjacent words. Using bigrams we can get phrases like "birth_control" in our output (spaces are replaced with underscores); without bigrams we would only get "birth" and "control".

  Then, add bigrams into our corpus, because we would like to keep the words "birth" and "control" as well as the bigram "birth_control". The following block shows part of phrases found by the bigram model

  > ['fda_approval', 'lasts_years', 'test_subjects', 'sperm_count', 'birth_control', 'family_planning', 'tl_dr', 'reproductive_organs', 'shoot_blanks', 'bullet_proof', 'proof_vest', 'sex_drive', 'paying_child', 'child_support', 'hell_yes', 'female_birth', 'protect_stds', 'male_birth', 'proven_safe', 'birth_controls', 'shooting_blanks', 'approved_fda', 'want_kids', 'hormonal_birth', 'use_condoms', 'shoot_bulletproof', ...]

  The output of topic model will show that bigrams indeed improved our model to generate better topics. For instance, some topics contains bigrams `birth_control` and `male_birth`.  

  **Bag-of-words**

  Bag-of-words model is an approach to represent a document as a vector. Under the bag-of-words model each document is represented by a vector containing the frequency counts of each word in the dictionary.

  For example, assume we have a dictionary containing the words `['coffee', 'milk', 'sugar', 'spoon']`. A document consisting of the string "coffee milk coffee" would then be represented by the vector `[2, 1, 0, 0]`. One of the main properties of the bag-of-words model is that it completely ignores the order of the tokens in the document that is encoded, which is where the name bag-of-words comes from.

  Here, we created a dictionary representation of the documents with `gensim.corpora.Dictionary` and `doc2bow()` method could create a corpus as the input of our topic model. 

</details>

**Topic Model Training**

Our topic model is based on Latent Dirichlet allocation (LDA). LDA is a generative statistical model that allows sets of observations to be explained by unobserved groups that explain why some parts of the data are similar.

Hyperparameter tuning shows the LDA model with 8 topics perform best. Hence, we used `gensim.models.Lda` to train a LDA model with 8 topics where each topic is a combination of keywords, and each keyword contributes a certain weightage to the topic. 

<details>
  <summary>Click to see the details on hyperparameter tuning</summary>

  We are ready to train the LDA model. We will first discuss how to set some of the training parameters.

  First of all, the elephant in the room: how many topics do we need? Let’s perform a series of sensitivity tests to help determine the following model hyperparameters

  - Number of Topics $K$
  - Dirichlet hyperparameter $\alpha$: Document-Topic Density
  - Dirichlet hyperparameter $\beta$: Word-Topic Density

  We’ll perform these tests in sequence, one parameter at a time by keeping others constant and run them over the two different validation corpus sets. We'll use topic coherence, `C_v`, as our choice of metric for performance comparison. We found the default setting, `alpha='symmetric', \beta='auto'`, perform best, so we will keep this setting to explore the optimal number of topics. 

  Pick the model that gave the highest `C_v`. In this case, we picked $K=8$ with highest average topic coherence 0.6425.

  ![hyperparameter tuning](images/topic-model/c_v_%5B'symmetric'%5D_%5B'auto'%5D.png)

</details>

**Output of Topic Model**

Then, let's explore the 8 topics generated by our topic models. The following cell show combinations of keywords and the weightage of keywords for each topic. 

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

The above results are hard to read, so we created interactive visualization with [`pyLDAvis`](https://pyldavis.readthedocs.io/en/latest/readme.html) package to interpret the topics. The interactive graph provides:

- a left panel that depicts a global view of the model (how prevalent each topic is and how topics relate to each other);
- a right panel containing a bar chart – the bars represent the terms that are most useful in interpreting the topic currently selected (what the meaning of each topic is).

<details>
  <summary>Click to expand for more details on topics' visualization</summary>

  On the left, the topics are plotted as circles, whose centers are defined by the computed distance between topics (projected into 2 dimensions). The prevalence of each topic is indicated by the circle’s area. On the right, two juxtaposed bars showing the topic-specific frequency of each term (in red) and the corpus-wide frequency (in blueish gray). When no topic is selected, the right panel displays the top 30 most salient terms for the dataset.

  Relevance is denoted by $\lambda$, the weight assigned to the probability of a term in a topic relative to its lift. When λ = 1, the terms are ranked by their probabilities within the topic (the ‘regular’ method) while when λ = 0, the terms are ranked only by their lift. The interface allows to adjust the value of λ between 0 and 1.

  For more details about topics' visualization, please see this paper, [LDAvis: A method for visualizing and interpreting topics](https://nlp.stanford.edu/events/illvi2014/papers/sievert-illvi2014.pdf).

</details>

For instance, if we choose topic 1 on the right panel, we can see the top most relevant terms for Topic 1 contains, female, pill, male, effect, birth_control, etc. And if we choose the term "pill", the right panel will show the conditional topic distribution given the term "pill". Obviously, "pill" is mentioned more in topic 1 than other topics.

<iframe id="lda_vis" 
	title="Topic Model Visualization" 
	width="100%" height="700px" scrolling="yes" frameborder="0"
	src="assets/html/lda_8.html">
</iframe>

**Location mentions**

![Location mentions](images/data-exploration/location_mentions.png)

**Number of times US states mentioned in comments**

<iframe id="usagraph" 
	title="State Mentions" 
	width="100%" height="700px" scrolling="yes" frameborder="0"
	src="assets/html/usagraph.html">
</iframe>

**Top word used in the most-mentioned US states**

Texas <br>
![Number of posts by subreddits](images/data-exploration/texas_top_words.png)

California <br>
![Number of posts by year](images/data-exploration/california_top_words.png)

New York <br>
![Number of comments by year](images/data-exploration/new_york_top_words.png)

Florida <br>
![Number of comments by year](images/data-exploration/florida_top_words.png)




**Data exploration**
Histogram of users commenting in more than one reddit submission
Vader Sentiment analysis of comments for each submission
Box plot and trend line for overall sentiment over time.


### Timeline 

## Conclusion

