<<<<<<< HEAD
<<<<<<< HEAD
# Subsetting
Alathea  
2014-07-09  
=======
=======
>>>>>>> upstream/master
---
title: "Subsetting"
author: "Alathea"
date: '2014-07-09'
output: 
  html_document:
    keep_md: yes
    toc: yes
    theme: united
---
<<<<<<< HEAD
>>>>>>> upstream/master
=======
=======
# Subsetting
Alathea  
2014-07-09  
>>>>>>> 78ea7b204c074194de2800abfb119a3b03135c13
>>>>>>> upstream/master

***

### Discussion Notes

<<<<<<< HEAD

```r
mtcars$cyl == 4 | 6
```

```
##  [1] TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE
## [15] TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE
## [29] TRUE TRUE TRUE TRUE
```

```r
mtcars$cyl == 4 || 6
```

```
## [1] TRUE
```

```r
# this works
mtcars$cyl == 4 | mtcars$cyl == 6
```

```
##  [1]  TRUE  TRUE  TRUE  TRUE FALSE  TRUE FALSE  TRUE  TRUE  TRUE  TRUE
## [12] FALSE FALSE FALSE FALSE FALSE FALSE  TRUE  TRUE  TRUE  TRUE FALSE
## [23] FALSE FALSE FALSE  TRUE  TRUE  TRUE FALSE  TRUE FALSE  TRUE
```

```r
# or this
mtcars$cyl %in% c(4, 6)
```

```
##  [1]  TRUE  TRUE  TRUE  TRUE FALSE  TRUE FALSE  TRUE  TRUE  TRUE  TRUE
## [12] FALSE FALSE FALSE FALSE FALSE FALSE  TRUE  TRUE  TRUE  TRUE FALSE
## [23] FALSE FALSE FALSE  TRUE  TRUE  TRUE FALSE  TRUE FALSE  TRUE
```


```r
# this gives 5 NAs because NA is a logical vector, which gets recycled
x <- 1:5
x[NA]
```

Jenny showed us this:


```r
mod <- lm(mpg ~ wt, data = mtcars)
str(mod, max.level = 1, give.attr = FALSE)
```

```
## List of 12
##  $ coefficients : Named num [1:2] 37.29 -5.34
##  $ residuals    : Named num [1:32] -2.28 -0.92 -2.09 1.3 -0.2 ...
##  $ effects      : Named num [1:32] -113.65 -29.116 -1.661 1.631 0.111 ...
##  $ rank         : int 2
##  $ fitted.values: Named num [1:32] 23.3 21.9 24.9 20.1 18.9 ...
##  $ assign       : int [1:2] 0 1
##  $ qr           :List of 5
##  $ df.residual  : int 30
##  $ xlevels      : Named list()
##  $ call         : language lm(formula = mpg ~ wt, data = mtcars)
##  $ terms        :Classes 'terms', 'formula' length 3 mpg ~ wt
##  $ model        :'data.frame':	32 obs. of  2 variables:
```


```r
# linearize the output and force back into the original structure
df[] <- sample(as.matrix(df))
```

```
## Error: cannot coerce type 'closure' to vector of type 'any'
```

`|` vs. `||`.  The shorter form makes element by element comparisons and the long form compares only the first element of each vector.  Normally the long form is used in `if` statements.

=======
>>>>>>> 78ea7b204c074194de2800abfb119a3b03135c13
***

### Quiz

1. *What does subsetting a vector with: positive integers, negative integers, logical vector, character vector?*

**I guess this means what happens when you subset a vector with one of the above.  A positive integer will select the item at that index and a negative integer will remove the item at that index.  I don't know about the vectors.**

2. *What’s the difference between `[`, `[[` and `$` when applied to a list?*

**Subsetting with `[` will select a list at that location but `[[` selects the item within that list.  `$` works for a named list.**

3. *When should you use `drop = FALSE`?*

**No one knows!**

4. *If x is a matrix, what does `x[] <- 0` do? How is it different to `x <- 0`?*

**Don't know.**

5. *How can you use a named vector to relabel categorical variables?*

**Maybe `names(data_frame) <- names(vector)` or something like that.**

***

### Data Types

#### Atomic Vectors


