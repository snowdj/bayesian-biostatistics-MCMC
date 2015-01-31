How to calculate posterior probability?
========================================================
author: Jan Smycka
date: 


The data
========================================================
We have five data points from poisson distribution

```r
x=rpois(5,20)
x
```

```
[1] 21 20 19 20 15
```
 
 
We would like to know posterior distribution of $\lambda$

Analytical solution
========================================================

Poisson distribution posterior can be expressed as

$$p(\lambda|x)=\dfrac{p(x|\lambda)*p(\lambda)}{p(x)}$$

where

$$p(x|\lambda)=\prod\limits_{i=1}^n \dfrac{\lambda^{x_i}e^{-\lambda}}{x_i!}; p(\lambda)=0.01\\$$


$$p(x)=\int_\lambda (\prod\limits_{i=1}^n \dfrac{\lambda^{x_i}e^{-\lambda}}{x_i!}*0.01)  d\lambda\\ $$



Analytical solution
========================================================
 
 - is accurate
 
 
 
 - cannot be obtained for most model types
 

Numerical computation
========================================================
We can try to compute numerically

The numerator can be expressed as R function

```r
baypo=function(l){
  #likelihood
  lh=function(l){
    prod(((l^x)*exp(-l))/factorial(x))
  }
  #likelihood and prior
  100000000*lh(l)*dunif(l,0,100)
  }
```


Numerical computation
========================================================

Now we can generate a grid and feed the function

```r
lambda=seq(0,100,0.01)
postdist=NULL
for (i in 1:length(lambda)){
  postdist[i]=baypo(lambda[i])
  }
prob=postdist/sum(postdist)
```


Numerical computation
========================================================
![plot of chunk unnamed-chunk-4](pres-figure/unnamed-chunk-4-1.png) 

Numerical computation
========================================================
 - theoretically works always
 
 - can be paralelized on clusters
 
 - is terribly slow and wasteful
 

Metropolis - Hastings
========================================================
So what about more clever ways of sampling?

![plot of chunk unnamed-chunk-5](pres-figure/unnamed-chunk-5-1.png) 

Metropolis - Hastings
========================================================
If we want to know where the modus is

![plot of chunk unnamed-chunk-6](pres-figure/unnamed-chunk-6-1.png) 

Metropolis - Hastings
========================================================
If we want to know where the modus is


![plot of chunk unnamed-chunk-7](pres-figure/unnamed-chunk-7-1.png) 

Metropolis - Hastings
========================================================
If we want to know where the modus is


![plot of chunk unnamed-chunk-8](pres-figure/unnamed-chunk-8-1.png) 

Metropolis - Hastings
========================================================
If we want to know where the modus is


![plot of chunk unnamed-chunk-9](pres-figure/unnamed-chunk-9-1.png) 
Metropolis - Hastings
========================================================
If we want to know where the modus is


![plot of chunk unnamed-chunk-10](pres-figure/unnamed-chunk-10-1.png) 

Metropolis - Hastings
========================================================
If we want to know where the modus is


![plot of chunk unnamed-chunk-11](pres-figure/unnamed-chunk-11-1.png) 

Metropolis - Hastings
========================================================
If we want to know where the modus is, we accept each $\lambda_n$ which satisfies

$$\frac{p(\lambda_n|y) }{ p(\lambda_o|y)}=\frac{p(x|\lambda_n)*p(\lambda_n) }{ p(x|\lambda_o)*p(\lambda_o)}>1$$

Metropolis - Hastings
========================================================
If we want to hit each value of $\lambda$ with probability $p(\lambda|y)$, we accept each $\lambda_n$

$$\frac{p(\lambda_n|y) }{ p(\lambda_o|y)}=\frac{p(x|\lambda_n)*p(\lambda_n) }{ p(x|\lambda_o)*p(\lambda_o)}>1$$

with probablity 1, and


$$\frac{p(\lambda_n|y) }{ p(\lambda_o|y)}=\frac{p(x|\lambda_n)*p(\lambda_n) }{ p(x|\lambda_o)*p(\lambda_o)}<1$$

with probability $\frac{p(\lambda_n|y) }{ p(\lambda_o|y)}$


Metropolis - Hastings
========================================================
And now switch to "histogram thinking"
![plot of chunk unnamed-chunk-12](pres-figure/unnamed-chunk-12-1.png) 

Metropolis - Hastings
========================================================
 - is more efficient than numerical integration
 
 - problem with camels
 
 - need to set up the jumps
 
 - can be slow if we have many variables

Metropolis - Hastings
========================================================
![plot of chunk unnamed-chunk-13](pres-figure/unnamed-chunk-13-1.png) 

Gibbs
========================================================
Lets imagine we have more variables than one. For example data from normal distribution and we want to estimate distribution of $\mu$ and $\sigma$.

