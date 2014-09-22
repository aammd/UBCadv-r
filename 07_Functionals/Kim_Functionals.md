---
Title: "Functionals"
Author: "Kim"
Date: '2014-09-23'
---

***

### Discussion Notes


***
### Exercises 1

1.  Why are the following two invocations of `lapply()` equivalent?

    ```{r, eval = FALSE}
    trims <- c(0, 0.1, 0.2, 0.5)
    x <- rcauchy(100)
    
    lapply(trims, function(trim) mean(x, trim = trim))
    lapply(trims, mean, x = x)
    ```

1.  The function below scales a vector so it falls in the range [0, 1]. How
    would you apply it to every column of a data frame? How would you apply it 
    to every numeric column in a data frame?

    ```{r}
    scale01 <- function(x) {
      rng <- range(x, na.rm = TRUE)
      (x - rng[1]) / (rng[2] - rng[1])
    }
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

1.  Fit the model `mpg ~ disp` to each of the bootstrap replicates of `mtcars` 
    in the list below by using a for loop and `lapply()`. Can you do it 
    without an anonymous function?

    ```{r}
    bootstraps <- lapply(1:10, function(i) {
      rows <- sample(1:nrow(mtcars), rep = TRUE)
      mtcars[rows, ]
    })
    ```

1.  For each model in the previous two exercises, extract $R^2$ using the
    function below.

    ```{r}
    rsq <- function(mod) summary(mod)$r.squared
    ```



### Exercises 2

1.  Use `vapply()` to:
    
    a) Compute the standard deviation of every column in a numeric data frame.
    
    a) Compute the standard deviation of every numeric column in a mixed data
       frame. (Hint: you'll need to use `vapply()` twice.)

1.  Why is using `sapply()` to get the `class()` of each element in 
    a data frame dangerous?

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
    
    Extra challenge: get rid of the anonymous function by using `[[` directly.

1.  What does `replicate()` do? What sort of for loop does it eliminate? Why 
    do its arguments differ from `lapply()` and friends?

1.  Implement a version of `lapply()` that supplies `FUN` with both the name 
    and the value of each component.

1.  Implement a combination of `Map()` and `vapply()` to create an `lapply()`
    variant that iterates in parallel over all of its inputs and stores its 
    outputs in a vector (or a matrix). What arguments should the function 
    take?

1.  Implement `mcsapply()`, a multicore version of `sapply()`. Can you
    implement `mcvapply()`, a parallel version of `vapply()`? Why or why not?


### Exercises 3

1.  How does `apply()` arrange the output? Read the documentation and perform 
    some experiments.

1.  There's no equivalent to `split()` + `vapply()`. Should there be? When 
    would it be useful? Implement one yourself.

1.  Implement a pure R version of `split()`. (Hint: use `unique()` and 
    subsetting.) Can you do it without a for loop?

1.  What other types of input and output are missing? Brainstorm before you 
    look up some answers in the [plyr paper](http://www.jstatsoft.org/v40/i01/).

### Exercises 4

1.  Why isn't `is.na()` a predicate function? What base R function is closest
    to being a predicate version of `is.na()`?

1.  Use `Filter()` and `vapply()` to create a function that applies a summary 
    statistic to every numeric column in a data frame.

1.  What's the relationship between `which()` and `Position()`? What's
    the relationship between `where()` and `Filter()`?

1.  Implement `Any()`, a function that takes a list and a predicate function, 
    and returns `TRUE` if the predicate function returns `TRUE` for any of 
    the inputs. Implement `All()` similarly.

1.  Implement the `span()` function from Haskell: given a list `x` and a 
    predicate function `f`, `span` returns the location of the longest 
    sequential run of elements where the predicate is true. (Hint: you 
    might find `rle()` helpful.)


### Exercises 5

1.  Implement `arg_max()`. It should take a function and a vector of inputs, 
    and return the elements of the input where the function returns the highest 
    value. For example, `arg_max(-10:5, function(x) x ^ 2)` should return -10.
    `arg_max(-5:5, function(x) x ^ 2)` should return `c(-5, 5)`.
    Also implement the matching `arg_min()` function.

1.  Challenge: read about the 
    [fixed point algorithm](http://mitpress.mit.edu/sicp/full-text/book/book-Z-H-12.html#%_sec_1.3). 
    Complete the exercises using R.
    
    
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

