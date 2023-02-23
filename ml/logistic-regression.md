---
description: it's a binary classification algorithm (one of two options) ex. is a cat?
---

# logistic regression

## prediction





$$
\hat{y} = \sigma(w^Tx+b); \textnormal{~$\sigma$ is activation function (usually sigmoid)}
$$

## loss/cost function

$$
LogLoss(w, b) = \sum_y{-y\log{\hat{y}}- (1 - y) \log{(1 - \hat{y})}}
$$

## regularization

## resources

[ml specitalization](https://www.coursera.org/learn/neural-networks-deep-learning/home/week/2)

[google developers](https://developers.google.com/machine-learning/crash-course/logistic-regression/model-training)