$$p(\mu, \sigma|y)=\dfrac{p(y|\mu, \sigma)*p(\mu)*p(\sigma)}{p(y)}$$

Gibbs
========================================================
![plot of chunk unnamed-chunk-14](pres-figure/unnamed-chunk-14-1.png) 

Gibbs
========================================================
Sampling from univariate distribution is always quite simply.

If we knew $\sigma$ we could sample $\mu$ from $p(\mu|\sigma,y)$

If we knew $\mu$ awe could sample $\sigma$ from $p(\sigma|\mu,y)$


Gibbs
========================================================
Yes, if sampling from univariate is simple, we didn't have to do it by M-H in previous chapter. But M-H can be easily generalized to more dimension unlike direct sampling.

Gibbs
========================================================
![plot of chunk unnamed-chunk-15](pres-figure/unnamed-chunk-15-1.png) 

Gibbs
========================================================
![plot of chunk unnamed-chunk-16](pres-figure/unnamed-chunk-16-1.png) 

Gibbs
========================================================
![plot of chunk unnamed-chunk-17](pres-figure/unnamed-chunk-17-1.png) 

Gibbs
========================================================
![plot of chunk unnamed-chunk-18](pres-figure/unnamed-chunk-18-1.png) 
Gibbs
========================================================
![plot of chunk unnamed-chunk-19](pres-figure/unnamed-chunk-19-1.png) 
Gibbs
========================================================
![plot of chunk unnamed-chunk-20](pres-figure/unnamed-chunk-20-1.png) 

Gibbs
========================================================

Get initial values of $\mu$ and $\sigma$.

Take initial $\sigma$ and sample $\mu$ from $p(\mu|\sigma,y)$

Take sampled $\mu$ and sample $\sigma$ from $p(\sigma|\mu,y)$

Repeat


Gibbs
========================================================
 - for many parameters is more efficient than numerical integration and M-H

 - problem with camels
 
 - problem with cigars

Gibbs
========================================================
![plot of chunk unnamed-chunk-21](pres-figure/unnamed-chunk-21-1.png) 

MCMC
========================================================
![mcmc illustration](http://mbjoseph.github.io/images/metrop.gif)


BUGS language
========================================================
There is a plenty of environments that can do Gibbs sampling for you
 - WinBUGS
 - OpenBUGS [www.openbugs.net](http://openbugs.net/w/FrontPage)
 - **JAGS** [mcmc-jags.sourceforge.net/](http://mcmc-jags.sourceforge.net/)

... and all those use BUGS language

JAGS - preparing the data
========================================================

```r
y <- rnorm(5, 48.1, 6.1^2)
N <- 5

my.data <- list(y=y, N=N)
my.data
```

```
$y
[1]  32.38750 111.87828  34.43014  87.51529  37.74286

$N
[1] 5
```

JAGS - model specification
========================================================
We will fit this model to the data:
$y_i \sim Normal(\mu, \sigma)$

```
model
{
  # p(mu) 
  
  # p(sigma) 
  
  # p(y|lambda)
  
}
```

JAGS - model specification
========================================================
We will fit this model to the data:
$y_i \sim Normal(\mu, \sigma)$

```
model
{
  # p(mu) ... mu prior
  
  # p(sigma) ... sigma prior
  
  # p(y|lambda) ... likelihood
  
}
```

JAGS - model specification
========================================================
We will fit this model to the data:
$y_i \sim Normal(\mu, \sigma)$

```
model
{
  # priors
    mu ~ dnorm (0, 0.001)
    sigma ~ dunif(0, 100)
  # likelihood
    for(i in 1:N)
    {
      y[i] ~ dnorm(mu, sigma)    
    }
}
```

JAGS - model specification
========================================================
We will dump the model to a file using ```cat("", file="")```


```r
cat("
model
{
  # priors
    mu ~ dnorm (0, 0.001)
    sigma ~ dunif(0, 100)
  # likelihood
    for(i in 1:N)
    {
      y[i] ~ dnorm(mu, sigma)    
    }
}
", file="my_model.txt")
```

JAGS - model specification
========================================================

```r
library(R2jags)

fitted.model <- jags(data=my.data,  model.file="my_model.txt", parameters.to.save=c("mu", "sigma"), n.chains=1, n.iter=300, n.burnin=100)
```

```
Compiling model graph
   Resolving undeclared variables
   Allocating nodes
   Graph Size: 11

Initializing model
```

JAGS - model specification
========================================================
![plot of chunk unnamed-chunk-25](pres-figure/unnamed-chunk-25-1.png) 


STAN sampler
=======================================================
 - [mc-stan.org](mc-stan.org)
 - uses addaptive combination of Gibbs ant Metropolis - Hastings
 sampler
 
 - may be more efficient for complicated posteriors (cigars, camels)
 
 - is more difficult to command


INLA
========================================================
dodelat
