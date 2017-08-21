---
layout: post
title:  "Decline curves using recurrent neural networks"
subtitle:  "An approach to modelling events"
date:   2017-07-26
categories: machine learning
---

Decline curve modelling is probably one of the least challenging problems to throw at neural networks these days. Since I'm a sucker for low hanging fruit that nobody cares about, here goes a short write-up of a proof of concept implementation I recently demoed at a seminar. Proof of concept implementations are great since we don't have to put up with users and/or real data.

Shale wells are prime candidates for decline curve modelling. Refracking of a shale well breaks the underlying assumptions of decline curves, which is that the first order controls during the initial production phase, i.e. the production history, don't change over the forecast period. Let's assume we have representative production profiles like this

![Refracking event]({{ site.url }}/assets/profile_example.svg)

After importing some modules

```python
import numpy as np
import keras.models as kem
import keras.layers as kel
import sklearn.preprocessing as preproc
```

we can produce these artificial production profiles with a function such as

```python
def calc_exponential_decline(p0, exp, time):
    return p0 * np.exp(-exp * time)

def calc_two_stage_decline(p0, exp_stage_zero, exp_stage_one, time_max, time_next_stage, time_min=0., 
  production_jumpfactor=4., num=50, noise=0.1, noise_mean=1.):
    time = np.linspace(time_min, time_max, num)
    stage_one = np.where(time > time_next_stage)    
    production = calc_exponential_decline(p0, exp_stage_zero, time) * np.random.normal(noise_mean, noise, time.shape) 
    time_stage_one_relative = np.linspace(time_min, time_max-time_next_stage, len(time[stage_one]))
    production[stage_one] = calc_exponential_decline(production[stage_one][0] + p0/production_jumpfactor, exp_stage_one, 
      time_stage_one_relative) * np.random.normal(noise_mean, noise*2., time_stage_one_relative.shape)
    stage = np.zeros_like(time)
    stage[stage_one] = 1.
    production[production < 0.] = 0.
    return time, production, stage
```