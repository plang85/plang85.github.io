---
layout: post
title:  "The future of reservoir simulator development"
subtitle:  "What physics-based frameworks can learn from neural network applications, and why we can't really conquer agility before then"
date:   2017-09-18
categories: opinion simulation
---

:construction: not nearly finished with this one :construction:

I guess for this one I have to stress that I speak for myself and not for my employer. 

Physics-based reservoir simulation has its merit, and it still has a place in tomorrow's data dominated world. This place is just not in front of a user.

For the better or worse, we’re moving fast towards complete automation, and predictive analysis of oil reservoirs is no exception. Form an end-user perspective, automatization initially manifests itself by a shift towads more abstract expections. If you drive a car yourself, expectations are closer to the bare metal, like respnsiveness of steering and breaks, acceleration and such. If you’re being driven, no matter if by a person or a pn automated system, you care about an enjoyable, safe and timely joirney. You also don't care that music suggestions in Spotify are repeatable everytime you ask for them, you care about that theyre good. Both systems are complex under the hood, and in the form of neural network based implementations, Very similar to numerical solutions to non linear pdes. The user is just not exposed to this complexity. She is another level onf indirection away, a level that transforms complex output to something more abstract, qualitative.

For reservoir simulators, however, the story is very different. the end-user is exposed to the full wrath that is input, execution and output of low-level solutions to coupled, non linear, often ill conditioned systems. These are solved in arbitrary parallel manner, iteratively, distibuted, n different platforms, different clusters and different moon cycles. anybody who knows anything about ieee floats gets the idea of what that means. Given all that, it is actually surprising that we deliver repeatable, backward compatible results, evEn though we add new leVels of parallelization, refactor, implement new features and so on. Not surprising for trivial cases, and the reproduction of analytic solutions to refence problems. But for structurally complex models, of 100km extent with m scale features, a combinatorial explosion of settings, flags and bells and whistles to model instruments such as well devices whose real world behavior is even difficult to describe after reading the one hundred page manual. There is, however, a price to be paid for this. This price is the main body of this text, and that it’s nit worth paying it.

User expectations that results between releases do. ot change also puts unrealistic pressure on the implementation to get thinGs perfect on the first try. which. never. happens. anywhere. this pressure results in long development times, no user feedback (since the first result the user sees are the ones they’ll want forever), and might result in no value features released after long time that nobody cares about anymore since the industry and it landscape has chnaged under our feet while we were up to building the perfct algorithm or parallelization stratehpgy.

The price paid by developing simulators this way is having budget that could go into innovation, performance improvements, addressing of technical dept, instead goes to maintaining backward compatible branhces and combinations thereof, accoumodating erroneous input in a gracious and meaningful manner. We have so many options, parameters that are adjustable, each with de facto infinite number of values, and the resulting combinatorial nightmare. Often, combinations of these settings dont play to the simulators sttrengthss, but finding these through testing in an exhaustive. manner is a futile effort.

Endless possibilities. no documentation nightmare of 100 options, no support for a million backward comp branches and themaintenance nightmare. all the effort could be spend on innovation, performance, user experience. developers would actially able to experiment, talk to customers, i prove ncrementally instead of fixing incrementally with stones while sitting in a house o glass. thehypotheses that algorith,sl featires and the latest greates parallelization or discretizatIon method actually deliVers customer value could be verified before commiting huge resources to it. engineers are relieved of the pressure to get things perfect, and in a fake it until you make it fashion they oVer time themselvS believE their implementations to be the one to rule them all, because if they didnt the consequences would seem too severe.


I opened by asserting that physics-based simulation has a role to play for tomorrows oilfield production, and I’m convinced it has. Advantages over generic neural-network implementations are lead by the ability to reason about results. It is often the resulting insight into captured emergence and unique nature of a reservoir that provides most value. There is essentially no such thing as Adversarial inputs either, and if then simulatros are likely to be very loud about this. Anybody whos dealt with convergence issues knows what I mean. Too much has been written already about the futile task of deterministic simulation in subsurface systems, and means to addres the issue. Naomi Oreseks et al openened a landmark article on it with one of the best abstracts I’ve ever read

Verification and validation of numerical models of natural systems is impossible. This is because natural systems are never closed and because model results are always non- unique. Models can be confirmed by the demonstration of agreement between observation and prediction, but confirmation is inherently partial. Complete confirmation is logically precluded by the fallacy of affirming the consequent and by incomplete access to natural phenomena. Models can only be evaluated in relative terms, and their predictive value is always open to question. The primary value of models is heuristic

This is not an excuse for sloppy implementations, or reduced testng effort. Reproducing analytic solutions and reference problems is a prerequisite for any numerical solver - it is also nit an issue, these basic building blocks these tests address have been successfully implemented 50 years ago and at the heart of all implementations. Also, as mentioned above, the fundamental point she makes is addresses by other means. With the wholistic view/assesment on the value of a product that encompasss every step of development these days, reskes summary it is howeVer a valuable paerspective to keep in mind when considering some of the lengths we go through to avoid a chnage in behavior. and all the things that come with it during product development.

I doupt that many people in charge of reserve booking copy paste numbers from simulator outputs. with oreskes reminder of the primarily heuristic value of simulations, we can develop simulators much closer to the customer, with faster feedback loops, and perhaps focus on what they're  really goot at - insight, and rejection of hypotheses. but we cant do this while expecting everything else, like results, to stay the same. real change is no cherry picking business. so the place for simulators is as a part of the dcision making suite. to provide insight, add to numeirc estimations and reject hypotheses. we should embrace some change in behavior. it means were allowed to improve. changing results should not result in loosing faith in the implementation, it should be seen as a sign of improvement. simulators that do not change should be whats actually frightening. 

The firts, immediate level of indirection we need is one in which the engineer describes the reservoir in a declarative manner, much closer to the perception of a geologist. I see reservoir engineers move from understanding the low-level mechanics of simulation and how to manipulate those to make the simulator model the natiral behavior of the system toward more geology and fluid dynamics persons, not as involved (needlessly) with the numerical side of the equation.

In short, today we distinguish between simulators an frameworks that use the simulator to account for uncertainties and such. The border between these systems need to disappear as far as the end user is concerned, and we need a uniform language that describes natural systems. The quality of your predictive suite will then depend of finding the best implementations that provide useful insight. The only way we can bring simulator development to agile development is to have ist as an integral part of the predictive system, which we may not talk to directly. We cant have it both, as part of it and then as a separate part, with expectations as usual. Cherry picking like this, and expectations of an egglaying woolmilkpig wont work. At least not in an agile spirit.

It is just natural that software is outdated at some points, or more specifically the concepts of software. Tools we write today will experience the sam ething in five years...if we dont think that we are kidding ourselves. In the case of reservoir simulatros I believe it is more the end user interaction taht is outdated. For simulators tolive, their end user interaction has to die.


In short, this is about changing our expectation of simulator results towards

- relaxing expectations of repeatability of quantitative results
- strengthen customer feedback to algorithm implementation
- explicit trade offs of accuracy, performance and resolution and confidence/correctness, cost
- distributed and fail safe

- never rely on any notion of completeness of data
- work on incomplete and unbounded data, windowing
- realtime, always on (online learning), event time and processing time
- when no data is coming in, iterate and refine simulation, when new data arrive, incorporate them fast into first order results
- simulation is always running, improving results when no new data, incorporating new data fast into first order predictions
- support data streams, real time data: leverage that things move slowly in reservoirs, but the amount of sensor data can be huge

not for the fundamental parts of the implementation (discretization, solver), but for implementation of the high-end features: parallelization strategies, high-end features etc...

