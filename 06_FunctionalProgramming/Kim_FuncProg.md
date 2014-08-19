---
Title: "Functional Programming"
Author: "Kim"
Date: '2014-08-19'
---

***

### Discussion Notes

You can do anything with functions that you can do with any other object.

Anonymous functions - functions can be made without giving them a name.

Closure = function written by another function.

DRY principle - do not repeat yourself in code. i.e. if you have to come back and change 
something, make sure it's only in one place.

lapply() is called a functional, because it takes a function as an argument.

Storing functions in lists and then applying them, e.g. lapply(), is useful

<<- in fact is not always a global assign as I had been led to believe. If the object already 
exists up an environment, it will just reassign to that value

***
### Exercises 1

1. Given a function, like `"mean"`, `match.fun()` lets you find a function. 
   Given a function, can you find its name? Why doesn't that make sense in R?
   
	**Not exactly sure I know what Hadley means here, but if a function has a name, then that is what
    you use to find it. If it doen't have a name and is anonymous, then it only exists at that instance 
    so you wouldn't be able to find it again?**

1. Use `lapply()` and an anonymous function to find the coefficient of 
   variation (the standard deviation divided by the mean) for all columns in 
   the `mtcars` dataset.
   
   **`lapply(mtcars, FUN=cv)` this is without an anonymous function though...**

1. Use `integrate()` and an anonymous function to find the area under the 
   curve for the following functions. 
   Use [Wolfram Alpha](http://www.wolframalpha.com/) to check your answers.

    1. `y = x ^ 2 - x`, x in [0, 10]
    
    	**`integrate(function(x) x ^ 2 - x, 0, 10)` 
    	283.3333 with absolute error < 3.1e-12**    	
    1. `y = sin(x) + cos(x)`, x in [-$\pi$, $\pi$]
    
    	**`integrate(function(x) sin(x) + cos(x), -pi, pi)` 
    	5.231803e-16 with absolute error < 6.3e-14**
    1. `y = exp(x) / x`, x in [10, 20]
    
    	**`integrate(function(x) exp(x) / x, 10, 20)` 
    	25613160 with absolute error < 2.8e-07**

1. A good rule of thumb is that an anonymous function should fit on one line 
   and shouldn't need to use `{}`. Review your code. Where could you have 
   used an anonymous function instead of a named function? Where should you 
   have used a named function instead of an anonymous function?
   
   **probably in plenty of places...**


### Exercises 2

1.  Why are functions created by other functions called closures? 

	**Because they enclose the environment of the parent function and can access all its variables**
	
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
    **Makes a function that takes the log of a number unless the first number is zero in which case 
    it does 1 over lambda times x. I don't know what this is mathematically nor what it should
     be called.**

1.  What does `approxfun()` do? What does it return?

	**Just used the help page, not sure what Hadley wanted? Return a list of points which linearly 
	interpolate given data points, or a function performing the linear (or constant) interpolation.**

1.  What does `ecdf()` do? What does it return?

	**Just used the help page, not sure what Hadley wanted? Compute an empirical cumulative 
	distribution function, with several methods for plotting, printing and computing with 
	such an “ecdf” object.**

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
    **I haven't tried this one yet.**

1.  Create a function `pick()` that takes an index, `i`, as an argument and 
    returns a function with an argument `x` that subsets `x` with `i`.

    ```{r, eval = FALSE}
    lapply(mtcars, pick(5))
    # should do the same as this
    lapply(mtcars, function(x) x[[5]])
    ```
    ```
    pick <- function(i){
    	function(x) x[[i]]
    }
    ```
    
### Exercises 3

1.  Implement a summary function that works like `base::summary()`, but uses a 
    list of functions. Modify the function so it returns a closure, making it 
    possible to use it as a function factory.

1. Which of the following commands is equivalent to `with(x, f(z))`?

    (a) `x$f(x$z)`.
    (b) `f(x$z)`.
    (c) `x$f(z)`.
    (d) `f(z)`.
    (e) It depends.
    
    
### Exercises 4

1.  Instead of creating individual functions (e.g., `midpoint()`, 
      `trapezoid()`, `simpson()`, etc.), we could store them in a list. If we 
    did that, how would that change the code? Can you create the list of 
    functions from a list of coefficients for the Newton-Cotes formulae?

1.  The trade-off between integration rules is that more complex rules are 
    slower to compute, but need fewer pieces. For `sin()` in the range 
    [0, $\pi$], determine the number of pieces needed so that each rule will 
    be equally accurate. Illustrate your results with a graph. How do they
    change for different functions? `sin(1 / x^2)` is particularly challenging.