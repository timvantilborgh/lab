---
title: A more intuitive approach to effect sizes - Persons as effect sizes
author: [Tim Vantilborgh]
categories: [research methods]
background: https://images.unsplash.com/photo-1513682121497-80211f36a7d3?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=2134&q=80
---

Effect sizes are necessary to determine if an effect is substantial or not. For example, a correlation of .30 would often be considered a medium-sized effect (based on Cohen's guidelines; whether these guidelines are useful is a different discussion...). An effect can be statistically significant, yet when the effect size is small one can wonder if the effect has any practical relevance. However, effect sizes are often difficult to interpret. What does it mean when a correlation signifies a small, medium, or large effect? How should you interpret various effect sizes, such as R<sup>2</sup>sup>, \eta<sup>2</sup>sup>, or f<sup>2</sup>sup>?

In a recent article, Grice et al. (2020) propose a simple, intuitive effect size that is understandable by both researchers and the broader public (https://journals.sagepub.com/doi/abs/10.1177/2515245920922982). Basically, they propose computing how many people in the study behaved or responded in a manner consistent with theoretical expectations. This can be computed as "percent correct classifications" (PCC). An interesting aspect of their approach is that describes the individuals rather than the inferences at the group-level, thus circumventing the ecological fallacy that we can infer how individuals behave based on group-level statistics. 

To explore the idea behind the PCC effect size, I created a simple script in R with some simulated data and with a real data set. The R script (https://osf.io/dnuq4?show=view&view_only=) and the dataset (https://osf.io/teqc2?show=view&view_only=) can be downloaded from OSF.

To run the R script, make sure that you have the tidyverse and the faux package installed. We will use the faux package to simulate data.

	# Load libraries (install these first if you don't have them yet)
	library(tidyverse)
	library(faux)

# Example 1

For the first example, assume that we are doing a survey study with 100 participants in which we measure perceptions of psychological contract breach and feelings of violation. Psychological contract breach refers to the perception that an employee may have that her/his employer has failed to fulfil one or more obligations to her/him. Feelings of violation refer to negative emotions that may follow from breach perceptions, such as anger, frustration, and resentment. Theoretically, we expect a positive relationship between both variables: people who perceive a psychological contract breach will report stronger feelings of violation than people who do not perceive a psychological contract breach. Let's use the faux package to simulate some data on both variables, assuming that the means of both variables are equal to 3, the standard deviations are equal to 1, and the correlation between both variables is 0.

	# Generate data
	df <- rnorm_multi(n = 100,
	                  mu = c(3, 3),
	                  sd = c(1, 1),
	                  r = c(0),
	                  varnames = c("breach", "violation"),
	                  empirical = FALSE)

The next step to get the PCC effect size is dichotomizing both variables with a median split. While there are obvious problems with a median split (which are discussed in the article), this should work when the variables are normally distributed. 

	# Perform median split on both variables
	df$breach.dichotomized = if_else(df$breach < median(df$breach), "low", "high")
	df$violation.dichotomized = if_else(df$violation < median(df$violation), "low", "high")

Theoretically, we expect that respondents who are categorized as "high" on the dichotomous breach variable will also be categorized as "high" on the dichotomous violation variable. And we would expect that respondents who are categorized as "low" on the dichotomous breach variable will also be categorized as "low" on the dichotomous violation variable. Whenever such a combination is observed, we can describe this respondent as correctly classified. Whenever there is a different combination of both dichotomous variables (e.g., "low" breach and "high" feelings of violation), it is inconsistent with our theoretical prediction and we count it as incorrectly classified.

So, to get the PCC effect size, we will compute a cross-table with the percentage of cases for each combination of the two dichotomous variables. Based on this, we can compute the percentage correctly classified and the percentage incorrectly classified.

	# Create cross-table
	proportions = prop.table(table(df$breach.dichotomized, df$violation.dichotomized))
	correctly.classified = proportions[1,1] + proportions[2,2]
	incorrectly.classified = proportions[1,2] + proportions[2,1]

In this case, we would find that 50% of all respondents are correctly classified and 50% are incorrectly classified. Put differently, 50% follows the theoretical expectations which seems to be equal to chance (not surprising as the correlation between both variables was set to 0).

# Example 2

Let's run the same example, but change the correlation between both variables to 0.5 (a strong effect size according to Cohen's guidelines).

	# Generate data
	df <- rnorm_multi(n = 100,
	                   mu = c(3, 3),
	                   sd = c(1, 1),
	                   r = c(0.5),
	                   varnames = c("breach", "violation"),
	                   empirical = FALSE)

	# Perform median split on both variables
	df$breach.dichotomized = if_else(df$breach < median(df$breach), "low", "high")
	df$violation.dichotomized = if_else(df$violation < median(df$violation), "low", "high")

	# Create cross-table
	proportions = prop.table(table(df$breach.dichotomized, df$violation.dichotomized))
	correctly.classified = proportions[1,1] + proportions[2,2]
	incorrectly.classified = proportions[1,2] + proportions[2,1]

Running this code would yield 66% correctly classified cases and 34% incorrectly classified cases (note that your results may differ somewhat because of the randomly simulated data). This is intuitively understandable: about 2/3rd of all participants seem to (report to) behave in line with what we theoretically expect.

# Example 3

Let's simulate data one more time, but now we will use a correlation with a whopping magnitude of .90 and see what happens with the PCC effect size.

	# Generate data
	df <- rnorm_multi(n = 100,
	                  mu = c(3, 3),
	                  sd = c(1, 1),
	                  r = c(0.9),
	                  varnames = c("breach", "violation"),
	                  empirical = FALSE)

	# Perform median split on both variables
	df$breach.dichotomized = if_else(df$breach < median(df$breach), "low", "high")
	df$violation.dichotomized = if_else(df$violation < median(df$violation), "low", "high")

	# Create cross-table
	proportions = prop.table(table(df$breach.dichotomized, df$violation.dichotomized))
	correctly.classified = proportions[1,1] + proportions[2,2]
	incorrectly.classified = proportions[1,2] + proportions[2,1]

We now have 84% correctly classified respondents and 16% incorrectly classified respondents. Put differently, more than 4/5th of the sample (reports to) behave(s) in line with our theoretical expectations. Again, this is more easy to understand than saying that there is a "very strong correlation" between both variables.

# Example 4: Real data

Ok, let's use real data for the final example. The data set "data_example_pcc.csv" contains real data from a survey in which breach perceptions and feelings of violation were measured. The dataset is rather small (53 observations), but it serves the purpose of demonstrating the PCC effect size. We will start by reading in the data and estimating the correlation between both variables.

	df = read_csv2("data_example_pcc.csv")

	# Correlation
	cor.test(df$breach, df$violation)

The correlation between both variables is .36 (a medium sized effect) and is statistically significant (*p* = .012).

	Pearson's product-moment correlation

	data:  df$breach and df$violation
	t = 2.6038, df = 45, p-value = 0.01245
	alternative hypothesis: true correlation is not equal to 0
	95 percent confidence interval:
	 0.08333751 0.58792082
	sample estimates:
	      cor 
	0.3618447 

We will use the same approach as with the simulated data:

	# Perform median split on both variables
	df$breach.dichotomized = if_else(df$breach < median(df$breach, na.rm=T), "low", "high")
	df$violation.dichotomized = if_else(df$violation < median(df$violation, na.rm=T), "low", "high")

	# Create cross-table
	proportions = prop.table(table(df$breach.dichotomized, df$violation.dichotomized))
	correctly.classified = proportions[1,1] + proportions[2,2]
	incorrectly.classified = proportions[1,2] + proportions[2,1]

We find that 61.70% of all participants are correctly classified. This does not appear to be a very strong effect, as it means that 38.30% of the participants report behaving in a way that is inconsistent with theory (e.g., perceiving a breach but not experiencing any feelings of violation or perceiving no breach but still experiencing feelings of violation). 

Overall, I feel that the PCC effect size has a certain appeal. It is an easy way to express the strength of an effect to a broader audience. You don't need to understand statistics to intuitively understand the PCC values. The examples that I use here are all based on a simple correlation, but the article also describes how the PCC can be used to estimate effect sizes in experimental designs or when assessing risk. Moreover, it also describes how the PCC can be used to understand the data behind the inferences.