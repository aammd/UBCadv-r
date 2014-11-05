---
title: "Exercises for 'Functionals'"
author: "Davor Cubranic"
date: "30 September, 2014"
output:
  html_document:
    toc: no
    keep_md: TRUE
---

Lapply
------

1. Why are the following two invocations of lapply() equivalent?
   
   
   ```r
       trims <- c(0, 0.1, 0.2, 0.5)
       x <- rcauchy(100)
   
       lapply(trims, function(trim) mean(x, trim = trim))
       lapply(trims, mean, x = x)
   ```

   Because the second form just calls `mean` directly with the value
   of its x argument assigned by `lapply` and the next argument (which
   is `trim` receiving the value of the current iteration element.

2. The function below scales a vector so it falls in the range [0, 1].
   
   
   ```r
       scale01 <- function(x) {
         rng <- range(x, na.rm = TRUE)
         (x - rng[1]) / (rng[2] - rng[1])
       }
   ```

   How would you apply it to every column of a data frame?
   
   ```r
   lapply(df, scale01)
   ```
   
   How would you apply it to every numeric column in a data frame?
   
   ```r
   lapply(df, function(x) { if (is.numeric(x)) scale01(x) else x })
   ```
   
3. Use both for loops and lapply() to fit linear models to the mtcars
   using the formulas stored in this list:

   
   ```r
       formulas <- list(
         mpg ~ disp,
         mpg ~ I(1 / disp),
         mpg ~ disp + wt,
         mpg ~ I(1 / disp) + wt
       )
   ```
   
   With `lapply`:
   
   ```r
   lapply(formulas, lm, data = mtcars)
   ```

   With a for loop:
   
   ```r
   out_formulas <- vector('list', length(formulas))
   for(i in seq_along(formulas)) {
       out_formulas[[i]] <- lm(formulas[[i]], data = mtcars)
   }
   out_formulas
   ```
   
4. Fit the model `mpg ~ disp` to each of the bootstrap replicates of
   `mtcars` in the list below by using a for loop and `lapply()`. Can
   you do it without an anonymous function?
      
   
   ```r
       bootstraps <- lapply(1:10, function(i) {
         rows <- sample(1:nrow(mtcars), rep = TRUE)
         mtcars[rows, ]
       })
   ```

   With `lapply`:
   
   ```r
   lapply(bootstraps, lm, formula = mpg~disp)
   ```

   With a for loop:
   
   ```r
   out_bootstraps <- vector('list', length(bootstraps))
   for(i in seq_along(bootstraps)) {
       out_bootstraps[[i]] <- lm(mpg~disp, data = bootstraps[[i]])
   }
   out_bootstraps
   ```
   
5. For each model in the previous two exercises, extract $R^2$ using
   the function below.
   
   
   ```r
   rsq <- function(mod) summary(mod)$r.squared
   ```
   
   Formulas:
   
   ```r
   lapply(out_formulas, rsq)
   ```
   
   Bootstraps:
   
   ```r
   lapply(out_bootstraps, rsq)
   ```


Friends of `lapply`
-------------------


1. Use `vapply()` to:

   1. Compute the standard deviation of every column in a numeric data
      frame.
      
      
      ```r
      vapply(df, sd, numeric(1))
      ```

   2. Compute the standard deviation of every numeric column in a
      mixed data frame. (Hint: you'll need to use vapply() twice.)
      
      
      ```r
      vapply(df[vapply(df, is.numeric, logical(1))], sd, numeric(1))
      ```

2. Why is using `sapply()` to get the `class()` of each element in a
   data frame dangerous?
   
   Because `class` can return a vector of variable length.

3. The following code simulates the performance of a t-test for
   non-normal data. Use `sapply()` and an anonymous function to
   extract the p-value from every trial.

   
   ```r
   trials <- replicate(
     100, 
     t.test(rpois(10, 10), rpois(7, 10)),
     simplify = FALSE
     )
   ```
   
   Answer:
   
   ```r
   sapply(trials, function(mod) mod$p.value)
   ```
   
   Extra challenge: get rid of the anonymous function by using `[[` directly.
   
   ```r
   sapply(trials, `[[`, 'p.value')
   ```
   
4. What does `replicate()` do? What sort of for loop does it
   eliminate? Why do its arguments differ from `lapply()` and friends?
   
   Replicate repeatedly evaluates an expression. It is roughly similar
   to the following for loop:
   
   
   ```r
   out <- vector('list', n)
   for(i in seq(n)) {
       out[[i]] <- eval(expr)
   }
   out
   ```
   
   Its arguments are different from `lapply` and friends because the
   same expression is evaluated every time, so there are no arguments
   that can be given to it. 

5. Implement a version of `lapply()` that supplies FUN with both the
   name and the value of each component.
   
   
   ```r
   lapply2 <- function(x, f, ...) {
       out <- vector('list', length(x))
       names(out) <- names(x)
       for(nm in names(x)) {
           out[[nm]] <- f(nm, x[[nm]], ...)
       }
       out
   }
   ```
   
6. Implement a combination of `Map()` and `vapply()` to create an
   `lapply()` variant that iterates in parallel over all of its inputs
   and stores its outputs in a vector (or a matrix). What arguments
   should the function take?
   
   Answer:
   
   ```r
   library(parallel)
   mcvMap <- function(f, FUN.VALUE , ...) {
       out <- mcMap(f, ...)
       vapply(out, identity, FUN.VALUE)
   }
   ```

7. Implement `mcsapply()`, a multicore version of `sapply()`. Can you
   implement `mcvapply()`, a parallel version of `vapply()`? Why or
   why not?
   
   Answer:
   
   ```r
   mcsapply <- function(x, f, ...) {
       out <- mclapply(f, ...)
       simplify2array(out)
   }
   mcvapply <- function(x, f, FUN.VALUE, ...) {
       out_list <- mclapply(f, ...)
       out <- matrix(rep(FUN.VALUE, length(x)), nrow = length(x))
       for (i in seq_along(x)) {
           stopifnot(
               length(res) == length(f.value),
               typeof(res) == typeof(f.value))
           out[i, ] <- out_list[[i]]
       }
       out
   }
   ```
   
   
Apply
-----

1. How does `apply()` arrange the output? Read the documentation and
   perform some experiments.

   It always arranges iterations as columns of the output.

2. There's no equivalent to `split()` + `vapply()`. Should there be?
   When would it be useful? Implement one yourself.
    
   No, because in general you may not have the same number of elements
   in each group
   
3. Implement a pure R version of `split()`. (Hint: use `unique()` and
   subsetting.) Can you do it without a for loop?
   
   
   ```r
   split2 <- function(x, f) {
       groups <- unique(as.factor(f))
       out <- vector('list', length(groups))
       names(out) <- levels(groups)
       for (g in groups) {
           out[[g]] <- x[f == g]
       }
       out
   }
   ```
   
4. What other types of input and output are missing? Brainstorm before
   you look up some answers in the plyr paper.


Manipulating lists
------------------

1. Why isn't `is.na()` a predicate function? What base R function is
   closest to being a predicate version of is.na()?

   Because its result is a logical vector of length equal to the
   length of the input.
   
2. Use `Filter()` and `vapply()` to create a function that applies a
   summary statistic to every numeric column in a data frame.

   
   ```r
   nummary <- function(df, f) {
       vapply(Filter(df, is.numeric), f, numeric(1))
   }
   ```
   
3. What's the relationship between `which()` and `Position()`?

   Answer: `Position(f, x)` is equivalent to `which(f(x))[1]`

   What's the relationship between `where()` and `Filter()`?

   Answer: `Filter(f, x)` is equivalent to `x[where(f, x)]`

4. Implement `Any()`, a function that takes a list and a predicate
   function, and returns TRUE if the predicate function returns TRUE
   for any of the inputs. Implement `All()` similarly.

   
   ```r
   All <- function(x) {
       Reduce(`&&`, x, TRUE)
   }
   Any <- function(x) {
       Reduce(`||`, x, FALSE)
   }
   ```

5. Implement the `span()` function from Haskell: given a list x and a
   predicate function `f`, span returns the location of the longest
   sequential run of elements where the predicate is true. (Hint: you
   might find `rle()` helpful.)

   
   ```r
   span <- function(x, f) {
       runs <- rle(vapply(x, f, logical(1)))
       pos <- 1
       max_pos <- NA
       max_length <- -Inf
       for (i in seq_along(runs$lengths)) {
           if (runs$values[[i]] && runs$lengths[[i]] > max_length) {
               max_length <- runs$lengths[[i]]
               max_pos <- pos
           }
           pos <- pos + runs$lengths[[i]]
       }
       max_pos
   }
   ```


Mathematical functionals
------------------------

1. Implement `arg_max()`. It should take a function and a vector of
   inputs, and return the elements of the input where the function
   returns the highest value. For example, `arg_max(-10:5, function(x)
   x ^ 2)` should return -10. `arg_max(-5:5, function(x) x ^ 2)`
   should return `c(-5, 5)`. Also implement the matching `arg_min()`
   function.

   
   ```r
   arg_max <- function(x, f) {
       f.x <- vapply(x, f, numeric(1))
       x[which(f.x == max(f.x))]
   }
   ```

2. Challenge: read about the
   [fixed point algorithm](http://mitpress.mit.edu/sicp/full-text/book/book-Z-H-12.html#%_sec_1.3).
   Complete the exercises using R.

   
   ```r
   fixed_point <- function(f, x0, tol = 0.0001) {
     is_close <- function(a, b) abs(a-b) < tol
     try_next <- function(x) {
       next_x <- f(x)
       if (is_close(x, next_x)) next_x else try_next(next_x)
     }
     try_next(x0)
   }
   fixed_point(cos, 1)
   ```
   
   ```
   ## [1] 0.7391
   ```
   
   ```r
   fixed_point(function(x) sin(x)+cos(x), 1, 0.0001)
   ```
   
   ```
   ## [1] 1.259
   ```
   
   ```r
   sqrt2 <- function(x) fixed_point(function(y) mean(c(y, x/y)), 1)
   sqrt(2)
   ```
   
   ```
   ## [1] 1.414
   ```
   
   ```r
   sqrt(3)
   ```
   
   ```
   ## [1] 1.732
   ```
   
   ```r
   phi <- fixed_point(function(x) 1 + 1/x, 1)
   phi
   ```
   
   ```
   ## [1] 1.618
   ```

Family of functions
-------------------


1. Implement `smaller` and `larger` functions that, given two inputs,
   return either the smaller or the larger value. Implement na.rm =
   TRUE: what should the identity be? (Hint: `smaller(x, smaller(NA,
   NA, na.rm = TRUE), na.rm = TRUE)` must be x, so `smaller(NA, NA,
   na.rm = TRUE)` must be bigger than any other value of x.)
   
   
   ```r
   smaller <- function(x, y, na.rm = FALSE) min(x, y, na.rm = na.rm)
   larger <- function(x, y, na.rm = FALSE) max(x, y, na.rm = na.rm)
   ```
   
   Use `smaller` and `larger` to implement equivalents of `min()`,
   `max()`, `pmin()`, `pmax()`, and new functions `row_min()` and
   `row_max()`.
   
   
   ```r
   min2 <- function(..., na.rm = FALSE) {
       Reduce(function(x, y) smaller(x, y, na.rm), list(...), init = Inf)
   }
   max2 <- function(..., na.rm = FALSE) {
       Reduce(function(x, y) larger(x, y, na.rm), list(...), init = -Inf)
   }
   pmin2 <- function(..., na.rm = FALSE) {
       simplify2array(Map(min2, ..., na.rm = na.rm))
   }
   pmax2 <- function(..., na.rm = FALSE) {
       simplify2array(Map(max2, ..., na.rm = na.rm))
   }
   row_min <- function(x, na.rm = FALSE) {
       apply(x, 1, function(row) {
           Reduce(function(x, y) smaller(x, y, na.rm), row)
       })
   }
   row_max <- function(x, na.rm = FALSE) {
       apply(x, 1, function(row) {
           Reduce(function(x, y) larger(x, y, na.rm), row)
       })
   }
   ```
   
   2. Create a table that has `and`, `or`, `add`, `multiply`, `smaller`,
   and `larger` in the columns and binary operator, reducing variant,
   vectorised variant, and array variants in the rows.

   1. Fill in the cells with the names of base R functions that
      perform each of the roles.

      &nbsp; | and | or | add | multiply | smaller | larger
      -------|-----|-----|-----|-----|-----|-----
      **binary** | `&&` | `||` | `+` | `*` | `min` | `max`
      **reducing** | `and` | `or` | `sum` | `prod` | `min` | `max`
      **vectorised** | ||| `pmin` | `pmax` |
      **array** | | | `row/colSums`| | |

   2. Compare the names and arguments of the existing R functions. How
      consistent are they? How could you improve them?

   3. Complete the matrix by implementing any missing functions.

3. How does `paste()` fit into this structure? What is the scalar
   binary function that underlies `paste()`? What are the `sep` and
   `collapse` arguments to `paste()` equivalent to? Are there any
   paste variants that don't have existing R implementations?
