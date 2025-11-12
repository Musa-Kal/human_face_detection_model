# 🧠 Face Detection Model  
![Python](https://img.shields.io/badge/Python-3.x-blue?logo=python)  
![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-orange?logo=tensorflow)  
![Machine Learning](https://img.shields.io/badge/Category-Machine%20Learning-brightgreen)  
![Status](https://img.shields.io/badge/Status-Experimental-yellow)

---

Recently I had been learning **A.I / M.L** and at this point have a pretty good understanding of how they work under the hood and the logic and maths behind them, so I wanted to make and train my **1st model** with all the knowledge I have acquired so far and this is my attempt at making my 1st model from scratch.

---

## 🎯 Why Face Detector?

Honestly, I was bored and I wanted something challenging and the classic number predicting model was too simple.

---

## 🗂️ Purpose of this README.md

This **readme.md** is a compilation of all my finding, research, intuition, and knowledge I acquired from this project.  
I am still new to A.I/M.L so below content might not be the best way or even a good one at that, also I might not know the right terms too.  
**Sorry in advance 🙏.**

---

## 💡 Intuition

I knew I wanted to do something complex.  
So one random **Tuesday at 10pm** I thought why not an object classifier and a face detector using a classical neural network with dense layers to be specific, and now I just needed to figure out the input and features for it.  
Input was pretty simple just the RGB value at each pixel but features I had to figure out, so that day while trying to sleep I had an though why not 5 features `x`, `y`, `height`, `width`, `confidence`.

- `x`, `y` being the top left coordinate of where the rectangle should be placed.  
- `height`, `width` being the size of the rectangle that encompasses the face.  
- `confidence` number from (0, 1) representing models confidence in image containing a face for non face containing images.

---

## 🔍 Findings

<details>
<summary><b>📅 Day 1</b></summary>

- learning [tensorflow](https://www.tensorflow.org/).  
- researching and finding dataset I could use.  
- researching the feasibility of my idea.
</details>

---

<details>
<summary><b>📅 Day 2</b></summary>

- model implementation in tensorflow.  
  - my original plan was to use dense layers but during my research I found out about convolution layers, which would work better for my use case. So I went a head learned more about them and ended up using them in most my hidden layer.  
- picked and setup the [CelebA](https://mmlab.ie.cuhk.edu.hk/projects/CelebA.html) dataset dataset train the model with.  
- tested impletion on very small training data like 10 images and everything worked as attend.  

> (after this point everything goes downhill 😭)
</details>

---

<details>
<summary><b>📅 Day 3</b></summary>

- I wanted to train my model locally using my own GPU (RTX 3070).  
    - this is is the point of no return.  
    - out the box tensorflow should have GPU support, so what I thought  
    - tensorflow cut GPU support for windows after [tensorflow 2.10](https://www.tensorflow.org/install/pip#windows-native)  
    - my drivers were refusing to work  
    - even GPT was making me go in circles  
    - even in my dreams I was setting up my GPU  
    - at this point my whole search history could be summarized with 2 words <b>cuda and tensorflow</b> but great learning experience tho.  
- While my GPU and tensorflow were refusing to work together, I decided to refactor some code to make it easer to play around with model parameters.  
- I find out I can use [WSL2](https://www.tensorflow.org/install/pip#windows-wsl2) to get tensorflow to register my GPU, no thanks to GPT, sometimes reading the documentation helps.
</details>

---

<details open>
<summary><b>📅 Day 4</b></summary>

- train and test the model.  
    - this is the 1st time I trained this model with about 10k images and It performed absolutely abysmally.  
    - my intuition was to train it with even more images and it's not a good idea to load anymore images in my RAM so I decided to add an offset parameter to model_setup.py so I can load an existing model and train it on more new images. This didn't fix the problem it still performed very poorly, so my intuition was that since I have confidence score as a feature but all my training data is of images with faces, so the model is just learning to maximize the confidence score since its always 1 for my training data, so I decided to add a new parameter to model_setup.py to add and control the quantity of images without faces and I did this by randomly inserting images with random RGB values for each pixel into the training data, this did improve the loss value but its was still extremely bad.  
    - my next intuition was either it's the model itself or the dataset and to test out my prediction I trained the model a few thousand times on one images so it could perfectly learn to fit the boundary box and and it was still off so, I compared it to the image label and prediction matched the label but not image itself meaning the it was a dataset miss-match after the little bit of research I found out Images I was using were aligned and cropped to focus the the face and the labels were from the original images  
    - I acquired the original images on [CelebA](https://mmlab.ie.cuhk.edu.hk/projects/CelebA.html) and trained a new model, it finally showed promising results.  
    - Until this point, the model ahs been trained on 30k images with and without faces.  
        - Even though model is able to predict the boundary box on images it has not been trained on fairly well it still shows a bias for of center on an image, and this is even more perevelent when it is made to predict boundary box of an image that has face either corner of the image, the predicted boundary is in the direction of the face but is not lined up with it and even in cases the images without faces model is confident there is a face in the middle so far model has learned to place boundary box in middle of the image with a bias towards the location of the face.  
        - I plan to retrain the model but this time augment and crop the images in the training data so the model better predict and boundary relative to the location of the face.
</details>

---

## 📊 Results & Observations

- Even though model is able to predict the boundary box on images it has not been trained on fairly well, it still shows a bias for the center of the image.  
- When the face is off-center, the prediction shifts toward the face but is not well aligned.  
- Even in cases where images **don’t contain faces**, the model is confident there is one in the middle.

> So far model has learned to place the boundary box in the middle with a bias toward the location of the face.

---

## 🔁 Next Steps

I plan to retrain the model but this time **augment and crop** the images in the training data so the model better predicts a boundary relative to the location of the face.

---