```r
x <- c(1:10)
x[2]
```

```
## [1] 2
```

```r
<<<<<<< HEAD
<<<<<<< HEAD
x[c(5,9)]
=======
x[c(5, 9)]
>>>>>>> upstream/master
=======
x[c(5,9)]
>>>>>>> upstream/master
```

```
## [1] 5 9
```

```r
x[-c(5:10)]
```

```
## [1] 1 2 3 4
```

```r
<<<<<<< HEAD
<<<<<<< HEAD
x[c(TRUE,FALSE,TRUE,TRUE,FALSE)]
=======
x[c(TRUE, FALSE, TRUE, TRUE, FALSE)]
>>>>>>> upstream/master
=======
x[c(TRUE,FALSE,TRUE,TRUE,FALSE)]
>>>>>>> upstream/master
```

```
## [1] 1 3 4 6 8 9
```

<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
An interesting thing about subsetting with a logical vector is that if the logical vector is shorter than the vector being subsetted, it will act like a pattern and repeat over the entire vector (see above).  I guess this is called recycling.

You can use character vectors to select from a named vector.  I did not know that was possible!


```r
y <- c(1:10)
names(y) <- letters[1:10]
y
```

```
##  a  b  c  d  e  f  g  h  i  j 
##  1  2  3  4  5  6  7  8  9 10
```

<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
#### Matrices and Arrays

Arrays are stored in column order.  If you subset with a number, e.g. `array[2]` it will count down through each item of each column.

#### Data Frames

List-type vs. Matrix type subestting:


```r
df <- data.frame(x = 1:3, y = 3:1, z = letters[1:3])

<<<<<<< HEAD
<<<<<<< HEAD
# There's an important difference if you select a single column:
# matrix subsetting simplifies by default, list subsetting does not.
=======
# There's an important difference if you select a single column: matrix
# subsetting simplifies by default, list subsetting does not.
>>>>>>> upstream/master
=======
# There's an important difference if you select a single column:
# matrix subsetting simplifies by default, list subsetting does not.
>>>>>>> upstream/master
str(df["x"])
```

```
## 'data.frame':	3 obs. of  1 variable:
##  $ x: int  1 2 3
```

```r
<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
str(df[, "x"])
```

```
##  int [1:3] 1 2 3
```

<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
#### S3 vs. S4 Objects

Noooooooooooooooooo!!!

#### Exercises

1. *Fix each of the following common data frame subsetting errors:*


```r
mtcars[mtcars$cyl = 4, ]
# to select values equal to 4, use "=="
mtcars[mtcars$cyl == 4, ]

mtcars[-1:4, ]
# to remove multiple entries, use a vector
mtcars[-c(1:4), ]

mtcars[mtcars$cyl <= 5]
# also need to select the columns
mtcars[mtcars$cyl <= 5, ]

mtcars[mtcars$cyl == 4 | 6, ]
# you have to do this:
mtcars[mtcars$cyl == 4 | mtcars$cyl == 6, ]
```

<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
2. *Why does `x <- 1:5; x[NA]` yield five missing values? Hint: why is it different from `x[NA_real_]`?*

**Not sure but something to do with `NA` being logical?**

3. *What does `upper.tri()` return? How does subsetting a matrix with it work? Do we need any additional subsetting rules to describe its behaviour?*


```r
x <- outer(1:5, 1:5, FUN = "*")
x[upper.tri(x)]
```

<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
**It returns `TRUE` in the upper triangle of the matrix so subsetting with it returns the values in the upper triangle, column by column.**

4. *Why does `mtcars[1:20]` return an error? How does it differ from the similar `mtcars[1:20, ]`?*

**Cuz you didn't tell it which columns to select.**

5. *Implement your own function that extracts the diagonal entries from a matrix (it should behave like `diag(x)` where `x` is a matrix).*

**Whyyyy????**


```r
x <- outer(1:4, 1:5, FUN = "*")
diag(x)
```

```
## [1]  1  4  9 16
```

