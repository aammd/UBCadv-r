---
Title: "Functional Programming"
Author: "Kim"
Date: '2014-08-19'
---

***

### Discussion Notes


***
### Exercises 1

1. Given a function, like `"mean"`, `match.fun()` lets you find a function. 
   Given a function, can you find its name? Why doesn't that make sense in R?

1. Use `lapply()` and an anonymous function to find the coefficient of 
   variation (the standard deviation divided by the mean) for all columns in 
   the `mtcars` dataset.

1. Use `integrate()` and an anonymous function to find the area under the 
   curve for the following functions. 
   Use [Wolfram Alpha](http://www.wolframalpha.com/) to check your answers.

    1. `y = x ^ 2 - x`, x in [0, 10]
    1. `y = sin(x) + cos(x)`, x in [-$\pi$, $\pi$]
    1. `y = exp(x) / x`, x in [10, 20]

1. A good rule of thumb is that an anonymous function should fit on one line 
   and shouldn't need to use `{}`. Review your code. Where could you have 
   used an anonymous function instead of a named function? Where should you 
   have used a named function instead of an anonymous function?


### Exercises 2

1.  Why are functions created by other functions called closures? 

1.  What does the following statistical function do? What would be a better 
    name for it? (The existing name is a bit of a hint.)

    ```{r}
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

    ```{r, eval = FALSE}
    m1 <- moment(1)
    m2 <- moment(2)

    x <- runif(100)
    stopifnot(all.equal(m1(x), 0))
    stopifnot(all.equal(m2(x), var(x) * 99 / 100))
    ```

1.  Create a function `pick()` that takes an index, `i`, as an argument and 
    returns a function with an argument `x` that subsets `x` with `i`.

    ```{r, eval = FALSE}
    lapply(mtcars, pick(5))
    # should do the same as this
    lapply(mtcars, function(x) x[[5]])
    ```
    
    
### Exercises 4

1.  Implement a summary function that works like `base::summary()`, but uses a 
    list of functions. Modify the function so it returns a closure, making it 
    possible to use it as a function factory.

1. Which of the following commands is equivalent to `with(x, f(z))`?

    (a) `x$f(x$z)`.
    (b) `f(x$z)`.
    (c) `x$f(z)`.
    (d) `f(z)`.
    (e) It depends.
    
    
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