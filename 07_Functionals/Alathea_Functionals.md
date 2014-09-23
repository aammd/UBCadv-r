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
##  [1] 0.6767 0.6641 0.7508 0.7232 0.7722 0.7411 0.8113 0.7535 0.5698 0.7193
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
