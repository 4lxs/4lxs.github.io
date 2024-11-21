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

even if the best model for predicting y given x is linear, we would not expect any real world dataset where $$y^{(i)}$$ exactly equals $$w^{(i)} x^{(i)}+b$$ for all $$1 \leq i \leq n$$ due to measurement errors and the like, as there will generally be some error.

## Loss Function

for linear regression. the mean squared error loss function is generally used

$$
L(X, b) = \frac{1}{n}\sum_{i=1}^n{\frac{1}{2}(\hat{y}^{(i)} - y^{(i)})^2}
$$

## Analytic solution

linear regression has an analytic solution. we can subsume the bias into the parameter $w$ by appending a column to design matrix consisting of all ones. then the prediction problem becomes to minimize $$|| y - Xw ||^2$$. taking the derivative with respect to $w$ and setting it to zero, in the end yields solution.

$$
w^* = (X^\top X)^{-1}X^\top y
$$

note that the solution will only be unique when $$X^\top X$$ is invertible.

## Gradient Descent

If we have an analytic solution, why would we need to use gradient descent?

matrix multiplication is a very computationally intensive problem ($O(n^3)$), which means that while it gives an optimal solution, it's unfeasible to compute it on very large datasets. For such problems it's better to use other optimization solutions.

[notes on gradient descent](gradient-descent.md)

## Example

```python
import torch
import matplotlib.pyplot as plt

f = lambda x: -5 * x + 25
xs = torch.arange(-5, 5, .25).view(-1, 1)
ys = f(xs)
# add randomness to data
ys += torch.randn(ys.shape)

# init
weights = torch.randn((1,), requires_grad=True)
bias = torch.tensor(0., requires_grad=True)
data = []
# train
for i in range(100):
  # feed forward
  yhat = xs * weights + bias
  loss = ((yhat - ys) ** 2 / 2).mean()
  print(f'w={weights.item():e}, l={loss.item():e}, b={bias.item():e}')
  # backprop
  loss.backward()
  # optimize
  weights.data -= weights.grad.data * .01
  bias.data -= bias.grad.data * .1

  weights.grad.data.zero_()
  bias.grad.data.zero_()
```

```
w=1.132170e+00, l=4.839108e+02, b=0.000000e+00
w=5.935752e-01, l=3.936823e+02, b=2.567060e+00
w=1.031284e-01, l=3.203598e+02, b=4.870683e+00
w=-3.435172e-01, l=2.607646e+02, b=6.937812e+00
w=-7.503120e-01, l=2.123172e+02, b=8.792645e+00
...
w=-4.948588e+00, l=3.953586e-01, b=2.491010e+01
w=-4.948682e+00, l=3.953578e-01, b=2.491014e+01
w=-4.948768e+00, l=3.953570e-01, b=2.491017e+01
w=-4.948846e+00, l=3.953564e-01, b=2.491020e+01
```

## Resources

[d2l](https://d2l.ai/chapter\_linear-regression/linear-regression.html)

[ibm](https://www.ibm.com/topics/linear-regression)

[why gradient descent](https://towardsdatascience.com/why-gradient-descent-is-so-common-in-data-science-def3e6515c5c)
