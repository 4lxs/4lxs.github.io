---
description: >-
  a tool used for predicting a target variable given feature variables assuming
  the relationship between target and feature variables is linear
---

# Linear Regression

## Prediction

assumption of linearity means that the expected value of the target can be expressed as a weighted sum of it's features

$$
\hat{y} = Xw + b
$$

## Reality

even if the best model for predicting y given x is linear, we would not expect any real world dataset where $y^{(i)}$ exactly equals $w^{(i)} x^{(i)}+b$ for all $1 \leq i \leq n$ due to measurement errors and the like, as there will generally be some error.

## Loss Function

for linear regression. the mean squared error loss function is generally used

$$
L(X, b) = \frac{1}{n}\sum_{i=1}^n{\frac{1}{2}(\hat{y}^{(i)} - y^{(i)})^2}
$$

## Analytic solution

linear regression has an analytic solution. we can subsume the bias into the parameter $w$ by appending a columnt to design matrix consisting of all ones. then the prediction problem becomes to minimize $|| y - Xw ||^2$. taking the derivative with respect to $w$ and setting it to zero, in the end yields solution.

$$
w^* = (X^\top X)^{-1}X^\top y
$$

note that the solution will only be unique when $X^\top X$ is invertible.

## Gradiant Descent

If we have an analytic solution, why would we need to use gradiant descent?

matrix multiplication is a very computationally intensive problem ($O(n^3)$), which means that while it gives an optimal solution, it's unfeasable to compute it on very large datasets. For such problems it's better to use other optimization solutions.

[notes on gradiant descent](gradient-descent.md)

## Resources

[d2l](https://d2l.ai/chapter_linear-regression/linear-regression.html)

[ibm](https://www.ibm.com/topics/linear-regression)

[why gradiant descent](https://towardsdatascience.com/why-gradient-descent-is-so-common-in-data-science-def3e6515c5c)