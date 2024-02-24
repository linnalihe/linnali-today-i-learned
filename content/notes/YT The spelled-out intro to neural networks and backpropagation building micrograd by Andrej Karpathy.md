---
date: 2024-02-12 16:37
tags:
  - lesson-note
  - ai
internal-link: []
---

https://karpathy.ai/zero-to-hero.html

https://www.youtube.com/watch?v=VMj-3S1tku0


micrograd is a library that Andrej released on GitHub. This lesson will be an overview
The engine has very few lines of code. 2 files include the engine and neural nets
`nn.py` define what is a neuron, a layer of neurons, and a multi-layer perceptron which is a sequence of layers

Autograd (AKA Automatic Gradient) engine which implements back-propagation this use case in AI

Back-propagation is an algorithm that efficiently evaluates the gradient of some loss function with respect to the weights of a neural network. By using this algorithm we can tune the weights of the neural network to minimize the loss function to improve accuracy

Understand what a derivative is
Derivative is the slope at a given point of a function f(x)

The video goes over an example of back-propagation by going through a manual calculation.

The focus of to find the rate of change or the derivative for each node and its relationship to dL where L is the final node.
Chain rule to find the relationship of dL/dx (AKA derivative of L with respect to x) where x is a node that does not directly result in L. Find the product of `(dz/dx)*(dL/dz)` to find `dL/dx`

"back-propagation recursive application of chain rule through the computation graph"

The operations at each step can be arbitrarily complex. What we care about is the local derivative at that point. For example, we can use the operation `tanh` because we can calculate its derivative. But we can also use addition, subtraction, multiplication, and division together to achieve `tanh`.

left off at 2:15:00