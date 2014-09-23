# Functionals
Alathea  
2014-09-04  

# The Exercises

## Why are the following two invocations of `lapply()` equivalent?

```r
trims <- c(0, 0.1, 0.2, 0.5)
x <- rcauchy(100)

lapply(trims, function(trim) mean(x, trim = trim))
lapply(trims, mean, x = x)
```

## The function below scales a vector so it falls in the range [0, 1]. How would you apply it to every column of a data frame? How would you apply it to every numeric column in a data frame?

```r
scale01 <- function(x) {
  rng <- range(x, na.rm = TRUE)
  (x - rng[1]) / (rng[2] - rng[1])
}

not_scaled <- data.frame(a = runif(10, 1, 1000),
                         b = runif(10, 1, 1000))
not_scaled2 <- not_scaled
not_scaled2$c <- c(letters[1:10])

not_scaled

lapply(not_scaled, function(x) scale01(x))
lapply(not_scaled, function(x) scale01(x))
```

## Use both `for` loops and `lapply()` to fit linear models to the `mtcars` using the formulas stored in this list:


```r
formulas <- list(
  mpg ~ disp,
  mpg ~ I(1 / disp),
  mpg ~ disp + wt,
  mpg ~ I(1 / disp) + wt
)

for(i in 1:length(formulas))
{
  model <- lm(formulas[[i]], data = mtcars)
  print(summary(model))
}

lapply(formulas, function(x) summary(lm(x, data = mtcars)))
```

## Fit the model `mpg ~ disp` to each of the bootstrap replicates of `mtcars` in the list below by using a `for` loop and `lapply()`. Can you do it without an anonymous function?


```r
bootstraps <- lapply(1:10, function(i) {
  rows <- sample(1:nrow(mtcars), rep = TRUE)
  mtcars[rows, ]
})

lapply(bootstraps, function(x) lm(mpg ~ disp, data = x))

for(i in 1:length(bootstraps)){
  print(lm(mpg ~ disp, data = bootstraps[[i]]))
}
```

## For each model in the previous two exercises, extract R2 using the function below.


```r
rsq <- function(mod) summary(mod)$r.squared

bootstraps <- lapply(1:10, function(i) {
  rows <- sample(1:nrow(mtcars), rep = TRUE)
  mtcars[rows, ]
})
models <- lapply(bootstraps, function(x) lm(mpg ~ disp, data = x))

unlist(lapply(models, function(x) rsq(x)))
```

```
##  [1] 0.6291 0.8473 0.7640 0.8014 0.7092 0.5669 0.7041 0.7760 0.7549 0.7475
```

## Use `vapply()` to: a) Compute the standard deviation of every column in a numeric data frame. b) Compute the standard deviation of every numeric column in a mixed data frame. (Hint: you’ll need to use vapply() twice.)


```r
vapply(mtcars, sd, double(1))
```

```
##      mpg      cyl     disp       hp     drat       wt     qsec       vs 
##   6.0269   1.7859 123.9387  68.5629   0.5347   0.9785   1.7869   0.5040 
##       am     gear     carb 
##   0.4990   0.7378   1.6152
```

```r
# using the iris dataset:
vapply(iris, is.numeric, logical(1))
```

```
## Sepal.Length  Sepal.Width Petal.Length  Petal.Width      Species 
##         TRUE         TRUE         TRUE         TRUE        FALSE
```

## Why is using `sapply()` to get the `class()` of each element in a data frame dangerous?


```r
sapply(iris, class)
```

```
## Sepal.Length  Sepal.Width Petal.Length  Petal.Width      Species 
##    "numeric"    "numeric"    "numeric"    "numeric"     "factor"
```

```r
vapply(iris, class, character(1))
```

```
## Sepal.Length  Sepal.Width Petal.Length  Petal.Width      Species 
##    "numeric"    "numeric"    "numeric"    "numeric"     "factor"
```

## The following code simulates the performance of a t-test for non-normal data. Use `sapply()` and an anonymous function to extract the p-value from every trial.  Extra challenge: get rid of the anonymous function by using [[ directly.



```r
trials <- replicate(
  100, 
  t.test(rpois(10, 10), rpois(7, 10)),
  simplify = FALSE
)

sapply(trials, function(x) get("p.value", x))
```

## What does `replicate()` do? What sort of for loop does it eliminate? Why do its arguments differ from `lapply()` and friends?

`replicate()` 

## Implement a version of `lapply()` that supplies `FUN` with both the name and the value of each component.

## Implement a combination of `Map()` and `vapply()` to create an `lapply()` variant that iterates in parallel over all of its inputs and stores its outputs in a vector (or a matrix). What arguments should the function take?

