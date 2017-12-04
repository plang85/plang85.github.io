---
layout: post
title:  "PyTorch's LSTM implementation explained"
date:   2017-12-02
categories: neural-networks rnn pytorch lstm
---

This is an attempt to explaining LSTM implementations a little differently, under the safe assumption that if anybody finds this they've looked at the usual suspects. With this in mind I won't cover any of the basic concepts here, there's plenty of excellent explanations out there. When I started looking into recurrent neureal networks, and LSTM implementations in particular, I didn'f find the unrolling illustration very intuitive:

<div class="fig figcenter">
  <img src="/assets/lstm-unrolled.png" width="60%">
  <div class="figcaption">RNNs are stateful, and thus aware of the order in which sequential inputs are propagated through the network. In a funcional implementation this is realized by the cell state being an additional input and return value. I find the unrolled illustration (left) more usefull.</div>
</div>

PyTorch choses a functionalish way to implement LSTMs in that it separates the data structure and the algorithm. `LSTMCell` is a type that stores two sets of weights, one for the input transformation and one for the internal gate mechanism. It then dispatches the actual transformations to a function, providing the weights, the actual input and the cell state as a parameter. An updated version of the cell's state is then returned. The approach is not purely funcional since the weight's internal stated is mutated, ie the `autograd` mechanism is aware of operations the weights participate in order to, well, compute gradients.

To understand the meaning of `hidden_size` and how it relates to [this](http://karpathy.github.io/2015/05/21/rnn-effectiveness/) figure, we need to first realize that `LSTMCell` applies an input weights dot product just as a it would for a conventiona perceptron, for which it needs an corresponding weight matrix reflecting the number of features/inputs, indicated by `input_size`.

<div class="fig figcenter">
  <img src="/assets/lstm-dot1-lin.png" width="60%">
  <div class="figcaption">Conventional perceptron: applying a weighted sum over the inputs in form of a dot product, which is commonly implemented in form of matrix multiplication of the input features and transposed weights.</div>
</div>

`LSTMCell` also performs this linear transformation on its input, but it does so with four weights for each input instead of one. 

<div class="fig figcenter">
  <img src="/assets/lstm-dot1.png" width="60%">
  <div class="figcaption">Linear transformation on inputs in LSTMCell - four weights are applied to each input. This for input_size = 3 and hidden_size = 1.</div>
</div>

If we increase `hidden_size` to `2`, the weight matris is expanded by another set of `4 * input_size` weights.

<div class="fig figcenter">
  <img src="/assets/lstm-dot1.png" width="60%">
  <div class="figcaption">Increasing the hidden_size of the LSTMCell increases the weight matrix by another set of weights, which will be evaluated as separate LSTM unit.</div>
</div>

Now this is where I find the naming of `LSTMCell` a little misleading for people like me that are not as familiar with neural network lingo. `LSTMCell` contains an arbitrary set of independent LSTM cells, each with their own set of weights applied to the same input matrix. I guess the naming arises from the idea that this type is where the logic of long short term memory cells is contained (even thought internally it dispatches to the functional implementation, but that's an implementation detail). So this is how `LSTM` and `LSTMCell` complement each other - the former implements a stacked layer of cells implemented in the latter. I guess it is important to highlight that a `LSTMCell` instance implements as many LSTM units as are asked for in `hidden_size`.