```r
<<<<<<< HEAD
<<<<<<< HEAD
=======
>>>>>>> upstream/master
get_diag <- function(input_matrix)
{
  n <- min(dim(input_matrix))
  select <- vector()
  for(i in 1:n){
    select[i] <- input_matrix[i,i]
  }
  return(select)
<<<<<<< HEAD
=======

get_diag <- function(input_matrix) {
    n <- min(dim(input_matrix))
    select <- vector()
    for (i in 1:n) {
        select[i] <- input_matrix[i, i]
    }
    return(select)
>>>>>>> upstream/master
=======
>>>>>>> upstream/master
}

get_diag(x)
```

```
## [1]  1  4  9 16
```

<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
6. *What does `df[is.na(df)] <- 0` do? How does it work?*

**It sets `NA` values to 0 by selecting those values (`is.na` generates a data frame with `TRUE` or `FALSE` depending on whether the condition is satisfied)**

***

### Subsetting Operators

Some subset operations are *simplifying* and drop a bunch of information and others are *preserving* and retain the same structure as the original data.

To access a column with a name contained in another variable, use `[[`. e.g.


```r
data <- data.frame(a = c(3:5), b = c(10:12), c = c(89:91))
var <- "a"

data$var
```

```
## NULL
```

```r
data[[var]]
```

```
## [1] 3 4 5
```

<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
The `$` can do partial name matching.  Whoa....dangerous.


```r
data2 <- data.frame(abc = c(3:5), bcd = c(10:12), cde = c(89:91))
data$a
```

```
## [1] 3 4 5
```

<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
#### Exercises

1. *Given a linear model, e.g. `mod <- lm(mpg ~ wt, data = mtcars)`, extract the residual degrees of freedom. Extract the R squared from the model summary (`summary(mod)`)*


```r
mod <- lm(mpg ~ wt, data = mtcars)
str(mod)
```

```
## List of 12
##  $ coefficients : Named num [1:2] 37.29 -5.34
##   ..- attr(*, "names")= chr [1:2] "(Intercept)" "wt"
##  $ residuals    : Named num [1:32] -2.28 -0.92 -2.09 1.3 -0.2 ...
##   ..- attr(*, "names")= chr [1:32] "Mazda RX4" "Mazda RX4 Wag" "Datsun 710" "Hornet 4 Drive" ...
##  $ effects      : Named num [1:32] -113.65 -29.116 -1.661 1.631 0.111 ...
##   ..- attr(*, "names")= chr [1:32] "(Intercept)" "wt" "" "" ...
##  $ rank         : int 2
##  $ fitted.values: Named num [1:32] 23.3 21.9 24.9 20.1 18.9 ...
##   ..- attr(*, "names")= chr [1:32] "Mazda RX4" "Mazda RX4 Wag" "Datsun 710" "Hornet 4 Drive" ...
##  $ assign       : int [1:2] 0 1
##  $ qr           :List of 5
##   ..$ qr   : num [1:32, 1:2] -5.657 0.177 0.177 0.177 0.177 ...
##   .. ..- attr(*, "dimnames")=List of 2
##   .. .. ..$ : chr [1:32] "Mazda RX4" "Mazda RX4 Wag" "Datsun 710" "Hornet 4 Drive" ...
##   .. .. ..$ : chr [1:2] "(Intercept)" "wt"
##   .. ..- attr(*, "assign")= int [1:2] 0 1
##   ..$ qraux: num [1:2] 1.18 1.05
##   ..$ pivot: int [1:2] 1 2
##   ..$ tol  : num 1e-07
##   ..$ rank : int 2
##   ..- attr(*, "class")= chr "qr"
##  $ df.residual  : int 30
##  $ xlevels      : Named list()
##  $ call         : language lm(formula = mpg ~ wt, data = mtcars)
##  $ terms        :Classes 'terms', 'formula' length 3 mpg ~ wt
##   .. ..- attr(*, "variables")= language list(mpg, wt)
##   .. ..- attr(*, "factors")= int [1:2, 1] 0 1
##   .. .. ..- attr(*, "dimnames")=List of 2
##   .. .. .. ..$ : chr [1:2] "mpg" "wt"
##   .. .. .. ..$ : chr "wt"
##   .. ..- attr(*, "term.labels")= chr "wt"
##   .. ..- attr(*, "order")= int 1
##   .. ..- attr(*, "intercept")= int 1
##   .. ..- attr(*, "response")= int 1
##   .. ..- attr(*, ".Environment")=<environment: R_GlobalEnv> 
##   .. ..- attr(*, "predvars")= language list(mpg, wt)
##   .. ..- attr(*, "dataClasses")= Named chr [1:2] "numeric" "numeric"
##   .. .. ..- attr(*, "names")= chr [1:2] "mpg" "wt"
##  $ model        :'data.frame':	32 obs. of  2 variables:
##   ..$ mpg: num [1:32] 21 21 22.8 21.4 18.7 18.1 14.3 24.4 22.8 19.2 ...
##   ..$ wt : num [1:32] 2.62 2.88 2.32 3.21 3.44 ...
##   ..- attr(*, "terms")=Classes 'terms', 'formula' length 3 mpg ~ wt
##   .. .. ..- attr(*, "variables")= language list(mpg, wt)
##   .. .. ..- attr(*, "factors")= int [1:2, 1] 0 1
##   .. .. .. ..- attr(*, "dimnames")=List of 2
##   .. .. .. .. ..$ : chr [1:2] "mpg" "wt"
##   .. .. .. .. ..$ : chr "wt"
##   .. .. ..- attr(*, "term.labels")= chr "wt"
##   .. .. ..- attr(*, "order")= int 1
##   .. .. ..- attr(*, "intercept")= int 1
##   .. .. ..- attr(*, "response")= int 1
##   .. .. ..- attr(*, ".Environment")=<environment: R_GlobalEnv> 
##   .. .. ..- attr(*, "predvars")= language list(mpg, wt)
##   .. .. ..- attr(*, "dataClasses")= Named chr [1:2] "numeric" "numeric"
##   .. .. .. ..- attr(*, "names")= chr [1:2] "mpg" "wt"
##  - attr(*, "class")= chr "lm"
```

