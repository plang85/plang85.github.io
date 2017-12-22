---
layout: post
title:  "Continuous integration systems for numerical applications"
date:   2017-12-21
categories: continuous-integration numerical-application testing
---

Here I share some of my experience in redesigning a continuous integration system for a large numerical application and the challenges that go with these systems. In particular, CI principles, approaches to handle computationally expensive and long running regression tests, issues that go along with parallelized, non-liner systems, result tolerances, performance tests and so on.

### Take-home points (TL;DR for hipsters)
- All design decisions derive from one principle: **Dev-CI parity**. Developers and CI run the same commands, in identical environments.
- CI systems should be dumb. How dumb you ask? They should run a single command and parse a results file. Then 
they may show pretty plots if so desired.
- This one command triggers a pipeline, the name of which shall be parameterized.
- The pipeline needs to be implemented as an abstraction, so that arbitrary steps and success criteria can be implemented and plugged into the system.
- Pipelines will contain steps of compilation, unit tests, regression tests, performance tests, you name it,
- A bisection system needs to be implemented, which also works with the same pipeline abstraction.

## Challenges 

### User input - the futile task of sanitization and whitelisting

Input to physics based simulators consists of options and arbitrary numeric values in integer and floating point format. While we can sanitize these based on ranges of validity, *e.g.* physical constraints, and blacklist non-sensical combinations of options, *i.e.* combinations of algorithms known to cause issues, rigorous validation of input values and whitelisting of supported workflows seems out of reach. For all intents and purposes, the possible combinations of user input are infinite. This is in sharp contrast to web apps with graphical interfaces, which most of the currently developed CI tools target. There is no *happy path* in most numerical apps or libraries, there is only chaos. The user is exposed to the full wrath that is floating point arithemtic, non-linear algorithms, parallelization and so on. 

## Pipelines

They shouldn't be defined in the CI system. I know, you're probably thinking of GoCD and their nice interface, fanning and dependecies of individual steps and more. Remember that most CI and DevOps tools do not target our domain. The pipeline needs to be able to run on a dev's machine, it should not be defined in the CI system. Use Ansible or what have you.

### Ansible

Use Ansible for your pipelines. It's flexible, doesn't require worker node installations, works in Python (which is part of the main-stream stack in numerical applications), and because of it's SSH strategy works nicely with dumping jobs of to compute clusers/queuing systems.

## Bisection

Long-running tests ('20') will necesserily test on an amalgamation of commits. Upon test failures, a bisection algorithm will be needed to find the culprit. This algorithm is aware of the VCS and the pipeline in question, that's it.

... :construction:
