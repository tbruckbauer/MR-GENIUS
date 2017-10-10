# MR-GENIUS

The "genius" R package implements mendelian randomization G-estimation under no interaction with unmeasured selection 
(MR GENIUS), based on work by Tchetgen Tchetgen, E., Sun, B. and Walter, S. (2017).

# How to Install

To install this package, use devtools:

```r
devtools::install_github("bluosun/MR-GENIUS")
```
(requires R version >= 3.4.1) 
# Overview
The package currently offers one function "genius" which returns the MR GENIUS estimate, estimated variance, confidence interval at user-specified significance level as well as the p-val corresponding to the two-sided Wald test of null causal effect of the exposure on the outcome. The estimator is given in equations (6) and (12) of Tchetgen Tchetgen et al (2017) for single and multiple instruments respectively, under an additive outcome model. The term E[A|G] is modelled under the logit and identity links for binary and continuous exposure respectively, with a linear predictor consisting of the main effects of all available instruments.  

```r
#Y: A numeric vector of outcomes
#A: A numeric vector of exposures (binary values should be coded in 1/0)
#G: A numeric matrix of IVs; each column stores values for one IV
#alpha: Significance level for confidence interval (default value=0.05)
#lower: The lower end point of the causal effect interval to be searched (default value=-10) 
#upper: The upper end point of the causal effect interval to be searched (default value=-10) 

genius(Y, A, G, alpha = 0.05, lower=-10, upper=10)
```

## Examples

```r
# the following packages are needed to simulate data; they are not required to run "genius" package
library("msm")
library("MASS")

expit <- function(x) {
    exp(x)/(1+exp(x))
}

### example with binary exposure, all instruments invalid ###
# true causal effect, beta = 1.0
# Number of instruments, nIV = 10
# Y: vector of outcomes
# A: vector of exposures
# G: matrix of instruments, one column per instrument

nIV=10; N=5000; beta=1;
phi=rep(-0.02,nIV); gamma=rep(-0.15,nIV); alpha=rep(-0.5,nIV);
Gn = mvrnorm(N,rep(0,nIV),diag(rep(1,nIV)))

G  = (Gn>0)*1;
U= as.vector(phi%*%t(G))+ rtnorm(n=N,mean=0.35,lower=0.2,upper=0.5);
A = rbinom(N,1,expit(as.vector(gamma%*%t(G)))+U-0.35-as.vector(phi%*%t(G)));
Y = as.vector(alpha%*%t(G)) + beta*A + U + rnorm(N);

genius(Y,A,G);

### example with continous exposure, all instruments invalid ###

nIV=10; N=500; beta=1;
phi=rep(-0.5,nIV); gamma=rep(-2,nIV); alpha=rep(-0.5,nIV);
lambda0=1; lambda1=rep(0.5,nIV);
Gn = mvrnorm(N,rep(0,nIV),diag(rep(1,nIV)))

G  = (Gn>0)*1;
U = as.vector(phi%*%t(G))+rnorm(N);
A = as.vector(gamma%*%t(G)) +U + rnorm(N,mean=0,sd=abs(lambda0+as.vector(lambda1%*%t(G))));
Y = as.vector(alpha%*%t(G)) + beta*A + U + rnorm(N);

genius(Y,A,G);
```

## References 
Tchetgen Tchetgen, E., Sun, B. and Walter, S. (2017). <a href="https://arxiv.org/abs/1709.07779"> The GENIUS Approach to Robust Mendelian Randomization Inference.</a> arXiv e-prints.