```r
<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
(df_mod <- mod[["df.residual"]])
```

```
## [1] 30
```

```r
<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
mod_sum <- summary(mod)
str(mod_sum)
```

```
## List of 11
##  $ call         : language lm(formula = mpg ~ wt, data = mtcars)
##  $ terms        :Classes 'terms', 'formula' length 3 mpg ~ wt
##   .. ..- attr(*, "variables")= language list(mpg, wt)
##   .. ..- attr(*, "factors")= int [1:2, 1] 0 1
##   .. .. ..- attr(*, "dimnames")=List of 2
##   .. .. .. ..$ : chr [1:2] "mpg" "wt"
##   .. .. .. ..$ : chr "wt"
##   .. ..- attr(*, "term.labels")= chr "wt"
##   .. ..- attr(*, "order")= int 1
##   .. ..- attr(*, "intercept")= int 1
##   .. ..- attr(*, "response")= int 1
##   .. ..- attr(*, ".Environment")=<environment: R_GlobalEnv> 
##   .. ..- attr(*, "predvars")= language list(mpg, wt)
##   .. ..- attr(*, "dataClasses")= Named chr [1:2] "numeric" "numeric"
##   .. .. ..- attr(*, "names")= chr [1:2] "mpg" "wt"
##  $ residuals    : Named num [1:32] -2.28 -0.92 -2.09 1.3 -0.2 ...
##   ..- attr(*, "names")= chr [1:32] "Mazda RX4" "Mazda RX4 Wag" "Datsun 710" "Hornet 4 Drive" ...
##  $ coefficients : num [1:2, 1:4] 37.285 -5.344 1.878 0.559 19.858 ...
##   ..- attr(*, "dimnames")=List of 2
##   .. ..$ : chr [1:2] "(Intercept)" "wt"
##   .. ..$ : chr [1:4] "Estimate" "Std. Error" "t value" "Pr(>|t|)"
##  $ aliased      : Named logi [1:2] FALSE FALSE
##   ..- attr(*, "names")= chr [1:2] "(Intercept)" "wt"
##  $ sigma        : num 3.05
##  $ df           : int [1:3] 2 30 2
##  $ r.squared    : num 0.753
##  $ adj.r.squared: num 0.745
##  $ fstatistic   : Named num [1:3] 91.4 1 30
##   ..- attr(*, "names")= chr [1:3] "value" "numdf" "dendf"
##  $ cov.unscaled : num [1:2, 1:2] 0.38 -0.1084 -0.1084 0.0337
##   ..- attr(*, "dimnames")=List of 2
##   .. ..$ : chr [1:2] "(Intercept)" "wt"
##   .. ..$ : chr [1:2] "(Intercept)" "wt"
##  - attr(*, "class")= chr "summary.lm"
```

