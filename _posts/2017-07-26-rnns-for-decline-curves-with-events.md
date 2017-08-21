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