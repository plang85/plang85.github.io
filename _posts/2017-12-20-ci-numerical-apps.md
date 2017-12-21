---
layout: post
title:  "Continuous integration systems for numerical applications"
date:   2017-12-21
categories: continuous-integration numerical-application testing
---

Here I share some of my experience in testing commercial numerical applications in a continuous integration setting, and the challenges that
go with these systems. In particular, CI principles, approaches to handle computationally expensive and long running 
regression tests, issues that go along with parallelized, non-liner systems, result tolerances, performance tests and so on.

### Take-home points (TL;DR for hipsters)
- All design decisions are based on one principle: **Dev-CI parity**. Nothing excuted by the CI system should be hard for the developer to run.
- CI systems should be dumb. How dumb you ask? They should run a single command and parse a results file. Then 
they can show pretty plots if so desired.
- This one command triggers a pipeline, the name of which shall be parameterized.
- The pipeline needs to be implemented as an abstraction, so that arbitrary steps and success criteria can be implemented and plugged into the system.
- Pipelines will contain steps of compilation, unit tests, regression tests, performance tests, you name it,
- A bisection system needs to be implemented, which also works with the same pipeline abstraction.
- Build infrastructure needs to go in containers.


:construction:
