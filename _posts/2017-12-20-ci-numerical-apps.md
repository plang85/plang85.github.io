---
layout: post
title:  "Continuous integration systems for numerical applications"
date:   2017-12-21
categories: continuous-integration numerical-application testing
---

Here I share some of my experience in redesigning a continuous integration system for a large numerical application and the challenges that go with these systems. In particular, CI principles, approaches to handle computationally expensive and long running regression tests, issues that go along with parallelized, non-liner systems, iterative solutions, floating point errors, result tolerances, performance tests and so on.

## Take-home points (TL;DR for hipsters)
- All design decisions derive from one principle: **Dev-CI parity**. Developers and CI run the same commands, in identical environments.
- CI systems should be dumb. How dumb you ask? They should run a single command and parse a results file. Then 
they may show pretty plots if so desired.
- This one command triggers a pipeline, the name of which shall be parameterized.
- The pipeline needs to be implemented as an abstraction, so that arbitrary steps and success criteria can be implemented and plugged into the system.
- Pipelines will contain steps of unit tests, regression tests, performance tests, you name it. They receive the artifact on which to work on through a module, which in turn either builds from source (specific commit) or points to some location.
- A bisection system needs to be implemented, which also works with the same pipeline abstraction.
- Chose success criteria for regression tests wisely - this is where you earn your money, and what constrains any future development. Familiarize yourself with FP errros and time series comparison. Know what's important to your customers.

## Challenges 

### User input - the futile task of sanitization and whitelisting

Input to physics based simulators consists of options and arbitrary numeric values in integer and floating point format. While we can sanitize these based on ranges of validity, *e.g.* physical constraints, and blacklist non-sensical combinations of options, *i.e.* combinations of algorithms known to cause issues, rigorous validation of input values and whitelisting of supported workflows seems out of reach. For all intents and purposes, the number of possible combinations of user input is infinite. This is in sharp contrast to web apps with graphical interfaces and their backends, which most of the currently developed CI tools target. There is no *happy path* in most numerical apps or libraries, there is only chaos. The user is exposed to the full wrath that is floating point arithemtic, non-linear algorithms, parallelization, iteration and so on. 

### Parallelization

Floating point arithmetic is sensitive to partitioning - think workload splitting of computations in multiple processes and threads. Results will differ between different parallelizations, unless implemented in invariant manner. In CI systems we usually test for several aspects related to this: scaling and invariance. The former referrs to performance, the latter to results. Tolerance aware success criteria thus need to be applied.

## Pipelines

They shouldn't be defined in the CI system. I know, you're probably thinking of GoCD and their nice interface, fanning and dependecies of individual steps and more. However, it is imperative that any pipeline can be run easily on a dev's machine. This means either duplication of pipeline steps, or definition of the pipeline outside of the CI system. I strongly recommend the latter, even though this probably incurrs a lessened user experience n the CI system. As benefits you will gain single source of truth value and much improved debugging and regression identification on the dev side.

### VCS

Pipelines must not to be aware of the version control system and the commit on which to work. Instead they will be injected the artifacts on which to work on in form of a module, this module can either build from scratch or use existing artifacts.

### Ansible

Use Ansible for your pipelines. It's flexible, doesn't require worker node installations, works in Python (which is part of the main-stream stack in most numerical applications), and because of its SSH approach plays nicely with dumping jobs off to compute clusers/queuing system head nodes.

### Success criteria

The bad news here is that we can only screw this up. If defined too loosely, your customers will loose confidence in your product if their results change unacceptably (very subjective) every other update. If defined to tightly, you will end up living a OS kernel developers life under the no regression mantra, and seriosly impede agile approaches to development. It may increase the pressure to get things right the first time to unhealthy levels, and may stall development. It may lead to having maintain different options and implementation variants, which again will stall development pace through immense maintenance burden. Success criteria for CI tests have the potential to make or break your product and development approach.

## Bisection

Long-running tests ('20') will necesserily test on an amalgamation of commits. Upon test failures, a bisection algorithm will be needed to identify culprits. This search algorithm is aware of the VCS, to walk code versions, and the pipeline in question - that's it.

## More on simulation applications

Here scaling tests will be performed on changing spatial discretizations of the same physical objects. This requires special care in defining comparison functions, since relying on integrals/cumulatives can be a serious pitfall.

... :construction:
