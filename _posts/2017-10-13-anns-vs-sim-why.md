---
layout: post
title:  "Why artificial neural networks will replace physics based simulation for reservoir performance forecasting"
subtitle:  "Precision is about formats"
date:   2017-09-01
categories: rants accuracy precision illustrations
---

*I guess for this one I should stress that opinions expressed herein are my own.*

Given the current hype around deep learning this title may sound like a surprising proposition to nobody. Looking beyond the frenzy, however, it is less clear that physics based simulation will take on a different role in the future of reservoir production foreacast. It is less clear because said forecast takes neural network approaches out of their comfort zone. This is because the rules goverining reservoir behaviour are (believed to be) well understood, and at the same time data that span the entire spectrum of is not available, and definitely not in large quantities. This makes the task primed to be conquered by physics based simulation, as opposed to generic neural networks that are performing well at tasks where spectrum-spannging data are abundant and at the same time the rules that relate input to output are difficult to express. Here I will outline a subjective selection of reasons why deep learning will overtake as primary source of reservoir forecasting.

## Performance

While in conventional simulation we solve non-linear systems of equation in an iterative manner during the prediction task, neural network approaches effectively factor out the compute-expensive non-linear part to a one-off training cost, leaving the prediction/evaluation process with a single forward pass of dot products and independent function evaluations.

<div class="fig figcenter">
  <img src="/assets/ann-sim-iteration-singlepass.png" width="60%">
  <div class="figcaption">Nope.</div>
</div>