Too much already has been written about the futile effort to verify numerical models of subsurface processes (...), and the need to account for uncertainties using things like multi-realization frameworks (...). In short, it is thus very much accepted that simulation results for a single scenario/model/realization are not sufficient, a notion which is only added to when history-matched models are used, since it is known that for non linear problems adjoint solutions are not unique (...). In fact I don't believe any professional reserve booker will copy paste numeric values from a simulator output. I would leave it at this short paragraph to not dilute the point I'm trying to make in the core of this article, if it wasn't for [Naomi Oreskes'](http://science.sciencemag.org/content/263/5147/641) abstract which is one of the finest I've ever come accross, and I just want to have this here:

> Verification and validation of numerical models of natural systems is impossible. This is because natural systems are never closed and because model results are always non- unique. Models can be confirmed by the demonstration of agreement between observation and prediction, but confirmation is inherently partial. Complete confirmation is logically precluded by the fallacy of affirming the consequent and by incomplete access to natural phenomena. Models can only be evaluated in relative terms, and their predictive value is always open to question. The primary value of models is heuristic

When you drive (are driven) in an auto piloted car, a customer doesn't care if the car decelerates at x msec-2 or 1.1x. What matters is comfort and safety. Now we won't be able to get there - simulators are supposed to produce numbers after all - but we should look over the fence and see if there's not something we can learn. This is hard since numbers we produce don't result in comfort or discomfort in the engineer (well sometimes they do I guess), but actually contribute to hard numbers that end up in reserve reports to trade government bodies. But maybe we can meet in the middle - produce numbers, realize they're not written in stone and focus on maximizing recovery, which is a more abstract goal that simulators definitely can contribute to.

Also, any experienced reservoir engineer-geologist-geophycisist would never use a numerical simulation result of any sort be a single source of truth for anything.

This sort of non-determinism, however, is not what I'm referring to. What I getting at is that if you run a single simulation with deterministic input, a deterministic output is expected. In other words, if a user runs the same simulation twice, she expects identical results.


Why is this important? To a user, it would mean faster time to delivery of feature requests and cheaper software. This goes back to why it is important to developers:

- Less time spent on branching for backward compatible behavior, reduced maintenance for these branches
- Reduced regression testing effort (ie testing against changes in behavior)
- Less bugs (ie introduced change in behaviour regressions didn't pick up)


Let's look at some of the lengts that numerical applications

<script src="https://gist.github.com/plang85/6ebe733325263fe7e80823d342d414dd.js"></script>


Even though for this trivial example it is obvious (hindsight 20:20) which splitting strategy yields the more accurate operation, achieving thread invariance by adapting threading strategies is non-trivial for generic algorithms. That being said, it's done routinely in advanced parallel compute applicatins and libraries.

We need to make trade-offs [explicit](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/43864.pdf)! Accuracy, 

Non-determinism is also related to eventual consistency, convergence and consensus finding. Distributed systems which incorporate distributed data may be in states of inconsistence, and consensus finding strategies may have non-deterministic outcomes, in particular if fault-tolerant systems are incorporated. So there may be times at which simulation output does not honor existing data inputs.

In all, instead of locking down on to the numerical simulator as output, we need to elevate a level ourselves to a system in which the simulator has a role but is not the defining factor. 

Since we solve iteratively, errors tend to propagate. Given above FP traits (ulp), and the ill conditioned nature of the systems we solve (starting values), convergence failures can show up only at later stages and be masked initally.

Does that mean that all bets are off, we have and excuse to let our inner freebirds out, not care about correctness and revert to 16 bit precision? Of course it doesn't. We still need to verify against analytic solutions and we're still solving a discrete mathematical implementation of a concrete physical model. Also, in terms of precision, modern reservoir models push the boundaries of 64 bit precision already. It is the loss of precision issue that is very relevant there, with models that span well into the 100 km range in terms of absolute coordinate values, and at the same time using higher resolution, i.e. smaller cells. When we then compute geometric dependent properties the combination of the two poses challenges. There are approaches to address these problems, and we have to be vigilant for iteratively propagating errors when we design our tests during development.

However, as models get more complex and feature combinations reach higher levels, implementation choices are far from straight forward and without right or wrong. We need to allow engineers to make these in an agile spirit - get something working, expose it to the customer, get feedback and improve. Reliying on simulator to reproduca past results on the dot puts the (unrealistic) burden on the implementer to get things right the first time. Even worse, it indirectly keeps people from looking into improving algorithms, since that would most certainly induce a change in behavior. This also impacts time to market since it puts pressure on engineers to get it right the first time - laboring under this illusion.

But we definitely can learn to focus on higher level traits rather than discrete results. We know when our non linear solutions converge.

I certainly believe in physics based simulation. It enables/makes possible to reason over results. Often when we use simulation this is actually more important than the numerical results themselves. We see and can trace back physical processes over time, which elevates our understanding of the system. I believe we can construct a framework that supports above points on top of existing simulation engines.

This insigth through reasoning including intuitive visualization (we can follow cause consequence relationships of emergent physical processes over time in a simulatino), is something that physics based numerical models have going for them that ML applications [desire](https://research.google.com/pubs/pub43146.html). Another thing numerical simulations are good at is [reliable generalization](https://openreview.net/forum?id=Sy8gdB9xx&noteId=Sy8gdB9xx) from tests to real data and more principled extensino to incorporate new features (]So it would be somewhat troubling, would it not, if the answer to the question “why do neural networks work (generalize) as well as they do?” turned out to be “we don’t really know!”](https://blog.acolyer.org/2017/05/11/understanding-deep-learning-requires-re-thinking-generalization/)). Simulators are also not as easily [fooled](http://arxiv.org/pdf/1412.1897), and if they are, it's generally very loud. So there are things that AI applications can learn from simulations (or maybe they've [figured it all out by now] (https://arxiv.org/pdf/1612.00222v1.pdf)), but that's for another day.

In the wake of automation, the end-user won't interact with the reservoir simulator directly anymore, but through a prediction framework that will take care of some of these requirements. But that's for another day.