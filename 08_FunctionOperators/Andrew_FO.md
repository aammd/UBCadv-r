# Andrew_FOs
Andrew MacDonald  
`r format(Sys.time(), '%d %B, %Y')`  


```r
knitr::opts_chunk$set(error = TRUE, cache = TRUE)
library(ggplot2)
library(tidyr)
library(magrittr)
```

```
## 
## Attaching package: 'magrittr'
## 
## The following object is masked from 'package:tidyr':
## 
##     extract
```

## Write a FO that logs a time stamp and message to a file every time a function is run.


```r
write_time <- function(timefile = "time.txt", f) {
  force(f)
  function(...) {
    now <- as.character(Sys.time())
    write(now,file = timefile)
    f(...)
  }
}

write_time(f = runif)(1)
```

```
## [1] 0.3928
```

## What does the following function do? What would be a good name for it?

This function saves its answer the first time and always returns that result, even if inputs change.  I'd call it `first_impression`

## modify delay_by


```r
library(lubridate)
```

```
## Error: there is no package called 'lubridate'
```

```r
delay_by2 <- function(delay, f) {
  force(f)
  runtime <- 0 
  dt <- as.numeric(Sys.time() - runtime)
  if(dt < delay) {
    function(...) {
      Sys.sleep(delay)
      f(...)
      runtime <<- Sys.time()
      }
    } else {
      function(...) {
       f(...)
       }
    }
}

g <- delay_by2(1, f = rnorm); g(100); Sys.sleep(2); g(100)
```

```
##   [1] -0.3485980  0.1061968  0.2276490 -0.6170184  1.2555195  0.2539663
##   [7]  0.0787527  1.2760550 -0.0158649  0.4746212 -0.5243083 -0.5326173
##  [13] -1.1193566  0.2643011  1.1787473  0.7306431 -0.7334601  1.1382327
##  [19]  1.2005419 -0.1901590  0.2471657 -1.0529072 -0.4439278  0.7435181
##  [25]  0.5551994  0.5824317  1.9520800 -0.4129079  1.2979595  1.1777063
##  [31]  0.0002998 -0.9197957 -0.2596468 -0.5751348  0.2146093  0.5843133
##  [37]  0.9059315 -0.6485545  1.4320627  0.1848071  1.4737110  0.4360490
##  [43]  0.9725128  1.2590489  0.7254634 -0.2703657 -0.2793976  0.2190237
##  [49]  0.3029305  0.9767140  0.3410712  0.5452128  0.9224937 -2.5120870
##  [55] -0.1282411  0.6466672 -1.7489101 -0.3151396  0.5731742 -0.1373263
##  [61]  0.0133330 -0.3454655 -0.0518932  0.6700852  1.4627769 -0.5180330
##  [67] -1.3351926 -1.0748726 -1.6337395 -0.6053337  0.7170154  0.7306954
##  [73] -0.7635696 -2.6277277  2.0766175 -1.7341207 -0.1590706  1.1980618
##  [79] -1.0933136 -0.4738698  1.0581812  1.3796338 -0.5426439 -0.6805589
##  [85] -0.0097974  0.4639341 -0.6660666 -0.3080733 -0.7353092  0.5904313
##  [91]  0.4201862 -1.8555023  0.3661329 -1.3111796 -0.1330539  2.2400643
##  [97]  0.6477478  0.7307337 -0.9588846  0.2032752
```

```
##   [1] -1.326087  0.340137 -1.390867 -0.231084  0.039118 -0.748485  0.931205
##   [8]  0.347411  0.994551 -0.566025 -1.480545 -1.135133 -1.022012  0.244522
##  [15] -0.271657 -0.182268 -0.119983  0.641178 -0.345277  1.616413 -0.966281
##  [22] -0.007818 -1.156400 -0.195813  0.886532  0.750685 -1.160126 -0.015005
##  [29] -0.517561  0.489273 -0.802548  0.661001 -0.019108 -1.514690 -0.268316
##  [36] -0.778861 -0.017904 -1.015424 -0.416102  0.241571  1.964871 -1.070117
##  [43]  0.333889 -1.789796  0.470704  0.327790 -0.467502  0.259586 -0.496457
##  [50] -1.740186  0.816158  0.043124  0.646057  1.406399  0.061370 -0.796482
##  [57] -1.346920 -1.012341 -0.812159  1.236477  0.949237 -1.077247 -0.536517
##  [64] -1.285154 -0.001004  1.339837 -1.550691  0.483909  0.676869 -0.489738
##  [71] -0.173907 -0.381882 -0.673424  0.482493 -1.500090 -0.341350  0.002132
##  [78] -0.364213 -1.972654  1.669030  0.296065  0.661963  0.757487  0.581844
##  [85]  1.104988 -1.134486 -0.710473 -0.118354 -0.881931 -0.570861  0.513938
##  [92] -1.173772  1.704088  0.212111 -0.235968  0.188605  0.950515 -0.294400
##  [99]  0.110958 -0.274730
```

