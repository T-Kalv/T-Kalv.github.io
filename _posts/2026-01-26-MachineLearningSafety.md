---
title: 'Machine Learning Safety'
date: 26/01/2026
permalink: /posts/2026/01/2026-01-26-MachineLearningSafety/
tags:
  - Adversarial Attack
  - Adversarial Training
  - Research
  - Artificial Intelligence
  - Machine Learning
  - Deep Learning
  - Machine Learning Safety
---

After taking an Advanced Artifical Intelligence course, I realised that while modern AI models achieve very highly in terms of benchmark performance, they have a massive, invisible blind spot. We tend to trust these so called **"Black Box"** systems with increasingly critical taks from Self-Driving Vehicles to Autonomous Robotics and Medical Diagnosis - assuming that if they have a high accuracy, they are also "intelligent".

However, from my experience in participating in an adversarial attack competition revealed a much different truth where these models do not "see the world" that us Humans perceive the world to be (assuming that whoever is reading is that is in-face classified as a Human Being). In this post, I'll share and summarise my finds on Machine Learning Safety, in particular Adversarial Machine Learning and how I went from simple attacks to breaking state-of-the-art robust defences using techniques such as **FGSM, PGD, Momentum, Input Diversity**...

## Background
### 
To a human, an image of a Car looks like a car, regardless of filter, lighting or positioning. However, Neural Networks operate by finding complex mathematical boundaries in a high-dimensional space. By calculating gradients of a model, we can determine mathematically how to modify each pixel in an image to push that image across the boundary.

This modified image of pixels is classes as an Adversarial Example. The goal of an Adversarial Attack is to find a pertubation $\delta$ that maximises the model's loss where the difference between the original image and modified image is smaller that the **Epsilon limit**, ensuring that the change is invisible to the Human eyes.

## Core Ideas:
### FGSM (Fast Gradient Sign Method):
The core idea of FGSM is to modify the original input image by adding a tiny amount of peturbation in the direction that maximally increases the model's loss. This model takes a **"one-shot"** approach where it takes on single, large step in the direction of the gradient.

It is computed as:
$$
x_{adv} = x + \epsilon \cdot \text{sign}(\nabla_x J(\theta, x, y))
$$

where:
* $\theta$: the model parameters
* $\text{x, y}$: the input and the label
* $J(\theta, x, y)$: the loss function
* A one-step modification to all pixel values to increase the loss function with a \( l_\infty \) perturbation

While FGSM is easily blocked if the model's loss landscape is **non-linear** meaning that one wrong step might result in missing the peak of the curve entirely. FGSM asumers the loss function is entirely linear, but in reality, deep neural networks are highly non-linear. This means that a single, large step is often insufficient to fool a robust model.

### PGD (Projected Gradient Descent):
PGD is an interative version of FGSM, where instead of taking one single, large step, we take many small steps ($\alpha $). After each step, if the individual pixel values go beyond the **epislon limit** or the valid pixel range $[0, 1]$, we then **clip/project** the back. This results in the PGD Adversarial Attack's ability to navigate around **non-linear lines** in the gradient loss landscape in order to find a more accurate adversarial example.

It is computed as:
$$
x_{t+1} = \Pi_{x+S} (x_t + \alpha \cdot \text{sign}(\nabla_x J(\theta, x_t, y)))
$$

where:
* $\alpha$: the step size (learning rate)
* $\Pi$: the projection function (clipping)
* $t$: the current iteration step

Standard PGD Adversarial attacks are considered the universal benchmark for evaluation robustness. However, from my experience in participating in an adversarial attack competition, standard PGD struggled to lower the accuracy to aproximately $57 \%$. The adversarial training sample models had learned to defend against standard PGD algorithms due to their use of a **"rugged" defence** which is also known as **Gradient Masking**.

### Gradient Masking:
Robust models often defend themsef through the creation of "rugged" loss landscaped. They hide their true gradients by creating small, falze local maximas near the data points causing traps for the PGD algorithm to fall into. This resutls in the PGD attack reporting a failure but the model isn't actuall robust, instead it is just hiding/diverting the path to its failure which is called **Gradient Masking**.

### MI-DI-FGSM (Momentum plus Input Diversity Fast Gradient Sign Method):


### Multi-Targetd PGD plus CW (Carlini-Wagner) Loss:
Standard Adversarial Attack algorithmsa are often untargeted as they just try to maximise the overal gradient loss where they move away from the correct answer. With robust models they are unsure about everything resuling in making it hard for standard Adversarial Attacks to just maximise ther error. 

Multi-Target PGD plus CW attempts to push the image towards every other possible workng class. Then it picks the target class that yields the strongesr attack. Then instead of using Standard Cross-Entropy loss as it often relies on probabilities, we instead use a **Carlini-Wagner (CW) loss** where it ignored the probabilites and looks directly at the logits which are the raw scores. It then calculutes the margin between the target class and the correct class.

It is computed as:
$$
L(x, t) = \max(Z(x)_{\text{real}} - Z(x)_{\text{target}}, -\kappa)
$$

where:

* $Z(x)_{\text{real}}$: raw logit score of the correct class
* $Z(x)_{\text{target}}$: raw logit score of the specific target class we want to fake
* $\kappa$: confidence parameter (margin)

Through the combination of Multi-Targeting with CW Loss, the Adversarial attack finds the specific vunerability in the models's Adversarial Training than the standard FGSM/PGD.

## Findings & Results
During the evaluation against 9 reference models (ranging from standard to highly robust), it was observed a clear progression in attack sucess:
* **Standard PGD** = The robust models maintained a high accuracy of around $57 \%$ to $67 \%$
* **PGD plus Restarts** = Improved performance slightly, dropping the accuracy down to around $56 \%$
* **MI-DI-FGSM and Multi-Targetd PGD plus CW Loss** = Broke defences, resulting in the robust accuracy dopiing significantly to around $30 \%$ 

## My Takeaways:
After taking an Advanced Artificial Intelligence course and participating in an adversarial attack competition, it highlighted a fudamental concern in Machine Learning Safety
* **Robustness $\neq$ Correctness:** A model can be robust against simple noise but completely fail against structured/diverse attacks.
* **The Power of Randomness:** Deterministic attacks are easy to defend against. Adding stochastic elements like **Input Diversity** is essential to finding the true weaknesses in a model
* **Safety is an Arms Race:** As attacks get smarter, defenses must evolve beyond simple adversarial training. We cannot trust a "high accuracy" score blindly without rigorous testing

## Further Reading:
- [Machine Learning Safety: *Machine Learning Safetu by Xiawei Huang, Gaojie Jin, Wenjie Ruan* ](https://github.com/xiaoweih/AISafetyLectureNotes/blob/main/Machine_Learning_Safety.pdf)
- [Robust Bench: *Standardised benchmark for adversarial robustnedd*](https://robustbench.github.io/)
- [Adversarial Attacks and Defenses in Deep Learning: *Adversarial Attacks and Defenses in Deep Learning by Kui Ren, Tianhang Zheng, Zhan Qin, Xue Liu* ](https://www.researchgate.net/publication/338408111_Adversarial_Attacks_and_Defenses_in_Deep_Learning)
