# Andrew -- Functions
Andrew MacDonald  
`r format(Sys.time(), '%d %B, %Y')`  





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
library(pryr)
```

```
## 
## Attaching package: 'pryr'
## 
## The following object is masked from 'package:dplyr':
## 
##     %.%
```

```r
library(magrittr)
```

```
## 
## Attaching package: 'magrittr'
## 
## The following object is masked from 'package:dplyr':
## 
##     %>%
```

## Excercises

### Given a function, like `"mean"`, `match.fun()` lets you find a function. Given a function, can you find its name? Why doesn't that make sense in R?

Because functions don't need to have any names at all, or could have all kinds of names (including aliases), so I can't even imagine how you'd do that.

###  Use `lapply()` and an anonymous function to find the coefficient of variation (the standard deviation divided by the mean) for all columns in the `mtcars` dataset


```r
sapply(mtcars, function(dat) sd(dat) / mean(dat) )
```

```
##    mpg    cyl   disp     hp   drat     wt   qsec     vs     am   gear 
## 0.3000 0.2886 0.5372 0.4674 0.1487 0.3041 0.1001 1.1520 1.2283 0.2001 
##   carb 
## 0.5743
```

### Use `integrate()` and an anonymous function to find the area under the curve for the following functions. Use [Wolfram Alpha](http://www.wolframalpha.com/) to check your answers.

1. `y = x ^ 2 - x`, x in [0, 10]

```r
integrate(function(x) x ^ 2 - x, 0, 10)
```

```
## 283.3 with absolute error < 3.1e-12
```

1. `y = sin(x) + cos(x)`, x in [-$\pi$, $\pi$]


```r
integrate(function(x) sin(x) + cos(x), - pi, pi)
```

```
## 2.616e-16 with absolute error < 6.3e-14
```
**mathworld says this should be 0.00318531. What went wrong?**

1. `y = exp(x) / x`, x in [10, 20]

```r
integrate(function(x) exp(x) / x, 10, 20)
```

```
## 25613160 with absolute error < 2.8e-07
```
**correct**

### Review your own code

Here is an example of a `magrittr` pipeline with a multiline anonymous function in it. is it horrible?

```
ctrl_single_pair %>% 
  group_by(resp) %>%
  do(anova_ord = aov(val ~ treatment, data = .)) %>%
  function(g) {
    g %>% 
      extract2("anova_ord") %>% 
      set_names(g %>% extract2("resp"))
    } %>%
  lapply(TukeyHSD) %>%
  lapply(function(x) x[["treatment"]]) %>%
  lapply(as.data.frame) %>%
  lapply(function(x) data.frame(comparison = row.names(x),x)) %>%
  function(m) {
    comps <- rbind_all(m)
    r <- names(m) %>% rep(times = sapply(m,nrow) %>% as.numeric)
    data.frame(r,comps)
    } %>% 
  select(response = r, comparison:p.adj) %>%
  mutate(response = response %>% as.character %>% trtnames[.]) %>%
  xtable %>% 
  print(include.rownames = FALSE, size = 8, comment = FALSE)
```

## Exercises 2
  
