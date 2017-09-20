---
layout: post
title:  "We need to ditch deterministic computation in reservoir simulation"
subtitle:  "What numerical simulation frameworks can learn from neural network applications, and why we can't really be agile before then"
date:   2017-09-18
categories: opinion simulation
---

:construction: not nearly finished with this one :construction:

I guess for this one I have to stress that opinions expressed here are my own, and that they do not necessarily allign with those of my employer. (People seem to do that - it must not be clear.)

Much has been written about the futile effort to verify numerical models of subsurface processes (...), and the need to account for uncertainties using things like multi-realization frameworks (...). In short, it is thus very much accepted that simulation results for a single scenario/model/realization are not sufficient, a notion which is only added to when history-matched models are used, since it is known that for non linear problems adjoint solutions are not unique (...). 

Also, any experienced reservoir engineer-geologist-geophycisist would never use a numerical simulation result of any sort be a single source of truth for anything.

This sort of non-determinism, however, is not what I'm referring to. What I getting at is that if you run a single simulation with deterministic input, a deterministic output is expected. In other words, if a user runs the same simulation twice, she expects identical results.


Why is this important? To a user, it would mean faster time to delivery of feature requests and cheaper software. This goes back to why it is important to developers:

- Less time spent on branching for backward compatible behavior, reduced maintenance for these branches
- Reduced regression testing effort (ie testing against changes in behavior)
- Less bugs (ie introduced change in behaviour regressions didn't pick up)


Let's look at some of the lengts that numerical applications

<script src="https://gist.github.com/plang85/6ebe733325263fe7e80823d342d414dd.js"></script>


Even though for this trivial example it is obvious (hindsight 20:20) which splitting strategy yields the more accurate operation, achieving thread invariance by adapting threading strategies is non-trivial for generic algorithms. That being said, it's done routinely in advanced parallel compute applicatins and libraries.

We need to make trade-offs explicit! Accuracy, 

Since we solve iteratively, errors tend to propagate. Given above FP traits (ulp), and the ill conditioned nature of the systems we solve (starting values), convergence failures can show up only at later stages and be masked initally.

Does that mean that all bets are off, we have and excuse to let our inner freebirds out, not care about correctness and revert to 16 bit precision? Of course it doesn't. We still need to verify against analytic solutions and we're still solving a discrete mathematical implementation of a concrete physical model. Also, in terms of precision, modern reservoir models push the boundaries of 64 bit precision already. It is the loss of precision issue that is very relevant there, with models that span well into the 100 km range in terms of absolute coordinate values, and at the same time using higher resolution, i.e. smaller cells. When we then compute geometric dependent properties the combination of the two poses challenges. There are approaches to address these problems, and we have to be vigilant for iteratively propagating errors when we design our tests during development.

However, as models get more complex and feature combinations reach higher levels, implementation choices are far from straight forward and without right or wrong. We need to allow engineers to make these in an agile spirit - get something working, expose it to the customer, get feedback and improve. Reliying on simulator to reproduca past results on the dot puts the (unrealistic) burden on the implementer to get things right the first time. Even worse, it indirectly keeps people from looking into improving algorithms, since that would most certainly induce a change in behavior. This also impacts time to market since it puts pressure on engineers to get it right the first time - laboring under this illusion.

But we definitely can learn to focus on higher level traits rather than discrete results. We know when our non linear solutions converge.