```r
<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
(rsq_mod <- mod_sum[["r.squared"]])
```

```
## [1] 0.7528
```

<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
### Subsetting and Assignment

Can you combine integer indices with integer NA?


```r
x <- c(1:5)

# this is working OK.
x[c(1, NA)] <- 0
```


<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
```r
data(mtcars)
mtcars[] <- lapply(mtcars, as.integer)
str(mtcars[])
```

```
## 'data.frame':	32 obs. of  11 variables:
##  $ mpg : int  21 21 22 21 18 18 14 24 22 19 ...
##  $ cyl : int  6 6 4 6 8 6 8 4 4 6 ...
##  $ disp: int  160 160 108 258 360 225 360 146 140 167 ...
##  $ hp  : int  110 110 93 110 175 105 245 62 95 123 ...
##  $ drat: int  3 3 3 3 3 2 3 3 3 3 ...
##  $ wt  : int  2 2 2 3 3 3 3 3 3 3 ...
##  $ qsec: int  16 17 18 19 17 20 15 20 22 18 ...
##  $ vs  : int  0 0 1 1 0 1 0 1 1 1 ...
##  $ am  : int  1 1 1 0 0 0 0 0 0 0 ...
##  $ gear: int  4 4 4 3 3 3 3 4 4 4 ...
##  $ carb: int  4 4 1 1 2 1 4 2 2 4 ...
```

```r
<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
mtcars <- lapply(mtcars, as.integer)
str(mtcars)
```

```
## List of 11
##  $ mpg : int [1:32] 21 21 22 21 18 18 14 24 22 19 ...
##  $ cyl : int [1:32] 6 6 4 6 8 6 8 4 4 6 ...
##  $ disp: int [1:32] 160 160 108 258 360 225 360 146 140 167 ...
##  $ hp  : int [1:32] 110 110 93 110 175 105 245 62 95 123 ...
##  $ drat: int [1:32] 3 3 3 3 3 2 3 3 3 3 ...
##  $ wt  : int [1:32] 2 2 2 3 3 3 3 3 3 3 ...
##  $ qsec: int [1:32] 16 17 18 19 17 20 15 20 22 18 ...
##  $ vs  : int [1:32] 0 0 1 1 0 1 0 1 1 1 ...
##  $ am  : int [1:32] 1 1 1 0 0 0 0 0 0 0 ...
##  $ gear: int [1:32] 4 4 4 3 3 3 3 4 4 4 ...
##  $ carb: int [1:32] 4 4 1 1 2 1 4 2 2 4 ...
```

<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
### Applications

Lookup tables:


```r
<<<<<<< HEAD
<<<<<<< HEAD
students <- data.frame(name = c(LETTERS[1:10]),
                       sex = c("m","f","m","f","f","f","f","m","m","f"),
                       student.number = sample(c(3333:8888), 10))
=======
students <- data.frame(name = c(LETTERS[1:10]), sex = c("m", "f", "m", "f", 
    "f", "f", "f", "m", "m", "f"), student.number = sample(c(3333:8888), 10))
>>>>>>> upstream/master
=======
students <- data.frame(name = c(LETTERS[1:10]),
                       sex = c("m","f","m","f","f","f","f","m","m","f"),
                       student.number = sample(c(3333:8888), 10))
>>>>>>> upstream/master
lookup <- c(m = "Male", f = "Female")

lookup[students$sex]
```

```
##        f        m        f        m        m        m        m        f 
## "Female"   "Male" "Female"   "Male"   "Male"   "Male"   "Male" "Female" 
##        f        m 
## "Female"   "Male"
```

<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
Random samples / bootstrap:


```r
(x <- data.frame(a = c(1:9), b = letters[1:9]))
```

