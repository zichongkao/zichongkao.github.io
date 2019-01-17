---
layout: post
date: 2019-01-17
title: "How To Predict Any Three Events More Accurately"
---

I learned about the James-Stein Estimator a few months ago and for an instant my world shook. As intelligent, well-educated adults, we'd like to think that we have a good understanding of the world; that though we may not know everything perfectly, we have the basic tools for reasoning down pat. But here come Messrs James and Stein who tell us that all this time we've been predicting multiple events by taking historical averages, we've been doing things wrongly. In fact, a more accurate method exists!

I couldn't believe it and had to examine the proof for myself. This exercise was, however, wholly unsatisfying. The existing proofs tend to take a blackbox approach that preclude real understanding. The original James-Stein proof basically says: Here, by some magic, we conjured up this estimator. Look! Using this identity (Stein's Lemma), it has less squared error than the naive estimator. Voila!

During my time at the [Recurse Center](https://www.recurse.com), I tried to come up with an alternative proof &mdash; one that would compare the probability clouds of the naive estimator and the James-Stein estimator and use geometry to tally up the aggregate change in squared error. I could then play with the geometry to determine why the James-Stein estimator only works in three or more dimensions.

My proof failed. I found an elegant approach to decompose the geometry into two separate phenomena. However, the phenomena were nearly but not perfectly independent of each other. As such, I couldn't calculate their effects separately and put them together again. Nevertheless, there is much pedagogical value in this sorta-proof. More than any of the existing proofs, it helped me understand why the James-Stein estimator works.

Here is the presentation I gave at the Recurse Center. You might want to skip to slide 7 if you're already familiar with the James-Stein estimator.

<div style="position:relative;padding-top:60.3%;">
  <iframe src="https://docs.google.com/presentation/d/e/2PACX-1vTCd6023cuM2zC5stMXhmun2EYSzuqawaCalD-0lqxsfA_cRVbIRKE-0RaMzmUDN5pGkRuoHIBj-_gS/embed?start=false&loop=false&delayms=3000" frameborder="0" style="position:absolute;top:0;left:0;width:100%;height:100%;" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>
</div>
