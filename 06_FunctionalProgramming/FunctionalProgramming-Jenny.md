# Jenny's reading of Functional Programming
Jenny Bryan  
`r format(Sys.time(), '%d %B, %Y')`  



Week 07 2014-08-21 we read [Functional Programming](http://adv-r.had.co.nz/Functional-programming.html). Source is found [here on github](https://github.com/hadley/adv-r/blob/master/Functional-programming.rmd).

This chapter uses `pryr`, so I load it:


```r
library(pryr)
```

## Motivation

I should remember this trick for making sure I get a data.frame back from `lapply()`:


```r
#df is a data.frame ...
df[] <- lapply(df, some_fun)
```

More good demos in here:

  * writing a function that creates another function to correct missing values
  * write a meta-function that employs a list of functions and `lapply()` to apply all the functions to `x`

## Anonymous functions

### Exercises

1. Given a function, like `"mean"`, `match.fun()` lets you find a function. 
   Given a function, can you find its name? Why doesn't that make sense in R?
   
   *I'm not really clear on this question. Maybe because I'm in a huge hurry. Skipping.*

1. Use `lapply()` and an anonymous function to find the coefficient of 
   variation (the standard deviation divided by the mean) for all columns in 
   the `mtcars` dataset.
   
   

```r
unlist(lapply(mtcars, function(x) sd(x) / mean(x)))
```

```
##    mpg    cyl   disp     hp   drat     wt   qsec     vs     am   gear 
## 0.3000 0.2886 0.5372 0.4674 0.1487 0.3041 0.1001 1.1520 1.2283 0.2001 
##   carb 
## 0.5743
```

1. Use `integrate()` and an anonymous function to find the area under the 
   curve for the following functions. 
   Use [Wolfram Alpha](http://www.wolframalpha.com/) to check your answers.

    1. `y = x ^ 2 - x`, x in [0, 10]
    1. `y = sin(x) + cos(x)`, x in [-$\pi$, $\pi$]
    1. `y = exp(x) / x`, x in [10, 20]
    

```r
integrate(function(x) x ^ 2 - x, lower = 0, upper = 10)
## 283.3 with absolute error < 3.1e-12
integrate(function(x) sin(x) + cos(x), lower = -pi, upper = pi)
## 5.232e-16 with absolute error < 6.3e-14
integrate(function(x) exp(x) / x, lower = 10, upper = 20)
## 25613160 with absolute error < 2.8e-07
```

1. A good rule of thumb is that an anonymous function should fit on one line 
   and shouldn't need to use `{}`. Review your code. Where could you have 
   used an anonymous function instead of a named function? Where should you 
   have used a named function instead of an anonymous function?
   
*I don't feel like I've done anything meaty enough to merit such reflection. Have I missed something?*

## Closures

A closure is a function that was written by another function.

Typos: A function factory is a factor__Y__ for making new functions.... describe the DESIRED actions. There was also a link MLE earlier that didn't make sense (ie linked to Mathemtaical functionals).

Predicting and checking what happens with these variations on `new_counter()`.


```r
i <- 0
new_counter2 <- function() {
  i <<- i + 1
  i
}
```
I think the first call to `new_counter2()` will cause `i` in my interactive workspace to increment to 1. I guess it will also print 1, because it will not find `i` in its own runtime environment and will look to parents. But I also feel like I must be wrong, because that behavior suggests the original closure approach is not necessary. There's some chance that subsequent calls to `new_counter2()` will always think `i` is zero to start with, since that was true at the time of creation. Let's find out.


```r
i
## [1] 0
new_counter2()
## [1] 1
i
## [1] 1
new_counter2()
## [1] 2
i
## [1] 2
```

I was wrong about the state of `i` at time of `new_counter2()` creation being a problem. Doesn't this mean the original closure approach was overkill for the problem of "a counter that records how many times a function has been called"?

Let's try the next variant.


```r
new_counter3 <- function() {
  i <- 0
  function() {
    i <- i + 1
    i
  }
}
```

I bet this always returns 1 and `i` in the interactive environment is not changing.


```r
i # leftover from above
## [1] 2
new_counter3()()
## [1] 1
i
## [1] 2
new_counter3()()
## [1] 1
```

Yep. I was a bit surprised I needed to use two sets of parenthesis, but that does actually make sense.

### Exercises

1.  Why are functions created by other functions called closures? 

1.  What does the following statistical function do? What would be a better 
    name for it? (The existing name is a bit of a hint.)

    
    ```r
    bc <- function(lambda) {
      if (lambda == 0) {
        function(x) log(x)
      } else {
        function(x) (x ^ lambda - 1) / lambda
      }
    }
    ```

1.  What does `approxfun()` do? What does it return?

1.  What does `ecdf()` do? What does it return?

1.  Create a function that creates functions that compute the ith 
    [central moment](http://en.wikipedia.org/wiki/Central_moment) of a numeric 
    vector. You can test it by running the following code:

    
    ```r
    m1 <- moment(1)
    m2 <- moment(2)
    
    x <- runif(100)
    stopifnot(all.equal(m1(x), 0))
    stopifnot(all.equal(m2(x), var(x) * 99 / 100))
    ```

1.  Create a function `pick()` that takes an index, `i`, as an argument and 
    returns a function with an argument `x` that subsets `x` with `i`.

    
    ```r
    lapply(mtcars, pick(5))
    # should do the same as this
    lapply(mtcars, function(x) x[[5]])
    ```

## Lists of functions

### Exercises

1.  Implement a summary function that works like `base::summary()`, but uses a 
    list of functions. Modify the function so it returns a closure, making it 
    possible to use it as a function factory.

1. Which of the following commands is equivalent to `with(x, f(z))`?

    (a) `x$f(x$z)`.
    (b) `f(x$z)`.
    (c) `x$f(z)`.
    (d) `f(z)`.
    (e) It depends.

## Case study: numerical integration

### Exercises

1.  Instead of creating individual functions (e.g., `midpoint()`, 
      `trapezoid()`, `simpson()`, etc.), we could store them in a list. If we 
    did that, how would that change the code? Can you create the list of 
    functions from a list of coefficients for the Newton-Cotes formulae?

1.  The trade-off between integration rules is that more complex rules are 
    slower to compute, but need fewer pieces. For `sin()` in the range 
    [0, $\pi$], determine the number of pieces needed so that each rule will 
    be equally accurate. Illustrate your results with a graph. How do they
    change for different functions? `sin(1 / x^2)` is particularly challenging.
