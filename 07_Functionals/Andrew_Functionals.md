# Andrew_Functionals
Andrew MacDonald  
`r format(Sys.time(), '%d %B, %Y')`  


```r
knitr::opts_chunk$set(error = TRUE)
```


## Exercises 1
### Why are the following two invocations of `lapply()` equivalent?
Because the first element of `mean()` gets interpreted as the value of `trim` **IF** the value of x is already supplied.


```r
mean(0.5, x = c(0:10,50))
```

```
## [1] 5.5
```

### The function below scales a vector so it falls in the range [0, 1]. How would you apply it to every column of a data frame? How would you apply it to every numeric column in a data frame?


```r
scale01 <- function(x) {
  rng <- range(x, na.rm = TRUE)
  (x - rng[1]) / (rng[2] - rng[1])
}

mtcars_scale <- mtcars[]
mtcars_scale[] <- lapply(mtcars, scale01)
head(mtcars_scale)
```

```
##                      mpg cyl    disp     hp   drat     wt   qsec vs am
## Mazda RX4         0.4511 0.5 0.22175 0.2049 0.5253 0.2830 0.2333  0  1
## Mazda RX4 Wag     0.4511 0.5 0.22175 0.2049 0.5253 0.3482 0.3000  0  1
## Datsun 710        0.5277 0.0 0.09204 0.1449 0.5023 0.2063 0.4893  1  1
## Hornet 4 Drive    0.4681 0.5 0.46620 0.2049 0.1475 0.4352 0.5881  1  0
## Hornet Sportabout 0.3532 1.0 0.72063 0.4346 0.1797 0.4927 0.3000  0  0
## Valiant           0.3277 0.5 0.38389 0.1873 0.0000 0.4978 0.6810  1  0
##                   gear   carb
## Mazda RX4          0.5 0.4286
## Mazda RX4 Wag      0.5 0.4286
## Datsun 710         0.5 0.0000
## Hornet 4 Drive     0.0 0.0000
## Hornet Sportabout  0.0 0.1429
## Valiant            0.0 0.0000
```

```r
scale_numerics <- function(dat){
  if(is.numeric(dat)){
    scale01(dat)
    } else {
      dat
      }
  }

iris_scale <- iris

iris_scale[] <- lapply(iris, scale_numerics)

head(iris)
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1          5.1         3.5          1.4         0.2  setosa
## 2          4.9         3.0          1.4         0.2  setosa
## 3          4.7         3.2          1.3         0.2  setosa
## 4          4.6         3.1          1.5         0.2  setosa
## 5          5.0         3.6          1.4         0.2  setosa
## 6          5.4         3.9          1.7         0.4  setosa
```

```r
try(lapply(iris, scale01))
```

### Use both for loops and `lapply()` to fit linear models to the mtcars using the formulas stored in this list:


```r
formulas <- list(
  mpg ~ disp,
  mpg ~ I(1 / disp),
  mpg ~ disp + wt,
  mpg ~ I(1 / disp) + wt
)

mods <- vector("list", length(formulas))

for (i in seq_along(formulas)){
  mods[[i]] <- lm(formulas[[i]], data = mtcars)
  }
```

The `lapply()` method is interesting. normally I would use an anonymous function, but I learned from the examples in this chapter that this is not actually necessary:


```r
models_mtcars <- lapply(formulas, lm, data = mtcars)
# old way
#lapply(formulas, function(form) lm(form, data = mtcars))
```


### Fit the model `mpg ~ disp` to each of the bootstrap replicates of mtcars in the list below by using a for loop and `lapply()`. Can you do it without an anonymous function?


```r
bootstraps <- lapply(1:10, function(i) {
  rows <- sample(1:nrow(mtcars), rep = TRUE)
  mtcars[rows, ]
})

## for loop

bootstrap_models <- vector("list",length(bootstraps))
for(i in seq_along(bootstraps)){
  bootstrap_models[[i]] <- lm(mpg ~ disp, data = bootstraps[[i]])
  }

## lapply

bootstrap_models_lapply <- lapply(bootstraps, lm, formula = mpg ~ disp)
```

