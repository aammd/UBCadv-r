---
Title: "Functionals"
Author: "Kim"
Date: '2014-09-23'
---

***
**DISCLAIMER that I did not put a lot of effort into doing many of the exercises, and skipped ones that I felt had no motivation for doing.**

### Chapter Notes

The complement to a closure is a functional, a function that takes a function as an input and returns a vector as output

the three most frequently used are lapply(), apply(), and tapply(). All three take a function as input (among other things) and return a vector as output

lapply() takes a function, applies it to each element in a list, and returns the results in the form of a list

R is faster at filling in existing spots than copying existing things to a newer, longer thing

sapply() and vapply(), variants of lapply() that produce vectors, matrices, and arrays as output, instead of lists

Map, a variant of lapply(), where all arguments can vary - i.e. you can have more than one argument to the function, 
such as 2 datasets.  Note that the order of arguments is a little different: function is the first argument for Map() and 
the second for lapply().  Map is useful whenever you have two (or more) lists (or data frames) that you need to process in parallel

Map is the same as mapply but with simplify=FALSE as the default, and mapply has other things that make it more confusing

apply() works on matrices and arrays

Reduce(), a powerful tool for extending two-argument functions, and Filter(), a member of an important class of 
functionals that work with predicates, functions that return a single TRUE or FALSE

Reduce() reduces a vector, x, to a single value by recursively calling a function, f, two arguments at a time. It combines 
the first two elements with f, then combines the result of that call with the third element, and so on. Calling Reduce(f, 1:3) 
is equivalent to f(f(1, 2), 3). Reduce is also known as fold, because it folds together adjacent elements in the list

Reduce() is an elegant way of extending a function that works with two inputs into a function that can deal with any number of inputs

A predicate is a function that returns a single TRUE or FALSE, like is.character

***
### Exercises 1

1.  Why are the following two invocations of `lapply()` equivalent?

    ```{r, eval = FALSE}
    trims <- c(0, 0.1, 0.2, 0.5)
    x <- rcauchy(100)
    
    lapply(trims, function(trim) mean(x, trim = trim))
    lapply(trims, mean, x = x)
    ```
    **because both apply the mean function to the x dataset using a trim value of those specified in 'trims'.**

1.  The function below scales a vector so it falls in the range [0, 1]. How
    would you apply it to every column of a data frame? How would you apply it 
    to every numeric column in a data frame?

    ```{r}
    scale01 <- function(x) {
      rng <- range(x, na.rm = TRUE)
      (x - rng[1]) / (rng[2] - rng[1])
    }
    ```
    
    ```
    **lapply(data, scale01, MARGIN=2)
    lapply(as.numeric(data), scale01, MARGIN=2) <- not correct in terms of just doing it to the numeric columns**
    ```

1.  Use both for loops and `lapply()` to fit linear models to the
    `mtcars` using the formulas stored in this list:

    ```{r}
    formulas <- list(
      mpg ~ disp,
      mpg ~ I(1 / disp),
      mpg ~ disp + wt,
      mpg ~ I(1 / disp) + wt
    )
    ```
    **Didn't attempt**

1.  Fit the model `mpg ~ disp` to each of the bootstrap replicates of `mtcars` 
    in the list below by using a for loop and `lapply()`. Can you do it 
    without an anonymous function?

    ```{r}
    bootstraps <- lapply(1:10, function(i) {
      rows <- sample(1:nrow(mtcars), rep = TRUE)
      mtcars[rows, ]
    })
    ```
    **Didn't attempt**

1.  For each model in the previous two exercises, extract $R^2$ using the
    function below.

    ```{r}
    rsq <- function(mod) summary(mod)$r.squared
    ```
    **Didn't attempt since I didn't do the previous ones**



### Exercises 2

