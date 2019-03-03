---
layout: post
title: Text To Image Generation Using Generative Adversarial Networks
---

Generative Adversarial Networks (GANs) are a popular variant of the generative class of 
neural network models. The fundamental principle behind a GAN model is a discriminator and a 
generator model working against each other (roughly speaking) thereby achieving the 
ultimate goal of fine-tuning the generator to facilitate generating meaningful,
non-random data. 

## Introduction

In this blog, I use [this paper](https://arxiv.org/pdf/1605.05396.pdf) as a reference to implement a GAN model
that takes in plain English text as input and outputs an image that would ideally look
similar to an image captioned as the input text. To train such a model, therefore, we
use a pre-captioned set of images where the caption text is the input to the generator 
part of the GAN model and the image is used for comparison with the generated image,
 by the discriminator.

## A word on dataset

We use [Oxford-102 flower images dataset](http://www.robots.ox.ac.uk/~vgg/data/flowers/102/)
for this task. This dataset consists of 8189 images of flowers from 102 different flower
categories. 

As for the textual part of the data, according to our reference paper:


<code>
For text features, we first pre-train a deep convolutionalrecurrent text encoder on 
structured joint embedding of
text captions with 1,024-dimensional GoogLeNet image
embedings
</code>
We used 5 captions per image while training.

## Network architecture: 

Image shamelessly copied from the reference paper:

![Network Architecture]({{ site.baseurl }}/data/2019-02-28-Text-To-Image-Using-GANs/arch.jpg)

I encourage readers to go through section 4 of the paper for details.

## Code

I started off with a basic implementation available [here](https://github.com/zsdonghao/text-to-image). 
After correcting the model architecture code, adding a model_server object under the inference code,
the model was trained for 600 epochs.  Full code for this project can be found here (https://bitbucket.org/voice2image/text_to_image_flowers/src/master/).


## Sample Results

At the end every epoch, we generate samples to visually test the model performance. Here
is how those generated samples transition in quality in a window of epoch-100 to epoch-130,
for example:

[![youtube_video](https://img.youtube.com/vi/aHDQc7wm_UI/0.jpg)](https://youtu.be/aHDQc7wm_UI)


## Demo

<html>
<body>
<form>
    <textarea id="imagename" name="imagename"
          rows="2" cols="33">
     pink flower with yellow petals
    </textarea>
    <input type="button" id="btn" value="GO" />
</form>
 <img id='image'>
    <script type="text/javascript">
        document.getElementById('btn').onclick = function() {
            var val = document.getElementById('imagename').value,
                src = 'http://13.59.20.142:8888/text/' + val,
                document.getElementById('image').src=src;
        }
    </script>
    
</body>
</html>


## Acknowledgements

Sincere thanks to the [authors of the reference paper](https://arxiv.org/pdf/1605.05396.pdf), 
[author of the reference repo](), and
[Faisal Mehfooz](https://www.linkedin.com/in/faisalmehfooz/) for helping with demo web api
for this project
