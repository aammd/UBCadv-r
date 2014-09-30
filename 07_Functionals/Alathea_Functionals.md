# Functionals
Alathea  
2014-09-04  

## Applications

**Use `Reduce()` to calculate values from a discrete time step equation**


```r
discrete_growth <- function(N, b, d, t)
{
  N + (b - d)*N
}

times <- list(0:10)
```

## The Exercises

### Why are the following two invocations of `lapply()` equivalent?

```r
trims <- c(0, 0.1, 0.2, 0.5)
x <- rcauchy(100)

lapply(trims, function(trim) mean(x, trim = trim))
lapply(trims, mean, x = x)
```

***

### The function below scales a vector so it falls in the range [0, 1]. How would you apply it to every column of a data frame? How would you apply it to every numeric column in a data frame?

```r
scale01 <- function(x) {
  rng <- range(x, na.rm = TRUE)
  (x - rng[1]) / (rng[2] - rng[1])
}

not_scaled <- data.frame(a = runif(10, 1, 1000),
                         b = runif(10, 1, 1000))
not_scaled2 <- not_scaled
not_scaled2$c <- c(letters[1:10])

lapply(not_scaled, scale01)
```

***

### Use both `for` loops and `lapply()` to fit linear models to the `mtcars` using the formulas stored in this list:


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

***

### Fit the model `mpg ~ disp` to each of the bootstrap replicates of `mtcars` in the list below by using a `for` loop and `lapply()`. Can you do it without an anonymous function?


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

***

### For each model in the previous two exercises, extract R2 using the function below.


```r
rsq <- function(mod) summary(mod)$r.squared

bootstraps <- lapply(1:10, function(i) {
  rows <- sample(1:nrow(mtcars), rep = TRUE)
  mtcars[rows, ]
})

models <- lapply(bootstraps, function(x) lm(mpg ~ disp, data = x))
unlist(lapply(models, rsq))
```

```
##  [1] 0.6703 0.7012 0.6402 0.7196 0.7857 0.6134 0.6671 0.6495 0.7476 0.7723
```

***