## Write wait_until() which delays execution until a specific time.

## There are three places we could have added a memoise call: why did we choose the one we did?
I guess if you memoised the innermost one, you would just be remembering the URLS, and if you memoised the outermost one you'd be saving the execution of the whole expression, which gets you nowhere


```r
# microbenchmark(list = list(
#   dot_every(10, memoise(delay_by(1, rnorm)))(100),
#   memoise(dot_every(10, delay_by(1, rnorm)))(100),
#   dot_every(10, delay_by(1, memoise(rnorm)))(100)
#      ))
```


## Why is the remember() function inefficient? How could you implement it in more efficient way?
Because recopies the list every time the function is rerun.  You could add an argument for the NUMBER of things you want to remember and then allocate space for them in your list.

## Why does the following code, from stackoverflow, not do what you expect? 

Because the closures don't remember all the different values, just the final one. you need to add `force()`

******



### Create a negative() FO that flips the sign of the output of the function to which it is applied.


```r
negative <- function(f){
  force(f)
  function(...)  -f(...)
}

negative(log)(40)
```

```
## [1] -3.689
```

```r
negative(min)(-5:2)
```

```
## [1] 5
```


## The evaluate package makes it easy to capture all the outputs (results, text, messages, warnings, errors, and plots) from an expression. Create a function like capture_it() that also captures the warnings and errors generated by a function.

