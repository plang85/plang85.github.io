---
layout: post
title:  "Decline curve modeling using recurrent neural networks"
subtitle:  "An approach to modeling events"
date:   2017-07-26
categories: machine-learning neural-networks recurrent decline-curves fracking
---

:construction: code and figures complete, text missing :construction:

Decline curve modeling is arguably one of the least challenging problems to throw at neural networks these days. Since I'm all in for low hanging fruit, here goes a short write-up of a proof of concept implementation I recently demoed at a seminar. Proof of concept implementations are great since we don't have to put up with users and/or real data.

Shale wells are prime candidates for decline curve modeling. Refracking of a shale well, however, breaks the underlying assumption of decline curve modeling: first order controls which govern the production history may not change during the forecast period. Let's assume we have a set of production profiles like this, which we know for a fact to be representative:

![Refracking event]({{ site.url }}/assets/profile_example.svg)
*An artificial production history with stage information (binary array here). We'll use profiles like these to train our network.*

We see two stages, zero and one, where the stage transition indicates the point in time of refracking. 
This post is about predicting the production profile given some initial production data and the planned 
time of refrecking, which should allow for comparisons as to the economically most sensible point for refracturing, for example. We'll be moving through this rather swiftly, without much regard for Python or Keras fundamentals (yes, naturally we'll be using Keras here). Let's get to it then - we'll start with the import of some of the usual suspects:

<script src="https://gist.github.com/plang85/5b63fbd837b608ac3e50583ac2de1b63.js"></script>

where we also define a file name under which we'll save the trained model for fast predicitons, like when you're making figures for a blog post. Next we define a function to produce a production profile like the one above:

<script src="https://gist.github.com/plang85/845141802581bbad8117ade85b490883.js"></script>

Let's not dwell over the details of this function. Suffice it to say that it provides means to generate above 
production profiles, whith two stages of different exponents, and some Gaussian noise. For convenience 
we'll use this function to generate training data for us. The model we are going to use is defined by

<script src="https://gist.github.com/plang85/d0dab63233acc7775119283230c175c6.js"></script>

where `kel.TimeDistributed(kel.Dense(num_targets))` forms a dense operation on the outputs for a single time
step from all units. Let's be explicit about the layout of our little network and some convenience indices

<script src="https://gist.github.com/plang85/8ab00751dbc7a66cc3e477326392cd69.js"></script>

where the features we provide are the production history and the stage array (zeros and ones for stage zero and one, respectively) of the production profiles. The targets which we train against is the entire production history of the profile (without the first value, but more on this later). We go on and define the training data setup and array layouts

<script src="https://gist.github.com/plang85/23a8acee4ae83c87b6b831e3942b270e.js"></script>

Filling the arrays

<script src="https://gist.github.com/plang85/2fbeff720b23e6141887fbbc508baf28.js"></script>

and produces this

![Training data]({{ site.url }}/assets/training_data.svg)
*The generated training data. Not shown are the corresponding stage arrays.*

![NA encoding 1]({{ site.url }}/assets/seq010_data.svg)
*Illustration of `na` encoding in a sequence in our feature array.*

![NA encoding 2]({{ site.url }}/assets/seq030_data.svg)
*Illustration of `na` encoding in a sample in our feature array, for a later sequence of the same profile.*

It is good practice to normalize the feature arrays - this keeps the weights of the input layers around unity and aids convergence and numerical/FP stability. It is required to normalize the target arrays for a bounded activation in the output layer, like the `sigmoid` function we use. Scaling thus creates an implicit link with this activation. The scalers available in `sklearn.preprocessing` require a two-dimensional view of the underlying values, with samples as rows and each feature/target in its own column, so this view is exactly what we're gonna provide by flattening the time step dimension of our arrays:

<script src="https://gist.github.com/plang85/ad9f1bd7ba7dda5ddf6fb2874c85d35b.js"></script>

Train the model, and instrument some hack to load a trained model for future quick predictions like you need when preparing graphs for a blog post or sth

<script src="https://gist.github.com/plang85/c7d8f7969920d53f7092e07d2bd6f6ac.js"></script>

You should see something along these lines

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
where a continuous residual reduction of three orders of magnitude indicates a well behaved model, and indeed the function generated above should be something to easily regress against. We can now use our model for prediction

<script src="https://gist.github.com/plang85/8722b3860b261a64816d40188193822b.js"></script>

upon inspection of the predicted target array in the stdout instruction we see the predicted sequence

```
[-39.26998901  41.88949966  38.46153259  33.63489151  28.81029129
  25.71617889  22.57770538  19.67162323  16.9606514   14.97911167
  13.07455444  11.59985352  10.24969101   9.04060364   7.96830654
   7.02176762   6.18764973   5.45265293  16.6064167   15.20786476
  13.66429615  12.14694881  10.71009541   9.40864754   8.25721645
   7.25116396   6.3779912    5.62249804   4.96940517   4.40454197
   3.91531825   3.49083686   3.12173867   2.80009866   2.51921129
   2.27336454   2.05775905   1.86830044   1.70152593   1.55444443
   1.4245491    1.30965495   1.20788932   1.11762822   1.03748643
   0.96625328   0.90285933   0.8464036    0.79607713   0.75117487]
```

where the first entry is near out `na` value, which reflects the way we set up our training sequences. This can now be plotted to illustrate the prediction based on the eight historic data points.

![Prediction]({{ site.url }}/assets/prediction_example.svg)
*The predicion of our trained network given an initial production history.*

We can visually inspect what the above value for loss on the validation set looks like

![Prediction vs reference]({{ site.url }}/assets/prediction_reference_example.svg)
*Comparing the prediction to the reference history.*
