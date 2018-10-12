---
layout: post
date: 2018-08-26
title: "A Second Pass at Statistics"
tags: list, datascience
---

In a quote very reminiscent of statistics, Arnold Sommerfeld said:

> Thermodynamics is a funny subject. The first time you go through it,
> you don't understand it at all. The second time you go through it,
> you think you understand it, except for one or two small points.
> The third time you go through it, you know you don't understand it,
> but by that time you are so used to it, it doesn't bother you any more. [1]

For my second pass at statistics, I focused on developing intuition and techniques for real-world applications. Here are a collection of resources that were useful for for me.

## Papers
 - <strong><a href="https://studies2.hec.fr/jahia/webdav/site/hec/shared/sites/czellarv/acces_anonyme/OringJASA_1989.pdf">Risk Analysis of the Space Shuttle: Pre-Challenger Prediction of Failure</a></strong><br />
Looks back on the ill-fated Challenger disaster and attempts to use better statistical analysis to measure accident risk.
 - <strong><a href="http://statweb.stanford.edu/~ckirby/brad/other/Article1977.pdf">Stein's Paradox in Statistics</a></strong><br />
An extremely readable introduction to the Stein Paradox. We learn that when we have more than 3 gaussians, the best estimator of the their means is not the sample mean!
 - <strong><a href="https://www.cs.princeton.edu/courses/archive/spring10/cos424/papers/FreedmanStark2003.pdf">What is the Chance of an Earthquake?</a></strong><br />
A non-mathematical paper which discusses what probabilities mean using a fascinating case study.

<br />
## Other Lists of Papers
As I sift through the papers here, I'll move the ones I like up into my list.
- <strong><a href="https://xianblog.wordpress.com/2010/07/01/top-15-and-more/">Xi'an's List</a></strong><br />
Have not gone through everything here yet, but they seem very promising.
- <strong><a href="https://andrewgelman.com/2015/03/04/these-are-the-statistics-papers-you-just-have-to-read/">Andrew Gelman's List</a> and <a href="https://andrewgelman.com/2010/06/25/classics_of_sta/">Another</a></strong><br />
A few gems, but many are presently out of reach for me. Better luck on the third pass perhaps.
- <strong><a href="https://normaldeviate.wordpress.com/2013/07/23/the-five-jeff-leeks-challenge/">Larry Wasserman's</a><br />

<br />
## Questions
 - How can we improve hypothesis testing to take into account seasonality effects present in the responses of the control and treatment groups of an experiment?
 - During data analysis, one often has to decide on the most informative ways to segment the dataset. What is a good measure of high-signal segmentation? Can we apply information theory concepts like mutual information? What about machine learning classification? What if we need confidence intervals around these metrics?
 - Why is it when we use the sample variance to estimate the population variance, we need to renormalize by n/n-1? (See [Bassel's Correction](https://en.wikipedia.org/wiki/Bessel%27s_correction)) <a href="https://stats.stackexchange.com/q/87422">This</a> and <a href='https://stats.stackexchange.com/q/3932'>this</a> are halfway good answers.
 - What is it about statistics that makes it (seem?) so much harder to learn than, say, Physics?

<br />

---
Much thanks to Olivia Angiuli for piggybacking me on her adventure through statistics grad school, and Anirudh Sankar for intriguing discussions.

[1] Arnold Sommerfeld, as quoted in Salvatore Califano's *Pathways to Modern Chemical Physics* (2012) and by many other science authors ([Wikipedia](https://en.wikiquote.org/wiki/Thermodynamics))
