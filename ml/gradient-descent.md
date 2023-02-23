---
description: >-
  it's an optimization algorithm. meaning, an algorithm to update parameters to
  minimize cost. note that it only works with convex functions, otherwise it
  would only find a local minimum
---

# Gradient Descent

<figure><img src="https://static.javatpoint.com/tutorial/machine-learning/images/gradient-descent-in-machine-learning1.png" alt=""><figcaption></figcaption></figure>

## algorithm

$$
w := lr \frac{\partial Loss(w, b)}{\partial w}
$$

$$
\\ b := lr \frac{\partial Loss(w, b)}{\partial b}
$$

## resources

[ml specialization](https://www.coursera.org/learn/neural-networks-deep-learning/home/week/2)

[ibm](https://www.ibm.com/topics/gradient-descent)
