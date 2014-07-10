# Jenny's reading of 02_Subsetting
Jenny Bryan  
10 July, 2014  



Week 02 2014-07-10 we read [Subsetting](http://adv-r.had.co.nz/Subsetting.html).

## Taking the quiz

*I made myself enter these answers before reading the chapter, most especially before reading the answers. I annotated/corrected my original answers as I read on.*

#### What does subsetting a vector with: positive integers, negative integers, logical vector, character vector?

  * Explained via examples:
    - `foo[c(1, 3, 5)]` gets elements 1, 3, and 5
    - `foo[-c(2, 4)]` gets all elements except 2 and 4
    - `foo[c(TRUE, FALSE)]` gets every other element, starting with 1, 3, ...
    - `foo[c("jan", "mar")]` gets the elements named `jan` and `mar`
    
*Nailed it.*

#### What’s the difference between `[`, `[[` and `$` when applied to a list?

  * `[` is vector style indexing and provides access to more than one element; it will return a list
  * `[[` is list style indexing and provides access to a single element; it will return whatever that element is
  * `$` is also list style indexing and provides access to a single element
  * difference between `[[` and `$`? with `[[` you must quote element names and you can index with an R object that holds the element name; with `$` you give the name unquoted, e.g. `iris$Sepal.Length`

*Yup.*

#### When should you use `drop = FALSE`?

  * Use `drop = FALSE` if you need a guarantee that you retain all dimensions, even if the extent of a dimension goes to zero or one.

*Correct.*

#### If `x` is a matrix, what does `x[] <- 0` do? How is it different to `x <- 0`?

  * `x[] <- 0` will fill every element of `x` with zero as a double
  * `x <- 0` will make `x` into a double vector of length holding the value 0

*Swish!*

#### How can you use a named vector to relabel categorical variables?

  * I know how to do this with `plyr::revalue()`. The `replace =` argument takes a named character vector. Names are the outgoing/old values and values are the incoming/new values. So `"this" = "that"` will cause all instances of `this` to be replaced with `that`. Was he looking for a base R solution?

*Not the answer he had in mind. Here's the base R answer: "A named character vector can act as a simple lookup table: `c(x = 1, y = 2, z = 3)[c("y", "z", "x")]`". I'm not completely sure how this even answers the question. Seems like we're missing some steps and context.*

*Overall, I did very well on the quiz. Don't plan to take many notes on this chapter.*

## Working the exercises re: Data types

#### Fix each of the following common data frame subsetting errors:

  * `mtcars[mtcars$cyl = 4, ]` *Replace the single `=` with double `==`.*
  * `mtcars[-1:4, ]` *Indexing vector mixes negative and positive integers which is a no-no; if goal is to drop elements 1 through 4 surround with parentheses, i.e. `mtcars[-(1:4), ]`.*
  * `mtcars[mtcars$cyl <= 5]` *This is vector-style indexing of data frame; I assume user meant to type `mtcars[mtcars$cyl <= 5, ]`.*
  * `mtcars[mtcars$cyl == 4 | 6, ]` *Sadly, you can't use `|` like that, but beware because this will NOT produce an error. It just won't return rows with cylinder equal to 4 or 6; in fact, it returns all the rows. Why? Because the combination of coercion and recycling implies that `6` will become a logical vector ot `TRUE`s of length equal to the rows in `mtcars`. How to fix? Pick one of these options: `mtcars[mtcars$cyl %in% c(4, 6), ]` or `mtcars[mtcars$cyl == 4 | mtcars$cyl == 6, ]`*
  * Why does `x <- 1:5; x[NA]` yield five missing values? Hint: why is it different from `x[NA_real_]?` *`NA` is a logical vector of length one. It's perfectly OK to index by a logical vector. Recycling will expand this to 5 logical `NA`s. And then indexing by `NA` always gives back an `NA`, so you get five of them. Indexing by `NA_real_` is indexing by a double. It will get truncated and be as if you're indexing by the integer `1`.*

#### What does `upper.tri()` return? How does subsetting a matrix with it work? Do we need any additional subsetting rules to describe its behaviour?


```r
x <- outer(1:5, 1:5, FUN = "*")
x[upper.tri(x)]
```

```
##  [1]  2  3  6  4  8 12  5 10 15 20
```

  * `upper.tri()` "returns a matrix of logicals the same size of a given matrix with entries TRUE in the lower or upper triangle." By indexing `x` with the return value of `x[upper.tri(x)]`, we're actually seeing vector-style indexing. The matrix of logicals doesn't really stay a matrix but becomes an atomic logical vector. That is then used to index the matrix contents, also as an atomic logical vector. Which explains why the return value is an atomic vector.  

#### Why does `mtcars[1:20]` return a error? How does it differ from the similar `mtcars[1:20, ]`?

  * Don't forget data frames are lists, not matrices. When you index a data frame vector style, with `[`, you'll index it as a list. Since `mtcars` does not have 20 variables, asking for variables 1 through 20 will trigger an error about selecting "undefined columns". If you indexed a matrix this way, the indexing would go into "vector mode" and you'd get the first 20 elements, even if that grabbed values from multiple columns. This doesn't happen -- and doesn't make sense -- with a data frame, since there's no guarantee that the contituent variables even have the same type.

#### Implement your own function that extracts the diagonal entries from a matrix (it should behave like `diag(x)` where `x` is a matrix).


```r
jFun <- function(x) {
  stopifnot(is.matrix(x), nrow(x) == ncol(x))
  n <- nrow(x)
  return(x[matrix(seq_len(n), nrow = n, ncol = 2)])
}
n <- 4
(x <- matrix(1:(n^2), nrow = n))
```

```
##      [,1] [,2] [,3] [,4]
## [1,]    1    5    9   13
## [2,]    2    6   10   14
## [3,]    3    7   11   15
## [4,]    4    8   12   16
```

```r
jFun(x)
```

```
## [1]  1  6 11 16
```

```r
identical(jFun(x), diag(x))
```

```
## [1] TRUE
```

```r
(z <- matrix(letters[1:5], nrow = 3, ncol = 5))
```

```
##      [,1] [,2] [,3] [,4] [,5]
## [1,] "a"  "d"  "b"  "e"  "c" 
## [2,] "b"  "e"  "c"  "a"  "d" 
## [3,] "c"  "a"  "d"  "b"  "e"
```

```r
jFun(z)
```

```
## Error: nrow(x) == ncol(x) is not TRUE
```

#### What does `df[is.na(df)] <- 0` do? How does it work?


```r
n <- 4
x <- matrix(1:(n^2), nrow = n)
(df <- as.data.frame(x))
```

```
##   V1 V2 V3 V4
## 1  1  5  9 13
## 2  2  6 10 14
## 3  3  7 11 15
## 4  4  8 12 16
```

```r
df[rbind(c(2, 2), c(4, 3))] <- NA
df
```

```
##   V1 V2 V3 V4
## 1  1  5  9 13
## 2  2 NA 10 14
## 3  3  7 11 15
## 4  4  8 NA 16
```

```r
df[is.na(df)] <- 0
df
```

```
##   V1 V2 V3 V4
## 1  1  5  9 13
## 2  2  0 10 14
## 3  3  7 11 15
## 4  4  8  0 16
```

```r
str(is.na(df))
```

```
##  logi [1:4, 1:4] FALSE FALSE FALSE FALSE FALSE FALSE ...
##  - attr(*, "dimnames")=List of 2
##   ..$ : NULL
##   ..$ : chr [1:4] "V1" "V2" "V3" "V4"
```

  * `df[is.na(df)] <- 0` replaces `NA`s in the data frame `df` with 0's, element-wise. `is.na(df)` returns a logical matrix, of same dimensions as `df`, with `TRUE`s for non-missing data and `FALSE`s for missing data. Then indexing `df` itself by this isolates the missing bits and replaces them with 0.

## Subsetting operators

Great tweet:

>  "If list `x` is a train carrying objects, then `x[[5]]` is
> the object in car 5; `x[4:6]` is a train of cars 4-6." ---
> @[RLangTip](http://twitter.com/#!/RLangTip/status/118339256388304896)

Remember that `$` does partial matching, whereas `[[` does not. I am not a fan of partial matching. Be explicit. Use autocompletion to help with all that strenuous typing.

Andrew was really excited by this re: indexing via `[[` with a vector:


```r
# If you do supply a vector it indexes recursively
b <- list(a = list(b = list(c = list(d = 1))))
b[[c("a", "b", "c", "d")]]
```

```
## [1] 1
```

```r
# Same as
b[["a"]][["b"]][["c"]][["d"]]
```

```
## [1] 1
```
I had kinda glossed over that, but his usage made me appreciate this. It can be nice way to get something that is an element of a list that is itself an element of a list. Easier to see in an example:


```r
mod <- lm(mpg ~ wt, data = mtcars)
str(mod, max.level = 2, give.attr = FALSE)
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
##   ..$ qr   : num [1:32, 1:2] -5.657 0.177 0.177 0.177 0.177 ...
##   ..$ qraux: num [1:2] 1.18 1.05
##   ..$ pivot: int [1:2] 1 2
##   ..$ tol  : num 1e-07
##   ..$ rank : int 2
##  $ df.residual  : int 30
##  $ xlevels      : Named list()
##  $ call         : language lm(formula = mpg ~ wt, data = mtcars)
##  $ terms        :Classes 'terms', 'formula' length 3 mpg ~ wt
##  $ model        :'data.frame':	32 obs. of  2 variables:
##   ..$ mpg: num [1:32] 21 21 22.8 21.4 18.7 18.1 14.3 24.4 22.8 19.2 ...
##   ..$ wt : num [1:32] 2.62 2.88 2.32 3.21 3.44 ...
```

```r
mod[[c("qr", "rank")]]
```

```
## [1] 2
```
This goes into the fitted model, gets the `qr` list element, and then goes into that to retrieve the `rank` element. Cleaner than `mod[["qr"]][["rank"]]`.

### Working the exercises

#### Given a linear model, e.g. `mod <- lm(mpg ~ wt, data = mtcars)`, extract the residual degrees of freedom. Extract the R squared from the model summary (`summary(mod)`).


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
mod$df.residual
```

```
## [1] 30
```

```r
mod_sum <- summary(mod)
str(mod_sum, max.level = 1, give.attr = FALSE)
```

```
## List of 11
##  $ call         : language lm(formula = mpg ~ wt, data = mtcars)
##  $ terms        :Classes 'terms', 'formula' length 3 mpg ~ wt
##  $ residuals    : Named num [1:32] -2.28 -0.92 -2.09 1.3 -0.2 ...
##  $ coefficients : num [1:2, 1:4] 37.285 -5.344 1.878 0.559 19.858 ...
##  $ aliased      : Named logi [1:2] FALSE FALSE
##  $ sigma        : num 3.05
##  $ df           : int [1:3] 2 30 2
##  $ r.squared    : num 0.753
##  $ adj.r.squared: num 0.745
##  $ fstatistic   : Named num [1:3] 91.4 1 30
##  $ cov.unscaled : num [1:2, 1:2] 0.38 -0.1084 -0.1084 0.0337
```

```r
mod_sum$r.sq
```

```
## [1] 0.7528
```

## Subsetting and assignment

Good to remember that assigning when subsetting with `[]` preserves original object class and structure.

I didn't know (had never thought about) how to add a literal `NULL` to a list:


```r
y <- list(a = 1)
y["b"] <- list(NULL)
str(y)
```

```
## List of 2
##  $ a: num 1
##  $ b: NULL
```

## Applications

### Lookup tables (character subsetting)

Clever. I think I've done this but never really registered that this was achieving lookup table functionality via character subsetting.

Idea: you've got an original character vector. You want to replace, e.g., all `m`s in it with `male`. Create a lookup vector with an element with name `m` and value `male`. Index the original with the lookup vector. However, relative to `car::recode()` and `plyr::revalue()` and `plyr::mapvalues()`, this base R trick requires you to be exhaustive in your lookup vector, i.e. you've got to address each unique value that appears in the original. I think I'll still use the convenience wrappers.


```r
x <- c("m", "f", "u", "f", "f", "m", "m")
lookup <- c(m = "Male", f = "Female", u = NA)
lookup[x]
```

```
##        m        f        u        f        f        m        m 
##   "Male" "Female"       NA "Female" "Female"   "Male"   "Male"
```

```r
lookup <- c(m = "Male")
lookup[x]
```

```
##      m   <NA>   <NA>   <NA>   <NA>      m      m 
## "Male"     NA     NA     NA     NA "Male" "Male"
```

### Removing columns from data frame

Good way to drop the variable `z` from a data.frame:

```
df[setdiff(names(df), "z")]
```

### Selecting rows based on a condition

Nice to know about [De Morgan's laws](http://en.wikipedia.org/wiki/De_Morgan's_laws), which can be useful to simplify negations:

  * `!(X & Y)` is the same as `!X | !Y`
  * `!(X | Y)` is the same as `!X & !Y`

For example, `!(X & !(Y | Z))` simplifies to `!X | !!(Y|Z)`, and then to `!X | Y | Z`.

### Working the exercises

#### How would you randomly permute the columns of a data frame? (This is an important technique in random forests). Can you simultaneously permute the rows and columns in one step?


```r
jMat <-
  as.data.frame(outer(as.character(1:4), as.character(1:4),
                      function(x, y) paste('x', x, y, sep = "")))
dimnames(jMat) <- list(paste0("row", seq_len(nrow(jMat))),
                       paste0("col", seq_len(length(jMat))))
jMat
```

```
##      col1 col2 col3 col4
## row1  x11  x12  x13  x14
## row2  x21  x22  x23  x24
## row3  x31  x32  x33  x34
## row4  x41  x42  x43  x44
```

```r
## permute the columns of jMat
jMat[sample(length(jMat))]
```

```
##      col4 col2 col1 col3
## row1  x14  x12  x11  x13
## row2  x24  x22  x21  x23
## row3  x34  x32  x31  x33
## row4  x44  x42  x41  x43
```

```r
## permute rows and columns in one step -- my original solution
jMat[sample(length(jMat))][sample(nrow(jMat)), ]
```

```
##      col2 col1 col3 col4
## row3  x32  x31  x33  x34
## row4  x42  x41  x43  x44
## row2  x22  x21  x23  x24
## row1  x12  x11  x13  x14
```

```r
## solution after seeing Andrew's and Alathea's
jMat[sample(nrow(jMat)), sample(length(jMat))]
```

```
##      col2 col4 col1 col3
## row2  x22  x24  x21  x23
## row4  x42  x44  x41  x43
## row1  x12  x14  x11  x13
## row3  x32  x34  x31  x33
```

I had worried (but not constructively enough to test!) that shuffling rows and columns at the same time would violate the integrity of rows and columns. But it does not.

#### How would you select a random sample of `m` rows from a data frame? What if the sample had to be contiguous (i.e. with an initial row, a final row, and every row in between)?


```r
## unconstrained sample of `m` rows
m <- 2
jMat[sample(nrow(jMat), size = m), ]
```

```
##      col1 col2 col3 col4
## row4  x41  x42  x43  x44
## row3  x31  x32  x33  x34
```

```r
## sample a clump of `m` rows
jMat[sample(nrow(jMat) - m + 1, size = 1) + (0:(m - 1)), ]
```

```
##      col1 col2 col3 col4
## row1  x11  x12  x13  x14
## row2  x21  x22  x23  x24
```
    
#### How could you put the columns in a data frame in alphaetical order?


```r
names(jMat) <- month.abb[seq_len(length(jMat))]
jMat
```

```
##      Jan Feb Mar Apr
## row1 x11 x12 x13 x14
## row2 x21 x22 x23 x24
## row3 x31 x32 x33 x34
## row4 x41 x42 x43 x44
```

```r
jMat[order(names(jMat))]
```

```
##      Apr Feb Jan Mar
## row1 x14 x12 x11 x13
## row2 x24 x22 x21 x23
## row3 x34 x32 x31 x33
## row4 x44 x42 x41 x43
```