I must say, although I love this approach (avoiding anonymous functions) because of its simple elegance, I have my doubts.  It strikes me as both harder to read and error prone.  Harder to read, because your reader might not remember/know that `data` is the second argument to `lm()` (I had to check, and I've been doing this for years). Error-prone, because you might inadvertently pass the list elements to the wrong argument, and not immediately realize your mistake.

### For each model in the previous two exercises, extract R2 using the function below.


```r
rsq <- function(mod) summary(mod)$r.squared

sapply(bootstrap_models_lapply, rsq)
```

```
##  [1] 0.7245 0.6923 0.7323 0.8274 0.8315 0.8376 0.6388 0.6914 0.6739 0.6137
```

```r
sapply(models_mtcars, rsq)
```

```
## [1] 0.7183 0.8597 0.7809 0.8838
```

### Use vapply() to Compute the standard deviation of every column in a numeric data frame.


```r
library(magrittr)

species_abundances <- sample(seq_len(100),size = 5) %>%
  sapply(rpois, n = 20) %>%
  data.frame %>%
  set_names(paste0("sp",1:5))

vapply(species_abundances, sd, numeric(1))
```

```
##    sp1    sp2    sp3    sp4    sp5 
## 12.052  7.956  6.697  3.323  5.807
```

### Compute the standard deviation of every numeric column in a mixed data frame. (Hint: you’ll need to use vapply() twice.)


```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
## 
## The following objects are masked from 'package:stats':
## 
##     filter, lag
## 
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
site_species <- species_abundances %>% 
  mutate(site = paste0("site",LETTERS[1:5]) %>%
           rep(times = 4))

vapply(site_species[vapply(site_species, is.numeric, logical(1))], sd, numeric(1))
```

```
##    sp1    sp2    sp3    sp4    sp5 
## 12.052  7.956  6.697  3.323  5.807
```

```r
## Equivalently, in magrittr style:
site_species %>%
  vapply(is.numeric, logical(1)) %>%
  site_species[.] %>%
  vapply(sd, numeric(1))
```

```
##    sp1    sp2    sp3    sp4    sp5 
## 12.052  7.956  6.697  3.323  5.807
```


### Why is using sapply() to get the class() of each element in a data frame dangerous?
Because many elements will have >1 classes, and`sapply` will silently return a list (you probably wanted a vector, or you would have just used `lapply` directly).

### Use sapply() and an anonymous function to extract the p-value from every trial.


```r
trials <- replicate(
  100, 
  t.test(rpois(10, 10), rpois(7, 10)),
  simplify = FALSE
)

sapply(trials, function(test) test$p.value)
```

```
##   [1] 0.395124 0.938636 0.933433 0.992279 0.699619 0.734354 0.337069
##   [8] 0.639538 0.516133 0.316349 0.625911 0.259112 0.957630 0.186732
##  [15] 0.631408 0.318106 0.039701 0.373798 0.372588 0.009652 0.739855
##  [22] 0.209297 0.118308 0.423157 0.850666 0.796699 0.138726 0.191988
##  [29] 0.776204 0.714290 0.828676 0.848705 0.093241 0.938717 0.675576
##  [36] 0.006341 0.510370 0.462457 0.340265 0.289342 1.000000 0.358995
##  [43] 0.256826 0.486691 0.049126 0.764726 0.368187 0.378001 0.914687
##  [50] 0.248287 0.809298 0.154192 0.743714 0.144545 0.275979 0.612982
##  [57] 0.301194 0.621215 0.431853 0.636959 0.028294 0.246977 0.740926
##  [64] 0.096218 0.028008 0.054615 0.698396 0.336151 0.106659 0.618808
##  [71] 0.360475 0.106651 0.903834 0.327449 0.752204 0.973521 0.071754
##  [78] 0.843765 0.171656 0.631749 0.066435 0.028983 0.942667 0.847624
##  [85] 0.965417 0.132013 0.744280 0.747676 0.604786 0.228295 0.252883
##  [92] 0.881808 0.743797 0.859511 0.975927 0.043768 0.274322 0.331877
##  [99] 0.579088 0.634047
```

```r
sapply(trials, '[[', i = "p.value") 
```

```
##   [1] 0.395124 0.938636 0.933433 0.992279 0.699619 0.734354 0.337069
##   [8] 0.639538 0.516133 0.316349 0.625911 0.259112 0.957630 0.186732
##  [15] 0.631408 0.318106 0.039701 0.373798 0.372588 0.009652 0.739855
##  [22] 0.209297 0.118308 0.423157 0.850666 0.796699 0.138726 0.191988
##  [29] 0.776204 0.714290 0.828676 0.848705 0.093241 0.938717 0.675576
##  [36] 0.006341 0.510370 0.462457 0.340265 0.289342 1.000000 0.358995
##  [43] 0.256826 0.486691 0.049126 0.764726 0.368187 0.378001 0.914687
##  [50] 0.248287 0.809298 0.154192 0.743714 0.144545 0.275979 0.612982
##  [57] 0.301194 0.621215 0.431853 0.636959 0.028294 0.246977 0.740926
##  [64] 0.096218 0.028008 0.054615 0.698396 0.336151 0.106659 0.618808
##  [71] 0.360475 0.106651 0.903834 0.327449 0.752204 0.973521 0.071754
##  [78] 0.843765 0.171656 0.631749 0.066435 0.028983 0.942667 0.847624
##  [85] 0.965417 0.132013 0.744280 0.747676 0.604786 0.228295 0.252883
##  [92] 0.881808 0.743797 0.859511 0.975927 0.043768 0.274322 0.331877
##  [99] 0.579088 0.634047
```

### What does replicate() do? What sort of for loop does it eliminate? Why do its arguments differ from lapply() and friends?

replicate is just sapply run over a vector of length `n` (first arg), that evaluates an expression once for each element and then simplifies the result.  
It eliminates for loops which just repeatedly evaluate an expression. Usually for random numbers; I can't think of a reason to use `replicate()` if something isn't random.
The second argument of `replicate()` isn't a function, it's an expression.  That's because it isn't actually *doing* anything with the vector of numbers, unlike the apply family.


```r
replicate(5, rnorm(2))
```

```
##         [,1]     [,2]    [,3]   [,4]     [,5]
## [1,]  0.9845 -0.02792 -0.3915 -0.752 -0.43394
## [2,] -0.8937  1.88882  2.4313 -1.360 -0.07852
```

```r
replicate(4, "blue")
```

```
## [1] "blue" "blue" "blue" "blue"
```


### Implement a version of lapply() that supplies FUN with both the name and the value of each component.

Not exactly sure what is meant by "component" here, but here goes:


```r
colnum <- c("blue" = 2, "green" = 7)

lapply(colnum, function(x) x/2)
```

```
## $blue
## [1] 1
## 
## $green
## [1] 3.5
```

ahh, it actually requires a `Map` or mapply solution!

```r
name_val_lapply <- function(X, FUN, ...){
 Map(FUN, names(X), X, ...)
}

funtest <- function(name_of, val_of){
  paste("the value of", name_of, "is", val_of)
}

name_val_lapply(colnum, funtest)
```

```
## $blue
## [1] "the value of blue is 2"
## 
## $green
## [1] "the value of green is 7"
```

### Implement a combination of Map() and vapply() to create an lapply() variant that iterates in parallel over all of its inputs and stores its outputs in a vector (or a matrix). What arguments should the function take?

I am hazy about what is going on here.  IN fact, rereading this section after looking at this question suggests to me that there are two different uses of the word "parallel" going on here: one refers to sending computations to different cores, and the other to the "zipper" action of `Map` | `mapply`.  not sure what do to here?


### Implement mcsapply(), a multicore version of sapply(). Can you implement mcvapply(), a parallel version of vapply()? Why or why not?

my intuition is that `mcvapply` is impossible, since it allocates the vector first and how can you do that with parallel? you probably can't.

In the cheapest move ever, I implement sapply by taking the guts of sapply and changing lapply to mclapply:

```r
library(parallel)

mcsapply <- function (X, FUN, ..., simplify = TRUE, USE.NAMES = TRUE) 
  {
  FUN <- match.fun(FUN)
  answer <- mclapply(X = X, FUN = FUN, mc.cores = 2, ...)
  if (USE.NAMES && is.character(X) && is.null(names(answer))) 
    names(answer) <- X
  if (!identical(simplify, FALSE) && length(answer)) 
    simplify2array(answer, higher = (simplify == "array"))
  else answer
  }

##pokey example
boot_df <- function(x) x[sample(nrow(x), rep = T), ]
rsquared <- function(mod) summary(mod)$r.square
boot_lm <- function(i) {
  rsquared(lm(mpg ~ wt + disp, data = boot_df(mtcars)))
}


library(microbenchmark)

## this doesn't actually speed anything up
if(FALSE){
microbenchmark(sapply(1:10, boot_lm),
               mcsapply(1:10, boot_lm))
}
```


## Matrix functionals

### How does apply() arrange the output? Read the documentation and perform some experiments.



```r
mat <- matrix(rnorm(20), nrow = 5)

apply(mat, 1, summary)
```

```
##             [,1]   [,2]    [,3]    [,4]    [,5]
## Min.    -0.22800 -1.490 -0.9800 -0.4810 -0.1500
## 1st Qu. -0.19800 -0.838 -0.5110 -0.3010 -0.0998
## Median   0.00688 -0.110 -0.3120 -0.0403  0.2130
## Mean     0.20800 -0.214 -0.4160  0.3980  0.6290
## 3rd Qu.  0.41300  0.514 -0.2170  0.6590  0.9420
## Max.     1.05000  0.855 -0.0586  2.1500  2.2400
```

```r
apply(mat, 2, summary)
```

```
##            [,1]    [,2]   [,3]   [,4]
## Min.    -0.1500 -0.9800 -0.355 -1.490
## 1st Qu. -0.0586 -0.6200 -0.241 -0.481
## Median   0.2020 -0.2280  0.400 -0.269
## Mean     0.6000 -0.3500  0.619 -0.385
## 3rd Qu.  0.8550 -0.0829  1.050 -0.188
## Max.     2.1500  0.1600  2.240  0.508
```

```r
apply(mtcars, 2, summary)
```

```
##          mpg  cyl  disp    hp drat   wt qsec    vs    am gear carb
## Min.    10.4 4.00  71.1  52.0 2.76 1.51 14.5 0.000 0.000 3.00 1.00
## 1st Qu. 15.4 4.00 121.0  96.5 3.08 2.58 16.9 0.000 0.000 3.00 2.00
## Median  19.2 6.00 196.0 123.0 3.70 3.32 17.7 0.000 0.000 4.00 2.00
## Mean    20.1 6.19 231.0 147.0 3.60 3.22 17.8 0.438 0.406 3.69 2.81
## 3rd Qu. 22.8 8.00 326.0 180.0 3.92 3.61 18.9 1.000 1.000 4.00 4.00
## Max.    33.9 8.00 472.0 335.0 4.93 5.42 22.9 1.000 1.000 5.00 8.00
```

### There’s no equivalent to split() + vapply(). Should there be? When would it be useful? Implement one yourself.


```r
pulse <- round(rnorm(22, 70, 10 / 3)) + rep(c(0, 5), c(10, 12))
group <- rep(c("A", "B"), c(10, 12))
pieces <- split(pulse, group)

split_vapply <- function(x, f, FUN, FUN.VALUE, ...){
  pieces <- split(x, f)
  vapply(pieces, FUN = FUN, FUN.VALUE = FUN.VALUE, ...)
}

split_vapply(pulse, group, mean, numeric(1))
```

```
##     A     B 
## 71.70 74.08
```


### Implement a pure R version of split(). (Hint: use unique() and subsetting.) Can you do it without a for loop?


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

split2(pulse, group)
```

```
## $A
##  [1] 65 71 72 74 77 71 71 74 72 70
## 
## $B
##  [1] 76 74 77 73 77 69 71 73 70 77 74 78
```

```r
split3 <- function(x, f) {
   groups <- unique(f)
   out <- vector('list', length(groups))
   names(out) <- groups
   lapply(groups, function(lev) x[f == lev] )
}


split3(pulse, group)
```

```
## [[1]]
##  [1] 65 71 72 74 77 71 71 74 72 70
## 
## [[2]]
##  [1] 76 74 77 73 77 69 71 73 70 77 74 78
```

### What other types of input and output are missing? Brainstorm before you look up some answers in the plyr paper.

er, isn't this just the missing blanks in the plyr table?

#### playing with reduce


```r
Reduce(`*`, 1:10)
```

```
## [1] 3628800
```

```r
factorial(10)
```

```
## [1] 3628800
```

```r
microbenchmark(Reduce(`*`, 1:10), factorial(10))
```

```
## Unit: nanoseconds
##               expr  min   lq median   uq   max neval
##  Reduce(`*`, 1:10) 8162 8490   8664 8991 77404   100
##      factorial(10)  460  545    856  941 15300   100
```


```r
sapply(rep(1,10), Reduce, f = sum)
```

```
##  [1] 1 1 1 1 1 1 1 1 1 1
```

```r
Reduce(`*`, 1:10, accumulate = TRUE)
```

```
##  [1]       1       2       6      24     120     720    5040   40320
##  [9]  362880 3628800
```

```r
simdecay <- function(){
  replicate(10, sample(1:10, 15, replace = T), simplify = FALSE) %>%
  Reduce(intersect, ., accumulate = TRUE) %>%
  sapply(length)
}

replicate(10, simdecay()) %>%
  matplot(type = "l")
```

![plot of chunk fib](./Andrew_Functionals_files/figure-html/fib.png) 

## Lists and Predicates

### Why isn’t is.na() a predicate function? What base R function is closest to being a predicate version of is.na()?

Its result is as long as its input -- unlike a true predicate, which returns length 1 always


```r
is.character(c("foo","bar"))
```

```
## [1] TRUE
```

```r
is.na(NaN)
```

```
## [1] TRUE
```

```r
is.na(NA)
```

```
## [1] TRUE
```

```r
is.na(3)
```

```
## [1] FALSE
```

```r
is.na(c(NA,NA))
```

```
## [1] TRUE TRUE
```

```r
is.numeric(c("fo","bar"))
```

```
## [1] FALSE
```

`anyNA` is closest to a predicate:


```r
anyNA(c(NA,NA))
```

```
## [1] TRUE
```

```r
anyNA(c("foo","bar"))
```

```
## [1] FALSE
```
um in fact it IS a predicate??

### Use Filter() and vapply() to create a function that applies a summary statistic to every numeric column in a data frame.


```r
numsum <- function(dat, sumstat){
  so_numeros <- Filter(is.numeric, dat)
  vapply(so_numeros, sumstat, numeric(1))
}

numsum(iris, sd)
```

```
## Sepal.Length  Sepal.Width Petal.Length  Petal.Width 
##       0.8281       0.4359       1.7653       0.7622
```

### What’s the relationship between which() and Position()? What’s the relationship between where() and Filter()?

`Position()` gives the first instance of TRUE; `which()` gives em all:


```r
scramble <- sample(1:5, size = 50, replace = TRUE)
Position(function(x) x == 1, scramble)
```

```
## [1] 4
```

```r
which(scramble == 1)
```

```
##  [1]  4  6  7 10 15 19 20 24 25 27 40 41 42 47
```

```r
min(which(scramble == 1))
```

```
## [1] 4
```

`where()` returns a logical vector, while `Filter()` returns the subset that you might produce with it:

```r
where <- function(f, x) {
  vapply(x, f, logical(1))
}
```

### Implement Any(), a function that takes a list and a predicate function, and returns TRUE if the predicate function returns TRUE for any of the inputs. Implement All() similarly.

I can think of a couple of ways to do this. is it cheating to use `any()` and `all()`?


```r
AnyAll <- function(FUN){
  function(f, x){
    test <- vapply(x, f, logical(1))
    FUN(test)
    }
}

Any <- AnyAll(any)
All <- AnyAll(all)

tester <- list("foo",1)

Any(is.numeric, tester)
```

```
## [1] TRUE
```

```r
All(is.numeric, tester)
```

```
## [1] FALSE
```

Here is less cheap means to do this, stolen from Davor


```r
AnyAll2 <- function(only_if_all = TRUE){
  ## any or all?
  if (only_if_all) {
    testfun <- function(test) Reduce(`&&`, test, only_if_all)
    } else 
      {
        testfun <- function(test) Reduce(`||`, test, only_if_all)
        }
  
  function(f, x){
    test <- vapply(x, f, logical(1))
    testfun(test)
    }
  }

All2 <- AnyAll2()
Any2 <- AnyAll2(FALSE)

Any2(is.numeric, tester)
```

```
## [1] TRUE
```

```r
All2(is.numeric, tester)
```

```
## [1] FALSE
```

```r
AnyAll3 <- function(only_if_all = TRUE){
  ## any or all?
  tests <- c(`||`,`&&`)
  testfun <- function(test) Reduce(tests[[only_if_all + 1]], test, only_if_all)
  
  function(f, x){
    test <- vapply(x, f, logical(1))
    testfun(test)
    }
  }

AnyAll3()(is.numeric, tester)
```

```
## [1] FALSE
```

```r
AnyAll3(FALSE)(is.numeric, tester)
```

```
## [1] TRUE
```

### Implement the span() function from Haskell: given a list x and a predicate function f, span returns the location of the longest sequential run of elements where the predicate is true. (Hint: you might find rle() helpful.)
Building off of `where()`

```r
where <- function(f, x) {
  vapply(x, f, logical(1))
}

span <- function(f, x){
  logivec <- where(f = f, x = x)
  runs <- rle(logivec)
  trues <- runs$lengths[runs$values]
  longest <- max(trues)[1] # find the first long sequence only
  start.longest <- which(runs$lengths == longest)
  start.longest + seq_len(longest)
}

green30 <- replicate(47, sample(list("green",30), size = 1, prob = c(0.7, 0.3)))

span(is.character, green30)
```

```
## [1]  8  9 10 11 12 13 14 15
```

## mathematical functions

### Implement arg_max(). It should take a function and a vector of inputs, and return the elements of the input where the function returns the highest value. For example, arg_max(-10:5, function(x) x ^ 2) should return -10. arg_max(-5:5, function(x) x ^ 2) should return c(-5, 5). Also implement the matching arg_min() function.



## reading notes 

I really liked this simple example of a functional to make randomized versions of common summary functions:


```r
randomise <- function(f) f(runif(1e3))
randomise(mean)
```

```
## [1] 0.4972
```

```r
#> [1] 0.5115665
randomise(mean)
```

```
## [1] 0.5015
```

```r
#> [1] 0.503939
randomise(sum)
```

```
## [1] 506.2
```

```r
replicate(500,randomise(sum)) %>%
  data.frame(x = .) %>%
  ggplot(aes(x = x)) + geom_density()
```

```
## Error: could not find function "ggplot"
```

