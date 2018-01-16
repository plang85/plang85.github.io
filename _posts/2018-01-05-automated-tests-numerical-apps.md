---
layout: post
title:  "Automated testing of numerical applications"
date:   2018-01-05
categories: numerical-application testing
---

:construction:

Automated tests are lighttowers. Development and refactoring of numerical applications is a fight against the five-headed hydra that is floating point errors, parallelization, non-linear systems, iterative solutions, and an infinite number of combinations of user input.

Automated tests come mostly in two forms, unit tests and regression tests. The former dos not suffer from nearly as much ambiguity as the latter. Also unit tests are usually cheap to run, common place and thus don't need special attention here. Unfortunately, unit test often are not sufficient, and I'll try to share my 0.02$ on why that is. In a nutshell
- Iterative solutions
- Ill-conditioned systems
- Floating point arithmetic
- Infinite combinations of user input

