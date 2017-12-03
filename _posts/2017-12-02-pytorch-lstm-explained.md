---
layout: post
title:  "PyTorch's LSTM implementation explained"
date:   2017-12-02
categories: neural-networks rnn pytorch lstm
---

This is an attempt to explaining LSTM implementations a little differently, under the safe assumption that if anybody finds this they've looked at the usual suspects. When I started looking into recurrent neureal networks, and LSTM cells in particular, I didn'f find the unrolling illustration very intuitive:

<div class="fig figcenter">
  <img src="/assets/lstm-unrolled.png" width="60%">
  <div class="figcaption">RNNs are stateful, and thus aware of the order in which sequential inputs are propagated through the network. In a funcional implementation this is realized by the cell state being an additional input and return value. I find the unrolled illustration (left) more usefull.</div>
</div>

PyTorch choses a functionalish way to implement LSTMs in that it separates the data structure and the algorithm. The LSTM cell is a type that stores two sets of weights, one for the input transformation and one for the internal gate mechanism. It then dispatches the actual transformations to a function, providing the weights, the actual input and the cell state as a parameter. An updated version of the cell's state is then returned. The approach is not purely funcional since the weight's internal stated is mutated, ie the autograd mechanism is aware of operations the weights participate in order to, well, compute gradients.

To understand the meaning of `hidden_size` and how it relates to [this](http://karpathy.github.io/2015/05/21/rnn-effectiveness/) figure, we need to first realize that `LSTMCell` applies an input-weights dot product just as a it would for a conventiona perceptron, for which it needs an input weight matrix reflecting the number of features/inputs, indicated by `input_size`.

<div class="fig figcenter">
  <img src="/assets/lstm-dot1-lin.png" width="60%">
  <div class="figcaption">Conventional perceptron: applying a weighted sum over the inputs in form of a dot product, which is commonly implmented as matrix multiplication of the input features and transposed weights.</div>
</div>

`LSTMCell` also performs this linear transformation on its input, but it does so with four weights for each input instead of one. 

<div class="fig figcenter">
  <img src="/assets/lstm-dot1.png" width="60%">
  <div class="figcaption">Linear transformation on inputs in `LSTMCell` - four weights are applied to each input. This for `input_size = 3` and `hidden_size = 1`.</div>
</div>
