---
title: "Exercises for 'Functional Programming"
author: "Davor Cubranic"
date: "21 August, 2014"
output:
  html_document:
    toc: no
    keep_md: TRUE
---

1. Errors:
   
   ```r
   df$c[df$c == -98] <- NA
   df$f[df$g == -99] <- NA
   ```


Anonymous Functions
-------

1. Given a function, like "mean", match.fun() lets you find a
   function. Given a function, can you find its name? Why doesn’t that
   make sense in R?
   
   No, because name binding is a property of an environment.

2. Use lapply() and an anonymous function to find the coefficient of
   variation (the standard deviation divided by the mean) for all
   columns in the mtcars dataset.

    
    ```r
    lapply(mtcars, function(x) sd(x)/mean(x))
    ```
    
    ```
    ## $mpg
    ## [1] 0.3
    ## 
    ## $cyl
    ## [1] 0.2886
    ## 
    ## $disp
    ## [1] 0.5372
    ## 
    ## $hp
    ## [1] 0.4674
    ## 
    ## $drat
    ## [1] 0.1487
    ## 
    ## $wt
    ## [1] 0.3041
    ## 
    ## $qsec
    ## [1] 0.1001
    ## 
    ## $vs
    ## [1] 1.152
    ## 
    ## $am
    ## [1] 1.228
    ## 
    ## $gear
    ## [1] 0.2001
    ## 
    ## $carb
    ## [1] 0.5743
    ```

3. Use integrate() and an anonymous function to find the area under
   the curve for the following functions. Use Wolfram Alpha to check
   your answers.
   
   $y = x ^ 2 - x, x \in [0, 10]$
   
   ```r
   integrate(function(x) x^2 - x, 0, 10)
   ```
   
   ```
   ## 283.3 with absolute error < 3.1e-12
   ```
   
   $y = sin(x) + cos(x), x \in [-π, π]$
   
   ```r
   integrate(function(x) sin(x) + cos(x), -pi, pi)
   ```
   
   ```
   ## 5.232e-16 with absolute error < 6.3e-14
   ```
   
   $y = exp(x) / x, x \in [10, 20]$
   
   ```r
   integrate(function(x) exp(x) / x, 10, 20)
   ```
   
   ```
   ## 25613160 with absolute error < 2.8e-07
   ```
  
Closures
-----

1. Why are functions created by other functions called closures?

   Because they enclose the environment of their parent function.

2. What does the following statistical function do? What would be a
   better name for it? (The existing name is a bit of a hint.)

    
    ```r
    bc <- function(lambda) {
      if (lambda == 0) {
        function(x) log(x)
      } else {
        function(x) (x ^ lambda - 1) / lambda
      }
    }
    ```
    
   It's a function factory that returns a different function depending
   on the value of the `lambda` parameter, corresponding to the
   following formula:

   $$ f(x) = \left\{
        \begin{array}{lr}
          \log(x) & : \lambda = 0 \\\\
          \frac{x ^ \lambda - 1}{\lambda} & : \lambda \ne 0
        \end{array}
      \right.
   $$

3. What does `approxfun()` do? What does it return?

   It's a function factory that returns a function performing (linear
   or constant) interpolation of the data points given to the factory.

4. What does ecdf() do? What does it return?

   It's a function factory that returns a function that calculates the
   value of empirical cumulative distribution function of the data
   points given to the factory.

5. Create a function that creates functions that compute the ith
   central moment of a numeric vector. You can test it by running the
   following code:
   
   
   ```r
   moment <- function(n) {
       function(x) mean((x - mean(x))^n)
   }
   m1 <- moment(1)
   m2 <- moment(2)
   
   x <- runif(100)
   stopifnot(all.equal(m1(x), 0))
   stopifnot(all.equal(m2(x), var(x) * 99 / 100))
   ```
   
6. Create a function `pick()` that takes an index, i, as an argument
   and returns a function with an argument x that subsets x with i.

   
   ```r
   pick <- function(i) {
       function(x) x[[i]]
   }
   
   stopifnot(all.equal(lapply(mtcars, pick(5)),
                       lapply(mtcars, function(x) x[[5]])))
   ```

Lists of Functions
---------

1. Implement a summary function that works like `base::summary()`, but
   uses a list of functions. Modify the function so it returns a
   closure, making it possible to use it as a function factory.

   
   ```r
   my_summary <- function(x) {
       fns <- list(min, function(x, ...) quantile(x, probs = .25, ...),
                   median, mean,
                   function(x, ...) quantile(x, probs = .75, ...), max)
       summs <- sapply(fns, function(f) f(x, na.rm = TRUE))
       names(summs) <- c('Min.', '1st Qu.', 'Median', 'Mean', '3rd Qu.', 'Max.')
       summs
   }
   my_summary(x)
   ```
   
   ```
   ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
   ## 0.01081 0.32606 0.49514 0.49869 0.69644 0.99983
   ```

2. Which of the following commands is equivalent to `with(x, f(z))`?
   
   Assuming `z` is in `x`, it's (b): `f(x$z)`. If `f` is also part of
   `x`, then it's (a): `x$f(x$z)`. But if neither is in `x`, then
   it's equivalent to (d): `f(z)`.


Numerical integration
----



