# 🧠 Face Detection Model

Recently I had been learning **A.I / M.L** and at this point have a pretty good understanding of how they work under the hood - the **logic** and **maths** behind them.  
So, I wanted to make and train my **first model** with all the knowledge I have acquired so far, and this is my attempt at making my **1st model from scratch**.

---

## 🎯 Why Face Detector?

Honestly, I was bored and I wanted something challenging - and the classic number-predicting model was too simple.

---

## 🗂️ Purpose of this README.md

This **README.md** is a compilation of all my **findings, research, intuition, and knowledge** I acquired from this project.  
I am still new to A.I/M.L, so the content below might not be the best way (or even a good one 😅).  
Also, I might not know the right terms too - **sorry in advance 🙏.**

---

## 💡 Intuition

I knew I wanted to do something complex.  
So one random **Tuesday at 10 PM**, I thought:  
> “Why not an object classifier and a face detector using a classical neural network with dense layers?”

Now I just needed to figure out the **input** and **features** for it.

- **Input:** RGB values at each pixel.  
- **Features:** I had to figure out. Then, while trying to sleep, I thought -  
  why not **5 features:** `x`, `y`, `height`, `width`, `confidence`.

### 📏 Feature Definitions

- `x`, `y` → top-left coordinates of where the rectangle should be placed  
- `height`, `width` → size of the rectangle that encompasses the face  
- `confidence` → number between (0, 1) representing the model’s confidence that the image contains a face (for non-face images)

---

## 🔍 Findings

### 🧩 Day 1
- Learning [TensorFlow](https://www.tensorflow.org/)  
- Researching and finding datasets I could use  
- Researching the feasibility of my idea  

---

### 🧠 Day 2
- Model implementation in TensorFlow  
  - My original plan was to use **dense layers**, but during my research, I found out about **convolution layers**, which would work better for my use case.  
    So, I went ahead, learned more about them, and ended up using them in most of my hidden layers.  
- Picked and set up the [CelebA](https://mmlab.ie.cuhk.edu.hk/projects/CelebA.html) dataset to train the model with  
- Tested implementation on very small training data (like 10 images) - everything worked as intended  

> (After this point, everything goes downhill 😭)

---

### 💻 Day 3
- I wanted to train my model **locally using my own GPU (RTX 3070)**  
  - This is the **point of no return**  
  - Out of the box, TensorFlow should have GPU support… or so I thought  
  - TensorFlow cut GPU support for Windows after [TensorFlow 2.10](https://www.tensorflow.org/install/pip#windows-native)  
  - My drivers refused to work  
  - Even GPT was making me go in circles  
  - Even in my dreams, I was setting up my GPU  
  - At this point, my whole search history could be summarized in two words: **cuda** and **tensorflow** 😭  
    > But great learning experience though!
- While my GPU and TensorFlow were refusing to cooperate, I decided to **refactor some code** to make it easier to play around with model parameters.  
- Finally, I found out I could use [WSL2](https://www.tensorflow.org/install/pip#windows-wsl2) to get TensorFlow to register my GPU - no thanks to GPT.  
  > Sometimes reading the documentation helps 😅

---

### 🧪 Day 4
- **Train and test the model**
  - This was the first time I trained the model with about **10k images**, and it performed **abysmally**.  
  - My intuition: train with even more images! But loading any more into RAM wasn’t a good idea.  
    So, I added an **offset parameter** to `model_setup.py` to load an existing model and train it on new images.  
    - This didn’t fix the problem - performance was still poor.  
  - My next intuition: since I had a **confidence score** feature, but all my training data contained **faces**, the model was just learning to maximize confidence (always = 1).  
    So, I added a **parameter** to `model_setup.py` to include images **without faces** by randomly inserting **noise images** (random RGB per pixel) into the training data.  
    - This improved loss slightly, but it was still bad.
  - My next intuition: maybe it’s the **model itself or the dataset**.  
    To test this, I trained the model thousands of times on **one image** so it could perfectly fit the bounding box - but it was still off.  
    Comparing with the label showed a **dataset mismatch**:  
    > The images were *aligned and cropped* to faces, but the labels were from the *original unaligned images*.
  - I then acquired the **original images** from [CelebA](https://mmlab.ie.cuhk.edu.hk/projects/CelebA.html) and trained a new model - and finally saw **promising results** 🎉  
  - Until this point, the model had been trained on **30k images** (with and without faces).

---

### 📊 Results & Observations
- The model can predict bounding boxes **fairly well** on unseen images,  
  but it shows a **bias towards the center** of the image.  
- When the face is off-center, the prediction shifts *toward* the face but is not well aligned.  
- For images **without faces**, the model is often confident there *is* one - right in the center 😅.  

> So far, the model has learned to place bounding boxes in the middle of images, biased toward the face’s general direction.

---

### 🔁 Next Steps
I plan to **retrain the model** - this time:
- **Augment** and **crop** training images
- Help the model learn to predict bounding boxes **relative to face location**

---
