---
layout: post
title: Serving Deep Learning Model Using JavaScript
---

CIFAR 10 model, try it out now!

<html>
  <head>
    <link rel="stylesheet" href="{{ site.baseurl }}/data/2021-12-05-Serving-Deep-Learning-Model-Using-JavaScript/cifar_model_page_style.css" />
  </head>
  <body>
    <div class="card elevation">
      <canvas
        class="canvas elevation"
        id="canvas"
        width="280"
        height="280"
      ></canvas>
      <div class="button" id="clear-button">CLEAR</div>
      <div class="predictions">
        <div class="prediction-col" id="prediction-0">
          <div class="prediction-bar-container">
            <div class="prediction-bar"></div>
          </div>
          <div class="prediction-number">plane</div>
        </div>
        <div class="prediction-col" id="prediction-1">
          <div class="prediction-bar-container">
            <div class="prediction-bar"></div>
          </div>
          <div class="prediction-number">car</div>
        </div>
        <div class="prediction-col" id="prediction-2">
          <div class="prediction-bar-container">
            <div class="prediction-bar"></div>
          </div>
          <div class="prediction-number">bird</div>
        </div>
        <div class="prediction-col" id="prediction-3">
          <div class="prediction-bar-container">
            <div class="prediction-bar"></div>
          </div>
          <div class="prediction-number">cat</div>
        </div>
        <div class="prediction-col" id="prediction-4">
          <div class="prediction-bar-container">
            <div class="prediction-bar"></div>
          </div>
          <div class="prediction-number">deer</div>
        </div>
        <div class="prediction-col" id="prediction-5">
          <div class="prediction-bar-container">
            <div class="prediction-bar"></div>
          </div>
          <div class="prediction-number">dog</div>
        </div>
        <div class="prediction-col" id="prediction-6">
          <div class="prediction-bar-container">
            <div class="prediction-bar"></div>
          </div>
          <div class="prediction-number">frog</div>
        </div>
        <div class="prediction-col" id="prediction-7">
          <div class="prediction-bar-container">
            <div class="prediction-bar"></div>
          </div>
          <div class="prediction-number">horse</div>
        </div>
        <div class="prediction-col" id="prediction-8">
          <div class="prediction-bar-container">
            <div class="prediction-bar"></div>
          </div>
          <div class="prediction-number">ship</div>
        </div>
        <div class="prediction-col" id="prediction-9">
          <div class="prediction-bar-container">
            <div class="prediction-bar"></div>
          </div>
          <div class="prediction-number">truck</div>
        </div>
      </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/onnxjs/dist/onnx.min.js"></script>
    <script src="{{ site.baseurl }}/data/2021-12-05-Serving-Deep-Learning-Model-Using-JavaScript/script.js"></script>
  </body>
</html>
