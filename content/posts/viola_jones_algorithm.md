---
author: "Abhay Vashist"
title: "Viola-Jones Algorithm"
date: "2019-11-29"
description: "Showcasing the Viola Jones Algorithm Implementation"
tags: ["machine learning", "python", "computer vision", "digital image processing"]
series: ["Grad Project"]
aliases: ["/viola_jones_algorithm"]
weight: 1
ShowToc: true
TocOpen: true
---

## Viola-Jones Algorithm

Implement the Viola-Jones Algorithm for rapid face detection in python from scratch. 
First developed a feature extraction script, which extracted 2.5 thousand features from a 19 by 19 grayscale image. 
I applied the feature extraction script to a 2000 image of non-faces and 500 images of faces. 
I implemented the AdaBoost algorithm through the python multiprocessor module, leading to a decrease in execution time by 20%. 
I ran 10 rounds of the algorithm to achieve an empirical error of 67% on the testing data set. Feature manipulated the cost function on the algorithm to priories false-positive, which led to a 5.4% false-positive error.