```
##   a b
## 1 1 a
## 2 2 b
## 3 3 c
## 4 4 d
## 5 5 e
## 6 6 f
## 7 7 g
## 8 8 h
## 9 9 i
```

```r
<<<<<<< HEAD
<<<<<<< HEAD
x[sample(nrow(x),3),]
=======
x[sample(nrow(x), 3), ]
>>>>>>> upstream/master
=======
x[sample(nrow(x),3),]
>>>>>>> upstream/master
```

```
##   a b
<<<<<<< HEAD
<<<<<<< HEAD
## 3 3 c
## 9 9 i
## 4 4 d
```

=======
## 1 1 a
## 8 8 h
## 9 9 i
>>>>>>> upstream/master
=======
## 2 2 b
## 5 5 e
## 3 3 c
<<<<<<< HEAD
```


>>>>>>> upstream/master
=======
## 9 9 i
## 4 4 d
>>>>>>> 78ea7b204c074194de2800abfb119a3b03135c13
```

>>>>>>> upstream/master
Ordering:


```r
<<<<<<< HEAD
<<<<<<< HEAD
(y <- x[sample(nrow(x),nrow(x)),])
=======
(y <- x[sample(nrow(x), nrow(x)), ])
>>>>>>> upstream/master
=======
(y <- x[sample(nrow(x),nrow(x)),])
>>>>>>> upstream/master
```

```
##   a b
<<<<<<< HEAD
<<<<<<< HEAD
=======
## 3 3 c
## 8 8 h
## 7 7 g
## 5 5 e
## 6 6 f
## 9 9 i
## 2 2 b
## 1 1 a
## 4 4 d
=======
>>>>>>> upstream/master
## 4 4 d
## 7 7 g
## 5 5 e
## 8 8 h
## 9 9 i
## 1 1 a
## 6 6 f
## 2 2 b
## 3 3 c
<<<<<<< HEAD
=======
## 2 2 b
## 6 6 f
## 8 8 h
## 5 5 e
## 9 9 i
## 1 1 a
## 7 7 g
## 3 3 c
## 4 4 d
>>>>>>> upstream/master
=======
>>>>>>> 78ea7b204c074194de2800abfb119a3b03135c13
>>>>>>> upstream/master
```

```r
(y <- y[order(y$a), ])
```

```
##   a b
## 1 1 a
## 2 2 b
## 3 3 c
## 4 4 d
## 5 5 e
## 6 6 f
## 7 7 g
## 8 8 h
## 9 9 i
```

<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
### Exercises

1. *How would you randomly permute the columns of a data frame? (This is an important technique in random forests). Can you simultaneously permute the rows and columns in one step?*


```r
(df <- data.frame(a = c(1:4), b = c(5:8), c = c(9:12), d = c(13:16)))
```

```
##   a b  c  d
## 1 1 5  9 13
## 2 2 6 10 14
## 3 3 7 11 15
## 4 4 8 12 16
```

```r
<<<<<<< HEAD
<<<<<<< HEAD
# randomly permute the columns
df[ , sample(ncol(df), ncol(df))]
```

```
##   a  d b  c
## 1 1 13 5  9
## 2 2 14 6 10
## 3 3 15 7 11
## 4 4 16 8 12
```

```r
# randomize columns and rows simultaneously
# although I doubt this is really happening simultaneously
=======

# randomly permute the columns
df[, sample(ncol(df), ncol(df))]
```

```
##    d  c b a
## 1 13  9 5 1
## 2 14 10 6 2
## 3 15 11 7 3
## 4 16 12 8 4
```

```r

# randomize columns and rows simultaneously although I doubt this is really
# happening simultaneously
>>>>>>> upstream/master
df[sample(nrow(df), nrow(df)), sample(ncol(df), ncol(df))]
=======
# randomly permute the columns
df[ , sample(ncol(df), ncol(df))]
>>>>>>> upstream/master
```

```
<<<<<<< HEAD
<<<<<<< HEAD
##    c  d b a
## 2 10 14 6 2
## 4 12 16 8 4
## 3 11 15 7 3
## 1  9 13 5 1
```

