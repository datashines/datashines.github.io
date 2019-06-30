---
layout: post
title: Youtube Comments Machine Learning
---

## Introduction

Youtube is the biggest and most popular steaming platform at the time of writing this post. For every 
youtube video, viewers post comments. I am a personal fan of reading many of those funny ones.
In general, these comments also give away the popular topics of discussion, and a sentiment around the 
video. In this blog post, I share my work in utilising these comments to provide the most popular topics
that are being talked about in the comments, and a general sentiment based on the language of these comments.
A by-product of this project is a [chrome extension](https://github.com/arj7192/yc-ml-chrome-ext), that I haven't 
published yet, but one can locally clone the repo an use the extension locally.

## Data Access

We use the [youtube data api](https://developers.google.com/youtube/v3/docs/comments/list)
 to pull the list of comments for a given youtube video id. 
 Note: this has a quota limit per day. As the api response we get a list of strings in return
 for the video id as the api request parameter.


## Word count

One of the indicators of topics being discussed in the comments is the most frequent words (except the stop-words). We follow
a 2-step process here:
- Lemmatize the text.
- Compute word frequencies across comments after lemmatization.

## Topic Modelling

[Latent Semantic Analysis](https://en.wikipedia.org/wiki/Latent_semantic_analysis) is one of the most popular and effective 
classical topic modelling technique in literature.
Here are the steps we take to fetch topics out of the youtube comments:

1 . Tokenize the text i.e. lemmatize -> get rid of stop words / punctuations / pronouns, etc.
`(Note : our current approach is limited to doing so for assuming the comments are in English)`

![]({{ site.baseurl }}/data/2019-06-30-Youtube-Comments-Machine-Learning/tokenize.png)

2 . We need to move from words to numbers, hence we replace words with their count vectors i.e. 
how many times do they occur in each document, in this case, each comment.

![]({{ site.baseurl }}/data/2019-06-30-Youtube-Comments-Machine-Learning/vectorize.png)

3 . Finally, we are ready to instantiate an LSA/LSI model. What does this model do ? It basically
transforms our document-word_count matrix into a multiplication of 

- document-topic,
- topic-topic, and
- topic-word_count 

matrices, which essentially is and [SVD](https://en.wikipedia.org/wiki/Singular_value_decomposition) operation.

This gives us the _k_ most important 'topics' across the documents (comments in our case)

![]({{ site.baseurl }}/data/2019-06-30-Youtube-Comments-Machine-Learning/lsi.png)

4 . Once the model is trained, we can now look at what topics are generated :

![]({{ site.baseurl }}/data/2019-06-30-Youtube-Comments-Machine-Learning/lsi_result.png)

5 . We also explore slightly more advanced topic modelling approaches like the [LDA](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation). 
Will write more on that and other advanced deep learning based approaches in the next blogs though.

![]({{ site.baseurl }}/data/2019-06-30-Youtube-Comments-Machine-Learning/lda.png)


## Sentiment Analysis

As a first step, instead of training a sentiment model from scratch (which we will be doing next), I 
researched the existing libraries, and nltk.vader ([library](https://www.nltk.org/_modules/nltk/sentiment/vader.html) and 
[paper](http://comp.social.gatech.edu/papers/icwsm14.vader.hutto.pdf)) stood out as a reasonable baseline. How does this work ?
[This](http://datameetsmedia.com/vader-sentiment-analysis-explained/) is an excellent short read for the answer but TL;DR --> first each word is given a sentiment value (between -4 to 4) based
on an sentiment intensity map a.k.a the VADER sentiment lexicon. Then these numerical values are 
aggregated across a sentence with required normalization to score each sentence between -1 and 1.
Vader additionally uses these heuristics to augment these scores' intensity:

1. punctuation - “I like it.” vs “I like it!!!”
2. capitalization - “amazing performance.”  vs “AMAZING performance.”
3. degree modifiers - “effing cute” vs “sort of cute”
4. shift in polarity due to “but” - _self-explanatory_  
(all sentiment-bearing words before the “but” have their valence reduced to 50% of 
their values, while those after the “but” increase to 150% of their values.)
5. examining the tri-gram before a sentiment-laden lexical feature to catch polarity negation
(From the paper - _By examining the tri-gram preceding a sentiment-laden
lexical feature, we catch nearly 90% of cases where negation flips the polarity of the text. A negated sentence
would be “The food here isn’t really all that great”._)


## Results

First, we put these three tasks (word count, topic model, sentiments) as apis using Flask + Python (Code [here](https://github.com/arj7192/yc-ml)).
To make this really usable, we created a chrome extension. Once you are on a youtube video page,
you can click this extension, and it should hopefully show results from all these 3 apis as can be
seen in these example screenshots:

----

![]({{ site.baseurl }}/data/2019-06-30-Youtube-Comments-Machine-Learning/res1.jpg)

----

![]({{ site.baseurl }}/data/2019-06-30-Youtube-Comments-Machine-Learning/res2.jpg)

----


Here is the link to the [chrome extension repo](https://github.com/arj7192/yc-ml-chrome-ext) 
again. Clone the repo, upload the folder to chrome://extensions and should be good to go !


## Acknowledgements

Big thanks to [Akshay Kumar](https://www.linkedin.com/in/skakshay/) for discussing the initial ideas around this project.
Special thanks to [Neeraj Lajpal](https://www.linkedin.com/in/neerajlajpal/) for his help with creating the chrome extension. 
Link to his [original repo](https://bitbucket.org/bitwick/bitflip_pilot_chrome/src/master/) that we used for reference.


## Next Steps
1. More advanced models on topic modelling as well as sentiment analysis to replace with the current ones.
2. Better UI for the chrome extension that shows the high level stats more intuitively and is user-friendly.



Thank you for reading this post. Hope it was helpful !

