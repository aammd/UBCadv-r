# Exercises for 'Debugging and Condition Handling'
Davor Cubranic  
`r format(Sys.time(), '%d %B, %Y')`  

1. What is the advantage of using `withCallingHandlers` in this scenario?

    A: with `tryCatch`, the traceback shows the error caused by the `tryCatch`, while in the case of `withCallingHandlers` the error is shown in the code that called the `message` function.


2. Modify `col_means` to be more robust.
    - Modified function:

```r
col_means <- function(df) {
  if (!is.data.frame(df)) stop('not a data frame')
  
  numeric <- vapply(df, is.numeric, logical(1))
  numeric_cols <- df[, numeric, drop=FALSE]
  
  data.frame(lapply(numeric_cols, mean))
}
```
    - Testing:

```r
library(testthat)
test_that('colmeans', {
  
  expect_equal(col_means(mtcars),
               as.data.frame(t(colMeans(mtcars))))
  expect_equal(col_means(mtcars[,0]),
               data.frame())
  expect_equal(col_means(mtcars[0,]),
               as.data.frame(t(colMeans(mtcars[0,]))))
  expect_equal(col_means(mtcars[,'mpg', drop=F]),
               as.data.frame(t(colMeans(mtcars[,'mpg', drop=F]))))
  expect_error(col_means(1:10), 'not a data frame')
  expect_error(col_means(as.matrix(mtcars)),
               'not a data frame')
  expect_error(col_means(as.list(mtcars)),
               'not a data frame')
  
  mtcars2 <- mtcars
  mtcars2[-1] <- lapply(mtcars2[-1], as.character)
  expect_equal(col_means(mtcars2),
               as.data.frame(t(colMeans(mtcars2[, 1, drop=FALSE]))))
})
```

3. Improve `lag` function to better handle unusual or bad inputs.
    - Modified function:

```r
lag <- function(x, n = 1L) {
  if (!is.vector(x)) stop("'x' is not a vector")
  if (!((is.numeric(n) || is.integer(n)) &&
          length(n) == 1 && !is.na(n))) stop("'n' is not a scalar")
  if (n < 0) stop("'n' cannot be negative")
  if (n > length(x)) stop("the amount of lag cannot be greater than the length of the vector")
  xlen <- length(x)
  c(rep(NA, n), x[seq_len(xlen - n)])
}
```
    - Testing:

```r
library(testthat)
test_that('lag', {
  expect_equal(lag(1:10),
               c(NA, 1:9))

  expect_equal(lag(1:10, 3),
               c(NA, NA, NA, 1:7))
  
  expect_equal(lag(1:10, 0),
               1:10)
  
  expect_error(lag(1:10, 20), 
               'the amount of lag cannot be greater than the length of the vector')
  
  expect_error(lag(NULL), 'not a vector')
  expect_error(lag(1:10, NULL), 'not a scalar')
  expect_error(lag(1:10, 1:2), 'not a scalar')
  expect_error(lag(1:10, -1), 'cannot be negative')
})
```