=======
##   b  c a  d
## 1 5  9 1 13
## 3 7 11 3 15
## 2 6 10 2 14
## 4 8 12 4 16
```


>>>>>>> upstream/master
=======
##   b a  c  d
## 1 5 1  9 13
## 2 6 2 10 14
## 3 7 3 11 15
## 4 8 4 12 16
=======
##   a  d b  c
## 1 1 13 5  9
## 2 2 14 6 10
## 3 3 15 7 11
## 4 4 16 8 12
>>>>>>> 78ea7b204c074194de2800abfb119a3b03135c13
```

```r
# randomize columns and rows simultaneously
# although I doubt this is really happening simultaneously
df[sample(nrow(df), nrow(df)), sample(ncol(df), ncol(df))]
```

```
<<<<<<< HEAD
##   a b  c  d
## 2 2 6 10 14
## 1 1 5  9 13
## 3 3 7 11 15
## 4 4 8 12 16
=======
##    c  d b a
## 2 10 14 6 2
## 4 12 16 8 4
## 3 11 15 7 3
## 1  9 13 5 1
>>>>>>> 78ea7b204c074194de2800abfb119a3b03135c13
```

>>>>>>> upstream/master
2. *How would you select a random sample of `m` rows from a data frame? What if the sample had to be contiguous (i.e. with an initial row, a final row, and every row in between)?*


```r
<<<<<<< HEAD
<<<<<<< HEAD
sample_rows <- function(input_df, sample_size)
{
  input_df[sample(nrow(input_df), sample_size, replace = TRUE), ]
=======
sample_rows <- function(input_df, sample_size) {
    input_df[sample(nrow(input_df), sample_size, replace = TRUE), ]
>>>>>>> upstream/master
=======
sample_rows <- function(input_df, sample_size)
{
  input_df[sample(nrow(input_df), sample_size, replace = TRUE), ]
>>>>>>> upstream/master
}

sample_rows(df, 3)
```

```
##     a b  c  d
<<<<<<< HEAD
<<<<<<< HEAD
=======
>>>>>>> upstream/master
## 1   1 5  9 13
<<<<<<< HEAD
## 4   4 8 12 16
## 1.1 1 5  9 13
=======
## 1.1 1 5  9 13
## 4   4 8 12 16
<<<<<<< HEAD
=======
## 3   3 7 11 15
## 3.1 3 7 11 15
## 3.2 3 7 11 15
>>>>>>> upstream/master
=======
>>>>>>> 78ea7b204c074194de2800abfb119a3b03135c13
>>>>>>> upstream/master
```

```r
sample_rows(df, 6)
```

```
##     a b  c  d
<<<<<<< HEAD
<<<<<<< HEAD
=======
## 4   4 8 12 16
## 2   2 6 10 14
## 4.1 4 8 12 16
## 4.2 4 8 12 16
## 3   3 7 11 15
## 4.3 4 8 12 16
=======
>>>>>>> upstream/master
## 1   1 5  9 13
## 2   2 6 10 14
## 3   3 7 11 15
## 4   4 8 12 16
## 1.1 1 5  9 13
## 4.1 4 8 12 16
>>>>>>> 78ea7b204c074194de2800abfb119a3b03135c13
```


```r
sample_contig_rows <- function(input_df)
{
  (row_nums <- sample(nrow(input_df), 2))
  input_df[min(row_nums):max(row_nums), ]
<<<<<<< HEAD
=======
## 4   4 8 12 16
## 2   2 6 10 14
## 2.1 2 6 10 14
## 3   3 7 11 15
## 1   1 5  9 13
## 3.1 3 7 11 15
```



```r
sample_contig_rows <- function(input_df) {
    (row_nums <- sample(nrow(input_df), 2))
    input_df[min(row_nums):max(row_nums), ]
>>>>>>> upstream/master
=======
>>>>>>> upstream/master
}

sample_contig_rows(df)
```

```
##   a b  c  d
<<<<<<< HEAD
=======
## 1 1 5  9 13
>>>>>>> 78ea7b204c074194de2800abfb119a3b03135c13
## 2 2 6 10 14
## 3 3 7 11 15
## 4 4 8 12 16
```