### Use `vapply()` to: a) Compute the standard deviation of every column in a numeric data frame. b) Compute the standard deviation of every numeric column in a mixed data frame. (Hint: you’ll need to use vapply() twice.)


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
vapply(iris[vapply(iris, is.numeric, logical(1))], sd, numeric(1))
```

```
## Sepal.Length  Sepal.Width Petal.Length  Petal.Width 
##       0.8281       0.4359       1.7653       0.7622
```

***

### Why is using `sapply()` to get the `class()` of each element in a data frame dangerous?


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

Sometimes there are several classes associated with a column and `sapply` has an invisible error.

***

### The following code simulates the performance of a t-test for non-normal data. Use `sapply()` and an anonymous function to extract the p-value from every trial.  Extra challenge: get rid of the anonymous function by using `[[` directly.


```r
trials <- replicate(
  100, 
  t.test(rpois(10, 10), rpois(7, 10)),
  simplify = FALSE
)

sapply(trials, function(x) get("p.value", x))
sapply(trials, `[[`, "p.value")
```

***

### What does `replicate()` do? What sort of for loop does it eliminate? Why do its arguments differ from `lapply()` and friends?

`replicate()` 

***

### Implement a version of `lapply()` that supplies `FUN` with both the name and the value of each component.

***

### Implement a combination of `Map()` and `vapply()` to create an `lapply()` variant that iterates in parallel over all of its inputs and stores its outputs in a vector (or a matrix). What arguments should the function take?

You could do this by writing a function that uses `Map()` on a list of items, given input vectors that should be applied in parallel to this list of items.  You basically need a combination of the arguments from vapply, and those from Map.


```r
vapply_map <- function(x, f, FUN.VALUE, ...)
{
  vapply(x, Map(f, ...), FUN.VALUE)
}
```

### Implement `mcsapply()`, a multicore version of `sapply()`. Can you implement `mcvapply()`, a parallel version of `vapply()`? Why or why not?

***

### How does `apply()` arrange the output? Read the documentation and perform some experiments.


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

`apply()` returns a data frame with columns named as row or column names, depending on which was used as input.

***

### There’s no equivalent to `split()` + `vapply()`. Should there be? When would it be useful? Implement one yourself.

This might be useful if you had a complex data structure such as a list of lists.  You could split by one level of list and apply a function to that subset.

***

### Implement a pure R version of `split()`. (Hint: use `unique()` and subsetting.) Can you do it without a `for` loop?

***

### What other types of input and output are missing? Brainstorm before you look up some answers in the `plyr` paper.

***

### Why isn’t `is.na()` a predicate function? What base R function is closest to being a predicate version of `is.na()`?

`is.na` returns `TRUE` or `FALSE` for each element of a list, whereas the predicate functions return a single `TRUE` or `FALSE`


```r
test <- list(1, 2, 3, NA, "a", "b")
is.character(test)
```

```
## [1] FALSE
```

```r
is.na(test)
```

```
## [1] FALSE FALSE FALSE  TRUE FALSE FALSE
```

***

### Use `Filter()` and `vapply()` to create a function that applies a summary statistic to every numeric column in a data frame.

***

### What’s the relationship between `which()` and `Position()`? What’s the relationship between `where()` and `Filter()`?


```r
where <- function(f, x) {
  vapply(x, f, logical(1))
}
```

`which()` will return the index of all elements with value `TRUE` whereas `Position()` returns the index of the first element only

`where()` returns a list with `TRUE` or `FALSE` for each element depending on whether or not it matches the predicate, whereas `Filter` simply returns a list containing only the elements that match the predicate.

***

### Implement `Any()`, a function that takes a list and a predicate function, and returns TRUE if the predicate function returns TRUE for any of the inputs. Implement All() similarly.

***

### Implement the `span()` function from Haskell: given a list x and a predicate function f, span returns the location of the longest sequential run of elements where the predicate is true. (Hint: you might find `rle()` helpful.)

***

### Implement `arg_max()`. It should take a function and a vector of inputs, and return the elements of the input where the function returns the highest value. For example, `arg_max(-10:5, function(x) x ^ 2)` should return `-10`. `arg_max(-5:5, function(x) x ^ 2)` should return `c(-5, 5)`. Also implement the matching `arg_min()` function.

***

### Challenge: read about the fixed point algorithm. Complete the exercises using R.

[link here](http://mitpress.mit.edu/sicp/full-text/book/book-Z-H-12.html#%_sec_1.3)

***

### Implement smaller and larger functions that, given two inputs, return either the smaller or the larger value. Implement na.rm = TRUE: what should the identity be? (Hint: smaller(x, smaller(NA, NA, na.rm = TRUE), na.rm = TRUE) must be x, so smaller(NA, NA, na.rm = TRUE) must be bigger than any other value of x.) Use smaller and larger to implement equivalents of min(), max(), pmin(), pmax(), and new functions row_min() and row_max().

***

### Create a table that has and, or, add, multiply, smaller, and larger in the columns and binary operator, reducing variant, vectorised variant, and array variants in the rows.

1. Fill in the cells with the names of base R functions that perform each of the roles.
2. Compare the names and arguments of the existing R functions. How consistent are they? How could you improve them?
3. Complete the matrix by implementing any missing functions.

***

### How does paste() fit into this structure? What is the scalar binary function that underlies paste()? What are the sep and collapse arguments to paste() equivalent to? Are there any paste variants that don’t have existing R implementations?


## Discussion Notes

## Reading Notes

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
##         [,1]   [,2]   [,3]    [,4]    [,5]
## [1,]  10.422 -6.143 -2.369  2.2404 -3.0395
## [2,]  -1.531 -5.984 -7.143 -0.9384 -4.4323
## [3,] -10.442 10.337 17.179 -8.0171  1.9341
## [4,]   9.359 -3.355 -9.004 -3.0141  0.4868
```

```r
(x1 <- sweep(x, 1, apply(x, 1, min), `-`))
```

```
##        [,1]   [,2]   [,3]  [,4]   [,5]
## [1,] 16.565  0.000  3.774 8.383  3.103
## [2,]  5.612  1.159  0.000 6.205  2.711
## [3,]  0.000 20.779 27.621 2.425 12.376
## [4,] 18.363  5.649  0.000 5.990  9.491
```

```r
x2 <- sweep(x1, 1, apply(x1, 1, max), `/`)
```

`Reduce()` useful for recursive operations

* given a function, folds together adjacent items in a list using the function

