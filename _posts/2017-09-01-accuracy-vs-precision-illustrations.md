---
layout: post
title:  "All these precision vs. accuracy illustrations make no sense to me"
subtitle:  "Precision is about formats"
date:   2017-09-01
categories: rants accuracy precision illustrations
---

Once upon a time I was preparing lecture notes for a numerical methods class. One of the things that always come up when dealing with the much beloved IEEE floating point model is a discussion of precision (relates to a format) and accuracy (relates to an operation). So when I googled 'accuracy vs. precision' in a search for some illustrative firepower and to avoid drawing anything myself, the illustrations I found on the first results page (and I mean...) almost exclusively looked like this

![Nope]({{ site.url }}/assets/accuracy_vs_precision_wrong.png)

which just doesn't make much sense to me. The accuracy part yes, sure, but the precision part? In sloppy terms, precision represents the number of discretely representable values within a range. In the above plot, the precision is always the same. The lady should look like this

![Better]({{ site.url }}/assets/accuracy_vs_precision.png)

right?