1. Instead of creating individual functions (e.g., `midpoint()`,
   `trapezoid()`, `simpson()`, etc.), we could store them in a list.
   If we did that, how would that change the code? Can you create the
   list of functions from a list of coefficients for the Newton-Cotes
   formulae?

   I must have something wrong because the new rules don't operate the
   same as the old ones:
   
   ```r
   nc_args <- list(midpoint=list(coef = c(0, 1), open=TRUE),
   trapezoid=list(coef=c(1,1)), simpson=list(coef=c(1, 4, 1)),
   boole=list(coef=c(7, 32, 12, 32, 7)), milne=list(coef=c(2, -1, 2),
   open = TRUE))
   rules <- (lapply(nc_args, function(args) do.call('newton_cotes',
   args)))
   
   stopifnot(all.equal(composite(sin, 0, pi, n=10, rule = midpoint),
                       composite(sin, 0, pi, n=10, rule = rules$midpoint)))
   ```
   
   ```
   ## Error: all.equal(composite(sin, 0, pi, n = 10, rule = midpoint),
   ## composite(sin, .... is not TRUE
   ```
   
   ```r
   stopifnot(all.equal(composite(sin, 0, pi, n=10, rule = trapezoid),
                       composite(sin, 0, pi, n=10, rule = rules$trapezoid)))
   ```
   
   ```
   ## Error: all.equal(composite(sin, 0, pi, n = 10, rule = trapezoid),
   ## composite(sin, .... is not TRUE
   ```
   
   ```r
   stopifnot(all.equal(composite(sin, 0, pi, n=10, rule = simpson),
                       composite(sin, 0, pi, n=10, rule = rules$simpson)))
   ```
   
   ```
   ## Error: all.equal(composite(sin, 0, pi, n = 10, rule = simpson),
   ## composite(sin, .... is not TRUE
   ```
   
   ```r
   stopifnot(all.equal(composite(sin, 0, pi, n=10, rule = boole),
                       composite(sin, 0, pi, n=10, rule = rules$boole)))
   ```
   
   ```
   ## Error: all.equal(composite(sin, 0, pi, n = 10, rule = boole),
   ## composite(sin, .... is not TRUE
   ```
   
   ```r
   stopifnot(all.equal(composite(sin, 0, pi, n=10, rule = milne),
                       composite(sin, 0, pi, n=10, rule = rules$milne)))
   ```

2. The trade-off between integration rules is that more complex rules
   are slower to compute, but need fewer pieces. For sin() in the
   range [0, π], determine the number of pieces needed so that each
   rule will be equally accurate. Illustrate your results with a
   graph.
   
   As can be seen from the table below, midpoint, trapezoid, and milne
   rules take over 10,000 iterations to get to where simpson and boole
   were at 1,000:
   
   
   ```r
   results <- lapply(list(midpoint = midpoint, trapezoid = trapezoid, simpson = simpson, boole = boole, milne = milne),
                     function(f) sapply(1:5,
                                        function(i) abs(2-composite(sin, 0, pi, n=10^i, rule=f))))
   results <- cbind(n = 10^(1:5), as.data.frame(results))
   results
   ```
   
   ```
   ##       n  midpoint trapezoid   simpson     boole     milne
   ## 1 1e+01 8.248e-03 1.648e-02 6.784e-06 9.967e-10 6.171e-03
   ## 2 1e+02 8.225e-05 1.645e-04 6.765e-10 1.332e-15 6.169e-05
   ## 3 1e+03 8.225e-07 1.645e-06 6.617e-14 2.220e-15 6.169e-07
   ## 4 1e+04 8.225e-09 1.645e-08 8.882e-16 8.882e-16 6.169e-09
   ## 5 1e+05 8.226e-11 1.645e-10 3.997e-15 3.997e-15 6.168e-11
   ```

   Viewed graphically, we see that the error decreases linearly in the
   number of iterations. Again, simpson and boole improve much quicker
   than the other three, essentially reaching the limit of machine
   precision at 1,000 and 100 iterations, respectively:
   
   
   ```r
   library(reshape2)
   results <- melt(results, id='n', 
                   variable.name = 'rule', value.name = 'error')
   
   library(ggplot2, quietly = TRUE)
   ggplot(results, aes(n, error)) + geom_line() + geom_point() + facet_grid(.~rule) + scale_x_log10() + scale_y_log10()
   ```
   
   ![plot of chunk unnamed-chunk-13](figure/unnamed-chunk-13.png) 

   Q: How do they change for different functions? sin(1 / x^2) is
   particularly challenging.

   Solution with `integrate` and Wolfram Alpha:
   
   ```r
   int_sol <- integrate(function(x) sin(1/x^2), 0, pi, subdivisions = 8570L)
   int_sol
   ```
   
   ```
   ## 0.9351 with absolute error < 9.4e-05
   ```
   
   ```r
   ### Wolfram Alpha: 0.935113
   ```

   To avoid producing NaN's at $x=0$, I'll use the interval
   $ [2.2204 &times; 10<sup>-16</sup>, \pi] $:
   
   ```r
   results <- lapply(list(midpoint = midpoint, trapezoid = trapezoid, simpson = simpson, boole = boole, milne = milne),
                     function(f) sapply(1:5,
                                        function(i) abs(int_sol$value-composite(function(x) sin(1/x^2), .Machine$double.eps, pi, n=10^i, rule=f))))
   results <- cbind(n = 10^(1:5), as.data.frame(results))
   results
   ```
   
   ```
   ##       n  midpoint trapezoid   simpson     boole     milne
   ## 1 1e+01 1.370e-01 3.893e-02 7.833e-02 1.553e-01 0.0643529
   ## 2 1e+02 4.033e-02 1.110e-01 1.012e-02 1.907e-02 0.0346863
   ## 3 1e+03 1.652e-02 1.165e-02 7.131e-03 3.218e-03 0.0050136
   ## 4 1e+04 1.766e-03 8.021e-04 1.445e-03 4.459e-05 0.0020420
   ## 5 1e+05 6.108e-05 8.012e-05 6.743e-05 2.802e-05 0.0001236
   ```
   This time, all algorithms are similar and within the reported
   absolute error of `integrate` (9.4171 &times; 10<sup>-5</sup>) after 10,000
   iterations, except for boole, which reaches it after 10,000
   iterations.
