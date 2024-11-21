---
description: TODO
---

# Neural Networks

<figure><img src="https://www.tibco.com/sites/tibco/files/media_entity/2021-05/neutral-network-diagram.svg" alt=""><figcaption><p>a simple neural network</p></figcaption></figure>

## Structure

A neural network is comprised of layers, split into input layer, output layer and (usually multiple) hidden layers. Each layer is made up of neurons.&#x20;

### Neuron

<figure><img src="https://k21academy.com/wp-content/uploads/2020/12/Perceptron-model.jpeg" alt=""><figcaption><p>neuron structure</p></figcaption></figure>

each neuron has as input the outputs of all neurons of the previous layers, weights and a bias. it calculates the output in a two step process. first it calculates the weighted sum of it's weights and inputs, and adds the bias. it passes the result into an activation function.

## Forward pass

A NN's output is calculated by going through layers ($$l_1^n$$ ) and computing the activations of that layer:$$a^{[l]} = g^{[l]}(w^{[l]} a^{[l-1]} + b^{[l]})$$ with $$a^{[0]}$$ being the input layer $$X$$. the output of the NN is then the output of the last layer $$\hat{y} = a^{[n]}$$

## Resources

[ibm](https://www.ibm.com/topics/neural-networks)

[ml specialization](https://www.coursera.org/learn/neural-networks-deep-learning/home/week/3)
