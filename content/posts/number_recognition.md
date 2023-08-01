---
author: "Abhay Vashist"
title: "Digit Recognition"
date: "2023-03-10"
description: "Help young students in drawing beautiful numbers"
tags: ["react", "typescript", "python", "pytorch"]
series: ["Personal Project"]
aliases: ["/digitRecognition"]
weight: 1
ShowToc: true
TocOpen: true
cover:
    image: https://www.quickanddirtytips.com/wp-content/uploads/2022/04/how-to-write-numbers-compressor.png
---

I build a number writing tool: [Check it out](https://avashist1998.github.io/digitRecognition/)

## What Technologies I used
- Github Pages (Hosting & Deployment) 
- React (UI)
- Python (Prototyping)
- Pytorch (Model design and training)
- Typescript (Model prediction)

## How did I build It

### Code

All of the code available on [Github](https://github.com/Avashist1998/digitRecognition).

### Design
The web application build using React frontend and LeNet model is used using Pytorch. The model is trained on the MNIST dataset.

- Project Structure
    - `model_exploration`: training and model exploration of the model
        - `notebook.ipynb`: jupyter notebook containing the model exploration
        - `LeNet5.py`: Pytorch definition of the model
    - `ui`: React application for running the frontend with standard react structure

#### Model Training
The model is build using Pytorch and the model code can be found [here](https://github.com/Avashist1998/digitRecognition/blob/master/model_exploration/src/LeNet5.py). The model is made up of 5 block, 3 of which are convolution blocks and 2 are feed forward blocks. `Tanh` is used a the activate function for all block except the final layer. The model is trained on the MNIST dataset for 10 epochs and has a test accuracy of `98.6%`.

#### React UI
The react application is build using `vite` and is a simple two page application. The first page is have basic landing page with general application and the second page is the actual application. The application is build using `typescript` and `react`. The application makes used of hash routeing since it is hosted on github pages. The application using HTML canvas for drawing of the numbers, and the prediction is made using the model trained above. The prediction is made using Oxxo's [blog](https://blog.oxxostudio.tw/posts/2017-12-26-mnist-pytorch/) on MNIST classification using Pytorch. Which allows us to use the model in the browser, without the need for a server.

### Final Thoughts
The project was a great learning experience of deploying a model in the browser. The application combined machine learning and web development, and I learned how to build a machine learning algorithm into a final solution. The project was a great learning experience and I am happy with the final result.
