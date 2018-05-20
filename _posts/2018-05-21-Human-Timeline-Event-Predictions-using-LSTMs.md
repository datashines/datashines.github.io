---
layout: post
title: Deep learning for long-term predictions
---

Sentiance uses machine learning to extract intelligence from smartphone sensor data such as accelerometer, gyroscope and 
location. This intelligence comes in the forms of sensor based activity detection, map matching, driving behavior, venue 
mapping and more.

### Detection vs Prediction

The obvious next step was to go from simply detecting what you are doing, to predicting what you will be doing in the future. 
Knowing our near-term future allows us to explain the _intent_ of our current situation. For example, if one is *detected* to be
currently at home, and is *predicted* to be at work by car, then we can immediately explain the _intent_ behind that car transport; 
its not just a general transport but a home-to-work commute.

### Deep learning, here we come

Having an order-1 markov chain based approach as the baseline, we turned to deep learning. We trained a Long Short-Term Memory 
(LSTM) recurrent neural network on several thousands of event timelines. The network learns to encode general human behavior 
and surprisingly is able to quickly adapt to specific user habits. The following figure illustrates the architecture of our 
deep learning pipeline: 

![]({{ site.baseurl }}/data/2018-05-21-Human-Timeline-Event-Predictions-using-LSTMs/LSTM.png)

The input is a sequence of a timeline's last 128 events, while the output is a prediction of the next event, together with a 
duration estimate of the current event. The network is implemented using TensorFlow on Python.

### Key notes

1. The network is often able to predict periodic and systematic events that a human observer could forget or would not even 
think of. 
2. We started out with simpler Bayesian models (Markov based) and gradually moved to a more advanced and complex solution.
3. Deep learning in this case actually solves the problem of dynamically adapting to the changes in the timing of events,
which the Probabilistic model couldn't.
4. The deep learning model actually runs in production now for all of Sentiance's users. Due to some of our platform
constraints, we could not use tensorflow serving module for model serving, but instead open a tf.session() python-thread for 
inference.

Check out the [full technical blog post](http://www.sentiance.com/2017/04/25/predictive-analytics-applying-deep-learning
-on-mobile-sensor-data/) that outlines the details on how we trained and tested our models, and how it actually works. Below 
is a glimpse of how our results look on a real timeline:

[![youtube_video](https://img.youtube.com/vi/ayPvFtAIOjI/0.jpg)](https://www.youtube.com/watch?v=ayPvFtAIOjI)
