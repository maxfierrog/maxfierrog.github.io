---
title: "Backpropagation Explained"
date: 2024-01-20
draft: false
order: 2
description: "How neural networks learn through backpropagation"
---

Backpropagation is the fundamental algorithm that allows neural networks to learn from data. It works by computing gradients of the loss function with respect to each weight in the network.

## The Algorithm

1. **Forward Pass**: Input data flows through the network to produce predictions
2. **Compute Loss**: Calculate the error between predictions and actual values
3. **Backward Pass**: Propagate the error backward through the network
4. **Update Weights**: Adjust weights using gradient descent

This process repeats until the network achieves satisfactory performance.