```r
<<<<<<< HEAD
<<<<<<< HEAD
=======
>>>>>>> upstream/master
# if you want a set number of rows:
sample_contig_rows_2 <- function(input_df, sample_size)
{
  # check that the sample size is smaller than the number of rows
  if(sample_size > nrow(input_df)){
    warning("Sample size too large.  Selecting all rows starting from a random index.")
    return(sample_contig_rows(input_df))
  }
  else if(sample_size == nrow(input_df)){
    warning("You just selected the entire data frame!")
    return(input_df)
  }
  else{
    row_max <- nrow(input_df) - sample_size + 1
    first_row <- sample(1:row_max, 1)
    return(input_df[first_row:(first_row + sample_size - 1), ])
  }
<<<<<<< HEAD
=======

# if you want a set number of rows:
sample_contig_rows_2 <- function(input_df, sample_size) {
    # check that the sample size is smaller than the number of rows
    if (sample_size > nrow(input_df)) {
        warning("Sample size too large.  Selecting all rows starting from a random index.")
        return(sample_contig_rows(input_df))
    } else if (sample_size == nrow(input_df)) {
        warning("You just selected the entire data frame!")
        return(input_df)
    } else {
        row_max <- nrow(input_df) - sample_size + 1
        first_row <- sample(1:row_max, 1)
        return(input_df[first_row:(first_row + sample_size - 1), ])
    }
>>>>>>> upstream/master
=======
>>>>>>> upstream/master
}

sample_contig_rows_2(df, 5)
```

```
## Warning: Sample size too large.  Selecting all rows starting from a random
## index.
```

```
##   a b  c  d
<<<<<<< HEAD
<<<<<<< HEAD
## 1 1 5  9 13
## 2 2 6 10 14
=======
## 3 3 7 11 15
## 4 4 8 12 16
>>>>>>> upstream/master
=======
## 1 1 5  9 13
## 2 2 6 10 14
<<<<<<< HEAD
## 3 3 7 11 15
## 4 4 8 12 16
=======
>>>>>>> 78ea7b204c074194de2800abfb119a3b03135c13
>>>>>>> upstream/master
```

```r
sample_contig_rows_2(df, 4)
```

```
## Warning: You just selected the entire data frame!
```

```
##   a b  c  d
## 1 1 5  9 13
## 2 2 6 10 14
## 3 3 7 11 15
## 4 4 8 12 16
```

```r
sample_contig_rows_2(df, 2)
```

```
##   a b  c  d
<<<<<<< HEAD
<<<<<<< HEAD
## 3 3 7 11 15
## 4 4 8 12 16
```


=======
## 2 2 6 10 14
=======
>>>>>>> upstream/master
## 3 3 7 11 15
```


<<<<<<< HEAD
=======
3. *How could you put the columns in a data frame in alphaetical order?*
>>>>>>> upstream/master

>>>>>>> upstream/master
3. *How could you put the columns in a data frame in alphaetical order?*

<<<<<<< HEAD

```r
<<<<<<< HEAD
=======
```r
>>>>>>> upstream/master
(df3a <- df[ ,sample(ncol(df),ncol(df))])
```

```
<<<<<<< HEAD
##    c a b  d
## 1  9 1 5 13
## 2 10 2 6 14
## 3 11 3 7 15
## 4 12 4 8 16
=======
##    c b  d a
## 1  9 5 13 1
## 2 10 6 14 2
## 3 11 7 15 3
## 4 12 8 16 4
>>>>>>> 78ea7b204c074194de2800abfb119a3b03135c13
```

```r
(df3b <- df3a[ ,order(names(df3a))])
<<<<<<< HEAD
=======
(df3a <- df[, sample(ncol(df), ncol(df))])
```

```
##   a  d  c b
## 1 1 13  9 5
## 2 2 14 10 6
## 3 3 15 11 7
## 4 4 16 12 8
```

```r
(df3b <- df3a[, order(names(df3a))])
>>>>>>> upstream/master
=======
>>>>>>> upstream/master
```

```
##   a b  c  d
## 1 1 5  9 13
## 2 2 6 10 14
## 3 3 7 11 15
## 4 4 8 12 16
```
<<<<<<< HEAD
<<<<<<< HEAD
=======

>>>>>>> upstream/master
=======
>>>>>>> upstream/master
