---
layout: post
title:  "Decline curves using recurrent neural networks"
subtitle:  "An approach to modelling events"
date:   2017-07-26
categories: machine-learning neural-networks recurrent decline-curves fracking
---

Decline curve modelling is probably one of the least challenging problems to throw at neural networks these days. Since I'm a sucker for low hanging fruit, here goes a short write-up of a proof of concept implementation I recently demoed at a seminar. Proof of concept implementations are great since we don't have to put up with users and/or real data.

Shale wells are prime candidates for decline curve modelling. Refracking of a shale well, however, breaks the underlying assumption of decline curves, which is that the first order controls that governed the production history may not change during the forecast period. Let's assume we have production profiles like this, which we consider representative

![Refracking event]({{ site.url }}/assets/profile_example.svg)

with two stages, zero and one, where the stage transition indicates a refracking of the well, which leads to a production increase. This post is about predicting the production profile given some initial production data and the planned stage, i.e. when refracking will take place in the lifetime of the well. Let's get to it. After importing some common modules (oh, and we'll be using Keras here of course)

<script src="https://gist.github.com/plang85/5b63fbd837b608ac3e50583ac2de1b63.js"></script>

we can produce these artificial production profiles with a function such as

<script src="https://gist.github.com/plang85/845141802581bbad8117ade85b490883.js"></script>

Let's not dwell over the details of this function. Suffice it to say that it provides means to generate above production profiles, whith two stages, each with its own exponent that controls the decline. For convenience we'll use this function to generate training data for us. The model we are going to use is defined as

<script src="https://gist.github.com/plang85/d0dab63233acc7775119283230c175c6.js"></script>

Our little network layout and some convenience indices

<script src="https://gist.github.com/plang85/8ab00751dbc7a66cc3e477326392cd69.js"></script>

Our training data setup and matrices

<script src="https://gist.github.com/plang85/23a8acee4ae83c87b6b831e3942b270e.js"></script>

Filling the arrays

<script src="https://gist.github.com/plang85/2fbeff720b23e6141887fbbc508baf28.js"></script>

Scaling, creates an implicit link with the activation function chosed for the dense operation on all LSTM units per time step

<script src="https://gist.github.com/plang85/ad9f1bd7ba7dda5ddf6fb2874c85d35b.js"></script>

Train the model, and instrument some hack to load a trained model for future quick predictions like you need when preparing graphs for a blog post or sth

<script src="https://gist.github.com/plang85/c7d8f7969920d53f7092e07d2bd6f6ac.js"></script>

You should see some

```
1960/1960 [==============================] - 3s - loss: 0.0133 - val_loss: 0.0101
Epoch 2/10
1960/1960 [==============================] - 2s - loss: 0.0094 - val_loss: 0.0087
Epoch 3/10
1960/1960 [==============================] - 2s - loss: 0.0084 - val_loss: 0.0077
Epoch 4/10
1960/1960 [==============================] - 2s - loss: 0.0074 - val_loss: 0.0065
Epoch 5/10
1960/1960 [==============================] - 2s - loss: 0.0058 - val_loss: 0.0045
Epoch 6/10
1960/1960 [==============================] - 2s - loss: 0.0035 - val_loss: 0.0023
Epoch 7/10
1960/1960 [==============================] - 2s - loss: 0.0017 - val_loss: 0.0012
Epoch 8/10
1960/1960 [==============================] - 2s - loss: 9.4538e-04 - val_loss: 7.1189e-04
Epoch 9/10
1960/1960 [==============================] - 2s - loss: 6.4418e-04 - val_loss: 5.3728e-04
Epoch 10/10
1960/1960 [==============================] - 2s - loss: 5.0730e-04 - val_loss: 4.3987e-04
```
