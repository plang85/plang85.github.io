---
layout: post
title:  "Decline curves using recurrent neural networks"
subtitle:  "An approach to modelling events"
date:   2017-07-26
categories: neural networks, recurrent, machine learning, decline curve, shale well, fracking
---

Decline curve modelling is probably one of the least challenging problems to throw at neural networks these days. Since I'm a sucker for low hanging fruit, here goes a short write-up of a proof of concept implementation I recently demoed at a seminar. Proof of concept implementations are great since we don't have to put up with users and/or real data.

Shale wells are prime candidates for decline curve modelling. Refracking of a shale well breaks the underlying assumptions of decline curves, which is that the first order controls during the initial production phase, i.e. the production history, may not change over the forecast period. Let's assume we have production profiles like this which we consider representative

![Refracking event]({{ site.url }}/assets/profile_example.svg)

this post is about predicting the production profile given some initial production data and the planned stage, i.e. when refracking will take place in the lifetime of the well. Let's get to it. After importing some common modules (oh yes we'll be using the awesome Keras framework here of course)

<script src="https://gist.github.com/plang85/5b63fbd837b608ac3e50583ac2de1b63.js"></script>

we can produce these artificial production profiles with a function such as

<script src="https://gist.github.com/plang85/845141802581bbad8117ade85b490883.js"></script>