1.  Use `vapply()` to:
    
    a) Compute the standard deviation of every column in a numeric data frame.
    
    **`vapply(dat, sd, MARGIN=2)`**
    
    a) Compute the standard deviation of every numeric column in a mixed data
       frame. (Hint: you'll need to use `vapply()` twice.)
       
    **`vapply(vapply(dat, is.numeric(), MARGIN=2), sd, MARGIN=2)`**

1.  Why is using `sapply()` to get the `class()` of each element in 
    a data frame dangerous?
    
    **because sapply() silently returns a list**

1.  The following code simulates the performance of a t-test for non-normal 
    data. Use `sapply()` and an anonymous function to extract the p-value from 
    every trial.

    ```{r}
    trials <- replicate(
      100, 
      t.test(rpois(10, 10), rpois(7, 10)),
      simplify = FALSE
    )
    ```
    **not able to test out if this works or not since my R is currently occupied running things...
    `sapply(trials, function(x) return(trials$pvalue)`**
    
    Extra challenge: get rid of the anonymous function by using `[[` directly.

1.  What does `replicate()` do? What sort of for loop does it eliminate? Why 
    do its arguments differ from `lapply()` and friends?
    
    **this wasn't even discussed up to this point in the chapter, these exercises are pretty frustratingly designed
       replicate performs the same function/command over the specified number of times
       I suppose it eliminates the need for a while or do?
       because you tell it how many times to do a given function and that is the first parameter?**

1.  Implement a version of `lapply()` that supplies `FUN` with both the name 
    and the value of each component.
    
    **I don't know what this is asking?**

1.  Implement a combination of `Map()` and `vapply()` to create an `lapply()`
    variant that iterates in parallel over all of its inputs and stores its 
    outputs in a vector (or a matrix). What arguments should the function 
    take?
    
    **Didn't attempt**


1.  Implement `mcsapply()`, a multicore version of `sapply()`. Can you
    implement `mcvapply()`, a parallel version of `vapply()`? Why or why not?

    **Didn't attempt**


### Exercises 3

1.  How does `apply()` arrange the output? Read the documentation and perform 
    some experiments.
    
    **I think it varies depending on what you give it, again can't test while my R is running other stuff :/**

1.  There's no equivalent to `split()` + `vapply()`. Should there be? When 
    would it be useful? Implement one yourself.
    
    **to split/subset types within a vector I suppose, why not just do something like vapply(dat, split(), FUN.VALUE=...)**

1.  Implement a pure R version of `split()`. (Hint: use `unique()` and 
    subsetting.) Can you do it without a for loop?

    **Didn't attempt**

1.  What other types of input and output are missing? Brainstorm before you 
    look up some answers in the [plyr paper](http://www.jstatsoft.org/v40/i01/).

    **Didn't attempt**

### Exercises 4

1.  Why isn't `is.na()` a predicate function? What base R function is closest
    to being a predicate version of `is.na()`?

	**I would think it is a predicate from Hadley's description? But I suppose is.NULL is closest?**
	
1.  Use `Filter()` and `vapply()` to create a function that applies a summary 
    statistic to every numeric column in a data frame.

    **Didn't attempt**

1.  What's the relationship between `which()` and `Position()`? What's
    the relationship between `where()` and `Filter()`?
    
    **I don't know, if this chapter was just meant to teach me a large set of existing functions, I really think it should have been done differently.**

1.  Implement `Any()`, a function that takes a list and a predicate function, 
    and returns `TRUE` if the predicate function returns `TRUE` for any of 
    the inputs. Implement `All()` similarly.

    **Didn't attempt, I wish he gave an easy example for why I might have reason to do these things so I'd actually be motivated for the exercises!**

1.  Implement the `span()` function from Haskell: given a list `x` and a 
    predicate function `f`, `span` returns the location of the longest 
    sequential run of elements where the predicate is true. (Hint: you 
    might find `rle()` helpful.)

    **Didn't attempt**

### Exercises 5

1.  Implement `arg_max()`. It should take a function and a vector of inputs, 
    and return the elements of the input where the function returns the highest 
    value. For example, `arg_max(-10:5, function(x) x ^ 2)` should return -10.
    `arg_max(-5:5, function(x) x ^ 2)` should return `c(-5, 5)`.
    Also implement the matching `arg_min()` function.

    **Didn't attempt**

1.  Challenge: read about the 
    [fixed point algorithm](http://mitpress.mit.edu/sicp/full-text/book/book-Z-H-12.html#%_sec_1.3). 
    Complete the exercises using R.
   
    **Didn't attempt, I'm so done with this chapter.**
 
    
### Exercises 6

1.  Implement `smaller` and `larger` functions that, given two inputs, return 
    either the smaller or the larger value. Implement `na.rm = TRUE`: what 
    should the identity be? (Hint: 
    `smaller(x, smaller(NA, NA, na.rm = TRUE), na.rm = TRUE)` must be `x`, so 
    `smaller(NA, NA, na.rm = TRUE)` must be bigger than any other value of x.) 
    Use `smaller` and `larger` to implement equivalents of `min()`, `max()`,
    `pmin()`, `pmax()`, and new functions `row_min()` and `row_max()`.

1.  Create a table that has _and_, _or_, _add_, _multiply_, _smaller_, and 
    _larger_ in the columns and _binary operator_, _reducing variant_, 
    _vectorised variant_, and _array variants_ in the rows.

    a) Fill in the cells with the names of base R functions that perform each of
       the roles.

    a) Compare the names and arguments of the existing R functions. How
       consistent are they? How could you improve them?

    a) Complete the matrix by implementing any missing functions.

1.  How does `paste()` fit into this structure? What is the scalar binary 
    function that underlies `paste()`? What are the `sep` and `collapse` 
    arguments to `paste()` equivalent to? Are there any `paste` variants 
    that don't have existing R implementations?

