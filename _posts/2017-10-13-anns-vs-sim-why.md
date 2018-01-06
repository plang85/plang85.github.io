---
layout: post
title:  "Will artificial neural networks overtake physics based simulation for reservoir performance forecasting?"
date:   2017-10-13
categories: future neural-networks reservoir-forecast
---

*I guess for this one I should stress that opinions expressed herein are my own.*

Given the current hype around deep learning this title may sound like a surprising proposition to nobody. Looking beyond the frenzy, however, it is less clear that physics based simulation will take on a different role in the future of reservoir production foreacast. It is less clear because said forecast takes neural network approaches out of their comfort zone. This is because the rules goverining reservoir behaviour are (believed to be) well understood, and at the same time data that span the entire spectrum of responses is not available, and definitely not in large quantities. This makes the task primed to be conquered by physics based simulation, as opposed to generic neural networks that are performing well at tasks where spectrum-spannging data are abundant and at the same time the rules that relate input to output are difficult to express. Here I will outline a subjective selection of reasons why deep learning will overtake as primary source of reservoir forecasting.

### What I mean by

I refer to physics based simulation as approaches that solve a set of governing equations, usually in differential form, which have been derived through our undertanding of the problem and a set of conscious approximations, again specific to the problem we solve. Thus these systems have the problem domain harcoded, in more and less obvious form. On the explicit side the discretization is a direct implementation of the governng equations, and user inputs are in form of the physical problem and usually validated against sanity checks which again reflect our understanding of the physics that underly the problem. On the implicit side we find matrix preconditioners that are chosen based on our expectations of what the linear system of equations looks like, and parallelization strategies which also reflect our understanding of the particular system. Also, non-linear solution strategiest that address convergence difficulties have a hardcoded awareness of what numerical values to expcect. It is these optimizations that make commecial solvers of this kind outperform generic numeric approaches.

### Performance

While in conventional simulation we solve non-linear systems of equation in an iterative manner during the prediction task, neural network approaches effectively factor out the compute-expensive non-linear part to a one-off training cost, leaving the prediction/evaluation process with a single forward pass of dot products and independent function evaluations.

<div class="fig figcenter">
  <img src="/assets/Sim-ML-iterative.png" width="60%">
  <div class="figcaption">ANN-based approaches effectively factor out the non-linear part of the algorithm to a one-off process, leaving embarassingly parallel operations for the prediction/evaluation stage.</div>
</div>

These operations during evaluation is what make neural network approaches so attractive, for they cater to embarrasingly parallel implementations.

### Explicit trade-offs
