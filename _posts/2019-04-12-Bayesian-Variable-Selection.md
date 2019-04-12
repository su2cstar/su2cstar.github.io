---
layout: post
title: "A Bayesian approach to constructing multiple confidence intervals of selected parameters with sparse signals"
date:   2019-04-12T00:00:00
author: Juyoung Ahn
categories: Bayesian-Variable-Selection
tags: FDR PFCR BFCR ZIMP
use_math: true
---
Zhigen Zhao and Sanat K. Sarkar  
Statistica Sinica  
Vol. 25, No. 2 (April 2015), pp. 725-741  

## 1. Introduction
### 1.1. Winner's Curse

* The bias intruduced by the selection
* Cause the usual confidence interval to have an extremely low coverage porbability

### 1.2. Toy Example

$$ \begin{align*}
Y_i | \beta_i  \stackrel{ind}{\sim}N(\beta_i,1) , \;
\beta_i \sim \begin{cases} 0 &\text{with probability 0.8} \\
N(0,1) &\text{with probability 0.2} \end{cases}
\end{align*}
$$

Let $Y_{(1)} = \max_{1\le i \le p} Y_i\;$ and $\;\beta_{(i)}$ be the coreesponding parameter. Constructing usual 95% confidence interval. $CI_{(1)}=Y_{(1)} \pm 1.95$. As reapeated 10,000 times to simulate the coverage probability. It was 42.4% and it is easy to see that $E[Y_{(1)}] \ge \beta_{(1)}$.

* Developing a confidence interval for a selected parameter that is statistically sound is important
* Constructing confidence intervals for multiple selected parameters subject to controlling an overall measure of false coverage

### 1.3. Zero Inflated Mixture Prior(ZIMP)

$$\boldsymbol{Y} | \boldsymbol{\beta} \sim f(\boldsymbol{y}|\boldsymbol{\beta})$$

where $\boldsymbol{\beta} = (\beta_1, \dots, \beta_p)$ and

$$
\beta_i \sim \pi(\beta_i) = \pi_0 1(\beta_i = 0) + (1-\pi_0) \psi(\beta_i)
$$

$\pi_0$ is the prior of $\beta_i$ being zero, and $\psi(\beta_i)$ is the distribution of $\beta_i$ given $\beta_i \ne 0$

* This model is useful in genetic experiments where many of the genes are believed to be non-differentially expressed, and in regressions with sparsity structure.
* If $\pi_0$ is large, the posterior probability of $\beta_i$ being 0 can also large
* this is problematic for equal-tail credible interval since it can obtain zero a high proportions of times
* high-posterior-density(HPD) regions : HPD regions always include zero due to the existence of point mass at zero

### 1.4. Credible interval

We can say that our $100(1 – \alpha)\%$ credible interval $C$ defines the subset on the parameter space, which we’ll call $\theta$, such that the integral

$$
\int_{C} \pi(\boldsymbol{\theta} | \boldsymbol{X}) d\boldsymbol{\theta} = 1 - \alpha
$$

$\pi$ here is the posterior probability distribution. So, for instance, if you needed a 95 percent credible interval, you would be working to find the interval over which the integral of the posterior probability distribution sums to 0.95

### 1.5 Loss Function

Consider a loss function that penalizes the inclusion of zero when $\beta_i$ is indeed non-zero.


* The Bayesian decision interval is then forced to include zero if there is overwhelming ovidence that  $\beta_i$ is 0 
* equivalently the local fdr score $ P( \beta_i =0 | \boldsymbol{Y}) $ is large
* Local fdr score is compared to a tuning parameter $k_2$ used in loss function
* the zero component is included in the interval if the local fdr score is greater than $k_2$

We determine the $k_2$ so that the posterior false coverage rate(PFCR) or the Bayes false coverage rate(BFCR) is controlled at a desired level. 
$k_2$ is often larger than $\alpha$, the proposed interval doesn't have to include zero even $ P(\beta_i =0 | \boldsymbol{Y}) > \alpha$