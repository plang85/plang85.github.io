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
  <img src="/assets/lstm-dot2.png" width="60%">
  <div class="figcaption">Increasing the hidden_size of the LSTMCell increases the weight matrix by another set of weights, which will be evaluated as separate LSTM unit.</div>
</div>

Now this is where I find the naming of `LSTMCell` a little misleading for people like me that are not as familiar with neural network lingo. `LSTMCell` contains an arbitrary set of independent LSTM cells, each with their own set of weights applied to the same input matrix. I guess the naming arises from the idea that this type is where the logic of long short term memory cells is contained (even thought internally it dispatches to the functional implementation, but that's an implementation detail). So this is how `LSTM` and `LSTMCell` complement each other - the former implements a stacked layer of cells implemented in the latter. I guess it is important to highlight that a `LSTMCell` instance implements as many LSTM units as are asked for in `hidden_size`.

<div class="fig figcenter">
  <img src="/assets/LSTMCell-inside-input.png" width="60%">
  <div class="figcaption">The hidden_size of an LSTMCell stands for the number of LSTM units and thus the size of the output matrix.</div>
</div>

The weights for the hidden state as well as the hidden state are passed as inputs to the LSTMCell function from the LSTMCek type. Each LSTM unit implements the gating mechanism, which uses a cell state that is also provided as input, and reflects the number of units.

<div class="fig figcenter">
  <img src="/assets/LSTMCell-inside-output.png" width="60%">
  <div class="figcaption">The output is a effectively a concatenation of all LSTM units, but implemented as matrix multiplication and independent function evals on elements.</div>
</div>

For a batch size larger than one, all matrices (input, hidden state/output, cell state) increase along the first axis (ie more rows), but the weight matrices remain the same size - they are being multiplied into all samples, which then results in a single gradient per weight with respect to the loss over all samples in the batch.

<div class="fig figcenter">
  <img src="/assets/LSTMCell-inside-output-fit.png" width="60%">
  <div class="figcaption">The output includes the updated cell state along with the target hidden state, to be passed to the algorithm along with the next input in the sequence.</div>
</div>

The output denoted `Y` here is used as hidden state `h` for the next set of input `X` in the sequence, along with the updated cell state `c`. This takes you through one pass over a LSTMCell, pretty much verbatim collected from different places in PyTorch, assuming no bias for simplicity:

```
batch_size = 5
sequence_length = 10
input_size = 2
hidden_size = 4

features = Variable(torch.randn(batch_size, input_size, sequence_length))

h = Variable(torch.zeros(input_size, 4 * hidden_size))
c = Variable(torch.zeros(input_size, 4 * hidden_size))

weight_ih = Parameter(torch.randn(4 * hidden_size, input_size))
weight_hh = Parameter(torch.randn(4 * hidden_size, hidden_size))

for iseq in range(sequence_length):
    input = features[:, :, iseq]

    gates = input.matmul(weight_ih.t())  + hx.matmul(weight_hh.t())
    ingate, forgetgate, cellgate, outgate = gates.chunk(4, 1)

    ingate = F.sigmoid(ingate)
    forgetgate = F.sigmoid(forgetgate)
    cellgate = F.tanh(cellgate)
    outgate = F.sigmoid(outgate)

    # cell state for next input in sequence
    c = (forgetgate * c) + (ingate * cellgate)
    # output to next layer and/or hidden state for this layer
    # for next input in sequence
    y = h = outgate * F.tanh(c)
```