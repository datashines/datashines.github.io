---
layout: post
title: Using Neural Nets for Audio Events Detection
---

Audio events detection as the name suggests is the task of detecting 1 or more audio events in an audio clip of a 
certain duration. In this post, we limit our discussion to 1 audio event in an audio clip of a fixed duation of 5 seconds.  

!audio[ This ]({{ site.baseurl }}/data/2018-05-13-Audio-Events-Detection/traffic_sample_1.wav) is for exmaple an audio clip with the audio event _traffic_  

## The Idea

> Literature has a well established pipeline for the task:   

![]({{ site.baseurl }}/data/2018-05-13-Audio-Events-Detection/ml_pipeline.png)

> State-of-the-art configuration before neural network era looked like :  

![]({{ site.baseurl }}/data/2018-05-13-Audio-Events-Detection/state-of-the-art.png)

> Feature transformation in the above configuration has particularly been a problem for reasons like this one:  

![]({{ site.baseurl }}/data/2018-05-13-Audio-Events-Detection/problem-with-feature-trans.png)

> That's where 1-D Convolutional Neural Networks could be a game changer :   

![]({{ site.baseurl }}/data/2018-05-13-Audio-Events-Detection/CNN-approach.png)

> 1-D kernels could bring out the most important frames that would survive the max-pool reduction layer :  

![]({{ site.baseurl }}/data/2018-05-13-Audio-Events-Detection/CNN-feature-trans.png)  

----
In case you were wondering, Fully-Connected Nets would suffer from the same feature-transformation limitation as the traditional methods, and it was tested to perform mcuh worse compared to a CNN model.


## Dataset
[UrbanSounds8K](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html) dataset is a monophonic sounds dataset i.e. each audio clip consists just one audio event (labelled). This dataset was used for the audio events detection task, providing with 8732 data samples and consisting of 10 output classes (audio events). Those are:  

* Air Conditioner Sound
* Car Horn
* Children Playing
* Dog Bark
* Drilling
* Engine Idling
* Gun Shot
* Jackhammer
* Siren
* Street Music  


## Feature Engineering
> Mel Frequency Cepstrum Coefficients (MFCCs) are renowned features for audio-related tasks.    

![]({{ site.baseurl }}/data/2018-05-13-Audio-Events-Detection/MFCC2.png)

> Calculating MFCCs include a number of steps as shown below   

![]({{ site.baseurl }}/data/2018-05-13-Audio-Events-Detection/MFCC1.png)

## Machine Learning Model
> Now that we have the data, features transformed, we can finally talk about the whole machine learning pipeline:   

![]({{ site.baseurl }}/data/2018-05-13-Audio-Events-Detection/CNN-arch.png)

## Results
> Not so surprisingly, CNN outperforms DNN outperforms kNN outperforms Random Choice Classifier:   

![]({{ site.baseurl }}/data/2018-05-13-Audio-Events-Detection/results.png)