You could do this by writing a function that uses `Map()` on a list of items, given input vectors that should be applied in parallel to this list of items.  You basically need a combination of the arguments from vapply, and those from Map.


```r
vapply_map <- function(x, f, FUN.VALUE, ...)
{
  vapply(x, Map(f, ...), FUN.VALUE)
}
```

## Implement `mcsapply()`, a multicore version of `sapply()`. Can you implement `mcvapply()`, a parallel version of `vapply()`? Why or why not?

## How does `apply()` arrange the output? Read the documentation and perform some experiments.


```r
apply(mtcars, 1, mean)
```

```
##           Mazda RX4       Mazda RX4 Wag          Datsun 710 
##               29.91               29.98               23.60 
##      Hornet 4 Drive   Hornet Sportabout             Valiant 
##               38.74               53.66               35.05 
##          Duster 360           Merc 240D            Merc 230 
##               59.72               24.63               27.23 
##            Merc 280           Merc 280C          Merc 450SE 
##               31.86               31.79               46.43 
##          Merc 450SL         Merc 450SLC  Cadillac Fleetwood 
##               46.50               46.35               66.23 
## Lincoln Continental   Chrysler Imperial            Fiat 128 
##               66.06               65.97               19.44 
##         Honda Civic      Toyota Corolla       Toyota Corona 
##               17.74               18.81               24.89 
##    Dodge Challenger         AMC Javelin          Camaro Z28 
##               47.24               46.01               58.75 
##    Pontiac Firebird           Fiat X1-9       Porsche 914-2 
##               57.38               18.93               24.78 
##        Lotus Europa      Ford Pantera L        Ferrari Dino 
##               24.88               60.97               34.51 
##       Maserati Bora          Volvo 142E 
##               63.16               26.26
```

```r
apply(mtcars, 2, mean)
```

```
##      mpg      cyl     disp       hp     drat       wt     qsec       vs 
##  20.0906   6.1875 230.7219 146.6875   3.5966   3.2172  17.8487   0.4375 
##       am     gear     carb 
##   0.4062   3.6875   2.8125
```

`apply()` returns a vector ordered by row or column, depending on which was used as input.

## There’s no equivalent to `split()` + `vapply()`. Should there be? When would it be useful? Implement one yourself.

This might be useful if you had a complex data structure such as a list of lists.  You could split by one level of list and apply a function to that subset.

## Implement a pure R version of `split()`. (Hint: use `unique()` and subsetting.) Can you do it without a `for` loop?

## What other types of input and output are missing? Brainstorm before you look up some answers in the `plyr` paper.


# Discussion Notes

# Reading Notes

A *functional* takes a function as input and returns a vector as output


```r
unlist(lapply(mtcars, class))
```

```
##       mpg       cyl      disp        hp      drat        wt      qsec 
## "numeric" "numeric" "numeric" "numeric" "numeric" "numeric" "numeric" 
##        vs        am      gear      carb 
## "numeric" "numeric" "numeric" "numeric"
```


```r
# slow loop
xs <- runif(1e3)
res <- c()
for (x in xs) {
  # This is slow!
  res <- c(res, sqrt(x))
}

#fast loop
res <- numeric(length(xs))
for (i in seq_along(xs)) {
  res[i] <- sqrt(xs[i])
}
```

* `sapply` and `vapply` are both wrappers for `lapply` that return vectors
* `vapply` is better for use within functions because it is more verbose about errors
* Use `Map` to process two lists in parallel
* `Map` is very similar to `mapply`
* `apply` functions work well with parallelisation because the order doesn't matter

`sweep()`


```r
(x <- matrix(rnorm(20, 0, 10), nrow = 4))
```

```
##        [,1]   [,2]    [,3]     [,4]   [,5]
## [1,] -8.431  2.271  -2.621  15.9053 20.265
## [2,] 14.113  6.352 -21.782  -0.3917 -3.196
## [3,]  3.724  6.425  -6.940   8.5159  7.918
## [4,] -7.672 -6.979   3.724 -19.2354  6.662
```

```r
(x1 <- sweep(x, 1, apply(x, 1, min), `-`))
```

```
##       [,1]  [,2]   [,3]  [,4]  [,5]
## [1,]  0.00 10.70  5.809 24.34 28.70
## [2,] 35.89 28.13  0.000 21.39 18.59
## [3,] 10.66 13.36  0.000 15.46 14.86
## [4,] 11.56 12.26 22.960  0.00 25.90
```

```r
x2 <- sweep(x1, 1, apply(x1, 1, max), `/`)
```
