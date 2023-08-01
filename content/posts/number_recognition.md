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

I created a number writing tool that you can check out [here](https://avashist1998.github.io/digitRecognition/).

## Technologies Used
- Github Pages (Hosting & Deployment)
- React (UI)
- Python (Prototyping)
- Pytorch (Model design and training)
- Typescript (Model prediction)
- Vite (Building Process)

## Codebase
The entire codebase is available on [Github](https://github.com/Avashist1998/digitRecognition).

### Design
For this web application, I utilized a React frontend and implemented a LeNet model using Pytorch. The model was trained on the MNIST dataset.



#### Project Structure
- `model_exploration`: This section covers the training and model exploration processes.
    - `notebook.ipynb`: A Jupyter notebook containing the model exploration.
    - `LeNet5.py`: The Pytorch definition of the model.
- `ui`: This contains the React application that constitutes the frontend with a standard React structure.


#### Model Training
The Pytorch-based LeNet model code can be found [here](https://github.com/Avashist1998/digitRecognition/blob/master/model_exploration/src/LeNet5.py). The model consists of 5 blocks, with 3 being convolution blocks and 2 being feedforward blocks. The activation function used for all blocks except the final layer is `Tanh`. Training was conducted on the MNIST dataset for 10 epochs, resulting in an impressive test accuracy of `98.6%`.

#### React UI
The React application is built using `vite` and comprises two simple pages. The landing page provides an overview of the application, while the second page hosts the actual tool. The application, built with `typescript` and `react`, uses hash routing for compatibility with Github Pages. For drawing the numbers, the application utilizes HTML canvas, and the predictions are made using the trained model. This prediction process is based on Oxxo's [blog](https://blog.oxxostudio.tw/posts/2017-12-26-mnist-pytorch/) on MNIST classification using Pytorch. The implementation enables us to perform predictions directly in the browser, eliminating the need for a server.

### Final Reflections
This project was an enriching learning experience in deploying a machine learning model in the browser. Combining machine learning and web development, I successfully integrated a machine learning algorithm into a complete solution. The process provided valuable insights into building and deploying models for real-world applications. I am thrilled with the final outcome and the skills I acquired throughout the project.
