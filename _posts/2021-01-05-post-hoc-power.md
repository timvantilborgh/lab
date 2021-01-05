---
title: Post-hoc power analysis using cumulative probabilities of finding significance
author: [Tim Vantilborgh]
categories: [research methods]
background: https://images.unsplash.com/photo-1458007683879-47560d7e33c3?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=1927&q=80
---

Conducting a power analysis can be difficult because you need a good estimate of the effect size that you want to be able to detect given a certain sample size and alpha level. Obtaining realistic estimates of effect sizes can be difficult when there is no prior research that looked at the same effect using a similar analytical approach. This issue becomes even more complex when you plan to use a more complicated analytical approach. Various tools exist when you want to estimate power for basic statistical techniques, such as regression or ANOVA, but when you are using more complicated techniques such as multilevel or structural equation models. 

Alternative approaches exist (e.g., Smallest Effect Size Of Interest - see [Lakens, 2017](https://journals.sagepub.com/doi/full/10.1177/1948550617697177) or sequential analysis - see [Lakens, 2014](https://onlinelibrary.wiley.com/doi/full/10.1002/ejsp.2023?casa_token=rCmbFzUtawMAAAAA%3ABdFZNurl32u-Wn203vjeyDkmpmJCf87BYf5eyu-YuJNNME7lJnsblRrDjZKgbLDrUYVHrN2F9jt8Ug8)) and you can always use simulation to estimate power (see [Arend & Schäfer](https://psycnet.apa.org/record/2018-48374-001)). However, these alternatives are not necessarily easy to implement. I was therefore intrigued when I read a new article by [Bliese & Wang (2020)](https://journals.sagepub.com/doi/abs/10.1177/0149206319886909) published in Journal of Management. 

In this article, they explain how you can easily estimate post-hoc power by calculating the cumulative probability of finding significance. In essence, this cumulative probability tells you the probability of finding significant effects given the characteristics of your sample and model. The cumulative probability can be obtained in various ways:

1. You can use non-parametric bootstrapping and record for each bootstrap whether an effect was significant (1) or not (0), after which you calculate the percentage of significant effects accross all bootstrapped samples.
2. You can take the *t* or *z* value of your test, and calculate the cumulative probability directly. The formula for the *t* value is best used when sample sizes are small, but with samples of 100 observations or more you can use the formula for the *z* value.  In R, this formula based on the *z* value is simply:

```
1-pnorm(1.96-abs(X))
```

with X being the *t* or *z* value that is associated with your effect.

Bliese and Wang (2020) demonstrate how to use this approach in R, but this approach can be used with any type of analysis or software. To try it out for myself, I ran a simple structural equation model in Mplus. I use a dataset that contains 226 observations of 15 variables (the data and the scripts can be downloaded from [OSF](https://osf.io/njuw6/) in the folder 210105 Post-hoc power analysis):

* Items 1 and 2 measure psychological contract fulfilment
* Items 3 tot 6 measure feelings of violation
* Items 7 to 15 measure trust

I then estimated a structural equation model using the following code (see example.inp):

```
TITLE: Demonstration post-hoc power

DATA:
FILE = "dataset.txt";

VARIABLE:
NAMES = fulf1 fulf2 viol1 viol2 viol3 viol4 
trust1 trust2 trust3 trust4 trust5 trust6 trust7 trust8 trust9;
USEV = fulf1 fulf2 viol1 viol2 viol3 viol4 trust1 trust2 trust3 trust4 trust5 trust6 trust7 trust8 trust9;

ANALYSIS:

MODEL:
fulfilment by fulf1 fulf2;
violation by viol1 viol2 viol3 viol4;
trust by trust1 trust2 trust3 trust4 trust5 trust6 trust7 trust8 trust9;

trust on violation fulfilment;
violation on fulfilment;

OUTPUT:
sampstat;
```

If you are familiar with Mplus, you will notice that this is a mediation model with three latent variables. The output file gives a lot of information (see example.out), but I will jump to what is probably the most important section, namely the effects of fulfilment on feelings of violation and trust and the effect of feelings of violation on trust.

```
TRUST    ON
    VIOLATION          0.397      0.208      1.912      0.056
    FULFILMENT        -0.155      0.155     -1.005      0.315

 VIOLATIO ON
    FULFILMENT        -0.260      0.053     -4.869      0.000
```

The first number in each line is the parameter estimate, the second number is the standard error, the third number of the ratio of the parameter estimate and the standard error (i.e., a *z* value), and the final number is the *p*-value. We will take these *z* values and use them in our formula (see R script example.R):

```
1-pnorm(1.96-abs(1.912)) # Cumulative probability of violation on trust
1-pnorm(1.96-abs(-1.005)) # Cumulative probability of fulfilment on trust
1-pnorm(1.96-abs(-4.869)) # Cumulative probability of fulfilment on violation
```

We would find the following results:

* The cumulative probability of the effect of feelings of violation on trust is .48. This would mean that there is a lot of uncertainty about this effect, which is no surprise as the *p*-value is just over .05. It would suggest that there is only 48% chance of finding a significant effect, given the characteristics of our sample and our model. Put differently, we have a power of 48% to find a significant effect, given our sample properties and model.
* The cumulative probability of the effect of fulfilment on trust is .17. This suggests that finding a significant effect with a similar sample and model is very low. Put differently, we have a power of 17% to find a significant effect, given our sample properties and model.
* The cumulative probability of fulfilment of violation is .998. The likelihood that we can replicate this effect, given the characteristics of our sample and model is extremely high. Put differently, we have a power of .998% to find a significant effect, given our sample properties and model.

Now I know that post-hoc power analysis is a debated topic. But I think that this approach can be useful in some cases. Bliese and Wang mention several advantages of routinely reporting the cumulative probability. I think that an extra advantage is that you could use this approach if reviewers ask you to report a post-hoc power analysis (which is a question that I have received regularly in the past couple of years).