shamelessly stealing from [this answer](http://stackoverflow.com/a/3904728)


```r
capture_all <- function(f){
  force(f)
  function(...) {
    out <- list()
    out$ouput <- capture.output(f(...))
    out$error <- tryCatch(f(...),error = function(e) e)
    out$warnin <- tryCatch(f(...),warning = function(w) w)
    out
    }
  }

capture_all(log)(-3)
```

```
## Warning: NaNs produced
## Warning: NaNs produced
```

```
## $ouput
## [1] "[1] NaN"
## 
## $error
## [1] NaN
## 
## $warnin
## <simpleWarning in f(...): NaNs produced>
```


## Create a FO that tracks files created or deleted in the working directory (Hint: use dir() and setdiff().) What other global effects of functions might you want to track?


```r
big_brother <- function(f){
  force(f)
  function(...){
    before <- dir(recursive = TRUE)
    f(...)
    after <- dir(recursive = TRUE)
    created <- setdiff(after, before)
    paste0("you just made ", paste(created, collapse = ", "))
  }
}


big_brother(write_time(f = runif))(1)
```

```
## [1] "you just made "
```

useful also: tracking changes in the global environment? though ideally that wouldn't happen

*****



## Our previous download() function only downloads a single file. How can you use partial() and lapply() to create a function that downloads multiple files at once? What are the pros and cons of using partial() vs. writing a function by hand?


```r
download_file <- function(url, ...) {
  download.file(url, basename(url), ...)
}
lapply(urls, download_file)
```

```
## Error: object 'urls' not found
```

```r
delay_by <- function(delay, f) {
  function(...) {
    Sys.sleep(delay)
    f(...)
  }
}

dot_every <- function(n, f) {
  i <- 1
  function(...) {
    if (i %% n == 0) cat(".")
    i <<- i + 1
    f(...)
  }
}

lapply(urls, dot_every(10, delay_by(1, download_file)))
```

```
## Error: object 'urls' not found
```


## Read the source code for plyr::colwise(). How does the code work? What are colwise()’s three main tasks? How could you make colwise() simpler by implementing each task as a function operator? (Hint: think about partial().)


```r
#plyr::colwise
function (.fun, .cols = true, ...) 
{
    if (!is.function(.cols)) {
        .cols <- as.quoted(.cols)
        filter <- function(df) eval.quoted(.cols, df)
        # extracts .cols from df, whicch is not yet known
    }
    else {
        filter <- function(df) Filter(.cols, df) 
        # applies predicate to columns of df, not yet known
    }
    dots <- list(...)
    function(df, ...) {
        stopifnot(is.data.frame(df))
        df <- strip_splits(df)
        filtered <- filter(df)       # take the filtered df
        if (length(filtered) == 0) 
            return(data.frame())
        out <- do.call("lapply", c(list(filtered, .fun, ...), 
            dots))
        ## previous line is the heart of it. Does lapply on the filtered data, applying .fun, with all the dots past and present
        names(out) <- names(filtered)
        quickdf(out)
    }
}
```

```
## function (.fun, .cols = true, ...) 
## {
##     if (!is.function(.cols)) {
##         .cols <- as.quoted(.cols)
##         filter <- function(df) eval.quoted(.cols, df)
##         # extracts .cols from df, whicch is not yet known
##     }
##     else {
##         filter <- function(df) Filter(.cols, df) 
##         # applies predicate to columns of df, not yet known
##     }
##     dots <- list(...)
##     function(df, ...) {
##         stopifnot(is.data.frame(df))
##         df <- strip_splits(df)
##         filtered <- filter(df)       # take the filtered df
##         if (length(filtered) == 0) 
##             return(data.frame())
##         out <- do.call("lapply", c(list(filtered, .fun, ...), 
##             dots))
##         ## previous line is the heart of it. Does lapply on the filtered data, applying .fun, with all the dots past and present
##         names(out) <- names(filtered)
##         quickdf(out)
##     }
## }
```

* determine if the data frame is to be filtered by name or by predicate
* filter it
* use lapply to run a function over those columns

*do it via partial?*

## Write FOs that convert a function to return a matrix instead of a data frame, or a data frame instead of a matrix. If you understand S3, call them as.data.frame.function() and as.matrix.function().


```r
as.df.fn <- function(f){
  function(...){
    as.data.frame(f(...))
  }
}

as.df.fn(matrix)(1:9, nrow = 3)
```

```
##   V1 V2 V3
## 1  1  4  7
## 2  2  5  8
## 3  3  6  9
```

```r
as.mat.fn <- function(f){
  function(...){
    as.matrix(f(...))
  }
}

obj <- as.mat.fn(data.frame)(one = 1:9, two = rnorm(9))
class(obj)
```

```
## [1] "matrix"
```

```r
obj
```

```
##       one     two
##  [1,]   1  0.6393
##  [2,]   2 -1.9493
##  [3,]   3  0.2030
##  [4,]   4 -0.1474
##  [5,]   5  0.1037
##  [6,]   6 -1.9847
##  [7,]   7  1.4444
##  [8,]   8  0.2952
##  [9,]   9 -0.4131
```

so, why was knowledge of S3 required??


## You’ve seen five functions that modify a function to change its output from one form to another. What are they? Draw a table of the various combinations of types of outputs: what should go in the rows and what should go in the columns? What function operators might you want to write to fill in the missing cells? Come up with example use cases.

I can't think of any of these, other than `Negate`

`base::Negate()`
`plyr::failwith()` 
`capture_it()`
`time_it()`


## Look at all the examples of using an anonymous function to partially apply a function in this and the previous chapter. Replace the anonymous function with partial(). What do you think of the result? Is it easier or harder to read?


```r
ignore <- function(...) NULL
tee <- function(f, on_input = ignore, on_output = ignore) {
  function(...) {
    on_input(...)
    output <- f(...)
    on_output(output)
    output
  }
}

g <- function(x) cos(x) - x
zero <- uniroot(g, c(-5, 5))
show_x <- function(x, ...) cat(sprintf("%+.08f", x), "\n")

library(pryr)

partial(uniroot, interval = c(-5, 5))(g)
```

```
## $root
## [1] 0.7391
## 
## $f.root
## [1] -2.604e-07
## 
## $iter
## [1] 6
## 
## $init.it
## [1] NA
## 
## $estim.prec
## [1] 6.104e-05
```

```r
format_it <- partial(sprintf, "%+.08f")

newline <- partial(cat, "\n")

show_x2 <- function(x) newline(format_it(x))
zero <- uniroot(tee(g, on_output = show_x2), c(-5, 5))
```

```
## 
##  +5.28366219
##  -4.71633781
##  +0.67637474
##  -0.23436269
##  +0.02685676
##  +0.00076012
##  -0.00000026
##  +0.00010189
##  -0.00000026
```

**previous chapter**:


```r
trims <- c(0, 0.1, 0.2, 0.5)
x <- rcauchy(1000)
sapply(trims, function(trim) mean(x, trim = trim))
```

```
## [1]  1.17305  0.03759  0.01918 -0.01883
```

```r
# this seems a bit dirty -- relies on knowing "trim" is 2nd arg
sapply(trims, partial(mean, x = x))
```

```
## [1]  1.17305  0.03759  0.01918 -0.01883
```


```r
boot_df <- function(x) x[sample(nrow(x), rep = T), ]
replace_samp <- partial(sample, replace = TRUE)
boot_df2 <- function(x) x[replace_samp(nrow(x)), ]
#boot_df2(iris)

`s[` <- partial(`[`,i = replace_samp(nrow(x)))
`s[`
```

```
## function (...) 
## replace_samp(nrow(x))[...]
```

```r
disp = function(x) x * 0.0163871
disp2 <- partial(`*`,y = 0.0163871)
disp2(1)
```

```
## [1] 0.01639
```

```r
am = function(x) factor(x, levels = c("auto", "manual"))
am <- partial(factor, levels = c("auto", "manual"))
am(c("auto","auto"))
```

```
## [1] auto auto
## Levels: auto manual
```



*****

## Implement your own version of compose() using Reduce and %o%. For bonus points, do it without calling function.


```r
"%o%" <- compose
compose2 <- partial(Reduce, f = `%o%`)
compose2(c(mean,exp,sqrt))(1:10)
```

```
## [1] 11.55
```

```r
mean(exp(sqrt(1:10)))
```

```
## [1] 11.55
```

```r
(mean %o% exp %o% sqrt)(1:10)
```

```
## [1] 11.55
```


## Extend and() and or() to deal with any number of input functions. Can you do it with Reduce()? Can you keep them lazy (e.g., for and(), the function returns once it sees the first FALSE)?


```r
and <- function(...) {
  lapply(list(...), force)
  fs <- lapply(list(...), match.fun)
  function(x) {
    results <- lapply(fs, function(f) f(x))
    Reduce(`&&`,results)
  }
}

and(is.numeric,is.integer,is.vector)(2L)
```

```
## [1] TRUE
```

```r
and(is.numeric,is.integer,is.vector,is.na)(2L)
```

```
## [1] FALSE
```


## Implement the xor() binary operator. Implement it using the existing xor() function. Implement it as a combination of and() and or(). What are the advantages and disadvantages of each approach? Also think about what you’ll call the resulting function to avoid a clash with the existing xor() function, and how you might change the names of and(), not(), and or() to keep them consistent.


```r
`%xor%` <- function(x, y) xor(x, y) 

TRUE %xor% FALSE
```

```
## [1] TRUE
```

```r
and <- function(f1, f2) {
  force(f1); force(f2)
  function(...) {
    f1(...) && f2(...)
  }
}

or <- function(f1, f2) {
  force(f1); force(f2)
  function(...) {
    f1(...) || f2(...)
  }
}
```



## Above, we implemented boolean algebra for functions that return a logical function. Implement elementary algebra (plus(), minus(), multiply(), divide(), exponentiate(), log()) for functions that return numeric vectors.


```r
maker <- function(f){
  force(f)
  function(...) {
    lapply(list(...), force)
    fs <- lapply(list(...), match.fun)
    function(x) {
      results <- lapply(fs, function(f) f(x))
      Reduce(f,results)
      }
    }
}

add_em <- maker(`+`)
unenclose(add_em)
```

```
## function (...) 
## {
##     lapply(list(...), force)
##     fs <- lapply(list(...), match.fun)
##     function(x) {
##         results <- lapply(fs, function(f) +x)
##         Reduce(`+`, results)
##     }
## }
```

```r
add_em(rnorm,rnorm)(1)
```

```
## [1] -0.5257
```

```r
heres_yer_functions <- list(plus = `+`,
                            minus = `-`,
                            multiply = `*`,
                            divide = `/`,
                            exponentiate = `^`)

yer_functions_are_done <- lapply(heres_yer_functions,maker)

yer_functions_are_done$plus(rnorm,rnorm)(1)
```

```
## [1] 0.1207
```

```r
yer_functions_are_done$divide(rnorm,rnorm)(1)
```

```
## [1] 3.028
```

