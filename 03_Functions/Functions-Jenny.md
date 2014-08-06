# Jenny's reading of 03_Functions
Jenny Bryan  
17 July, 2014  



Week 02 2014-07-10 we read [Functions](http://adv-r.had.co.nz/Functions.html). Source is found [here on github](https://github.com/hadley/adv-r/blob/master/Functions.rmd).

## Taking the quiz

*I made myself enter these answers before reading the chapter, most especially before reading the answers. I annotated/corrected my original answers as I read on.*

1.  What are the three components of a function?

  * the formal arguments
  * the parent environment
  * the function body? return value?
  
*Not bad! "The three components of a function are its body, arguments and environment."*

1.  What does the following code return?

    
    ```r
    y <- 10
    f1 <- function(x) {
      function() {
        x + 10
      }
    }
    f1(1)()
    ```
    
    * Hmmm ... 11?
    
*Correct.*

1.  How would you more typically write this code?

    
    ```r
    `+`(1, `*`(2, 3))
    ```
    
    * `(2 * 3) + 1`

*Correct.*
    
1.  How could you make this call easier to read?

    
    ```r
    mean(, TRUE, x = c(1:10, NA))
    ```

  * `mean(c(1:10, NA), na.rm = TRUE)`

*Correct.*

1.  Does the following function throw an error when called? Why/why not?

    
    ```r
    f2 <- function(a, b) {
      a * 10
    }
    f2(10, stop("This is an error!"))
    ```

  * No, because lazy evaluation implies that `b` will only be evaluated when it comes up in the function. Which it does not.
  
*Correct.*

1.  What is an infix function? How do you write it? What's a replacement 
    function? How do you write it?
    
    * Not sure about the infix thing.
    * A replacement function requires making an assignment to the `[<-` operator, or something like that.

*Well, he sort of punted on the answer as well and sends the reader to sections on [infix](#infix-functions) and [replacement functions](#replacement-functions). I'll look forward to reading them.*

1.  What function do you use to ensure that a cleanup action occurs 
    regardless of how a function terminates?
    
    * `on.exit()`
    
*Correct.*

## Function components -- Exercises

1.  What function allows you to tell if an object is a function? What function
    allows you to tell if a function is a primitive function?
    

```r
str(sum)
```

```
## function (..., na.rm = FALSE)
```

```r
get("sum")
```

```
## function (..., na.rm = FALSE)  .Primitive("sum")
```

```r
is.function(sum)
```

```
## [1] TRUE
```

```r
is.primitive(sum)
```

```
## [1] TRUE
```

1.  This code makes a list of all functions in the base package. 
    
    
    ```r
    objs <- mget(ls("package:base"), inherits = TRUE)
    funs <- Filter(is.function, objs)
    ```

    Use it to answer the following questions:

    1. Which base function has the most arguments?
    
    
    ```r
    str(funs, max.level = 1, list.len = 6)
    ```
    
    ```
    ## List of 1167
    ##  $ -                                 :function (e1, e2)  
    ##  $ -.Date                            :function (e1, e2)  
    ##  $ -.POSIXt                          :function (e1, e2)  
    ##  $ :                                 :.Primitive(":") 
    ##  $ ::                                :function (pkg, name)  
    ##  $ :::                               :function (pkg, name)  
    ##   [list output truncated]
    ```
    
    ```r
    n_args <- sapply(funs, function(x) length(formals(x)))
    the_one <- which.max(n_args)
    names(funs)[the_one]
    ```
    
    ```
    ## [1] "scan"
    ```
    
    ```r
    length(formals(funs[[the_one]]))
    ```
    
    ```
    ## [1] 22
    ```
    
    1. How many base functions have no arguments? What's special about those
       functions.
       

```r
       table(n_args, useNA = "always")
```

```
## n_args
##    0    1    2    3    4    5    6    7    8    9   10   11   12   14   16 
##  221  192  341  187   85   81   29   12    8    3    2    1    2    1    1 
##   22 <NA> 
##    1    0
```

```r
       length(no_args <- which(n_args == 0))
```

```
## [1] 221
```

```r
       funs[head(no_args)]
```

```
## $`-`
## function (e1, e2)  .Primitive("-")
## 
## $`:`
## .Primitive(":")
## 
## $`!`
## function (x)  .Primitive("!")
## 
## $`!=`
## function (e1, e2)  .Primitive("!=")
## 
## $`(`
## .Primitive("(")
## 
## $`[`
## .Primitive("[")
```

```r
       addmargins(table(n_args, sapply(funs, is.primitive)))
```

```
##       
## n_args FALSE TRUE  Sum
##    0      39  182  221
##    1     192    0  192
##    2     341    0  341
##    3     187    0  187
##    4      85    0   85
##    5      81    0   81
##    6      29    0   29
##    7      12    0   12
##    8       8    0    8
##    9       3    0    3
##    10      2    0    2
##    11      1    0    1
##    12      2    0    2
##    14      1    0    1
##    16      1    0    1
##    22      1    0    1
##    Sum   985  182 1167
```
       
       They are mostly primitive functions.
       
    1. How could you adapt the code to find all primitive functions?


```r
str(prims <-lapply(funs, function(x) if(is.primitive(x)) x else NULL),
    list.len = 6)
```

```
## List of 1167
##  $ -                                 :function (e1, e2)  
##  $ -.Date                            : NULL
##  $ -.POSIXt                          : NULL
##  $ :                                 :.Primitive(":") 
##  $ ::                                : NULL
##  $ :::                               : NULL
##   [list output truncated]
```

```r
prims[sapply(prims, is.null)] <- NULL
str(prims, list.len = 6)
```

```
## List of 182
##  $ -              :function (e1, e2)  
##  $ :              :.Primitive(":") 
##  $ !              :function (x)  
##  $ !=             :function (e1, e2)  
##  $ (              :.Primitive("(") 
##  $ [              :.Primitive("[") 
##   [list output truncated]
```

1. What are the three important components of a function?

Body, environment, arguments.

1. When does printing a function not show what environment it was created in?

When the function was created in the global environment.

## Lexical scoping 

### Dynamic lookup

This is good to know about: `codetools::findGlobals(f)`. Will show your function's external dependencies.

### Exercises

1. What does the following code return? Why? What does each of the three `c`'s mean?

    
    ```r
    c <- 10
    c(c = c)
    ```

You get a numeric vector with a single element, holding the value 10, named `c`.


```r
c(c = c)
```

```
## $c
## function (..., recursive = FALSE)  .Primitive("c")
```

```r
str(c(c = c))
```

```
## List of 1
##  $ c:function (..., recursive = FALSE)
```

2. What are the four principles that govern how R looks for values?

  * name masking
  * functions vs. variables
  * a fresh start
  * dynamic lookup

3. What does the following function return? Make a prediction before 
   running the code yourself.

    
    ```r
    f <- function(x) {
      f <- function(x) {
        f <- function(x) {
          x ^ 2
        }
        f(x) + 1
      }
      f(x) * 2
    }
    f(10)
    ```

I predict this returns `((10 ^ 2) + 1) * 2 = 202`. And it does.

## Function arguments

> Arguments are matched first by exact name (perfect matching), then by prefix matching and finally by position.

### Calling a function given a list of arguments

Here's a use of `do.call()` that's good to be reminded of:

> Suppose you had a list of function arguments:


```r
args <- list(1:10, na.rm = TRUE)
```

> How could you then send that list to `mean()`?  You need `do.call()`:


```r
do.call(mean, list(1:10, na.rm = TRUE))
```

```
## [1] 5.5
```

```r
# Equivalent to
mean(1:10, na.rm = TRUE)
```

```
## [1] 5.5
```

### Default and missing arguments

The claim is that "Since arguments in R are evaluated lazily (more on that below), the default value can be defined in terms of other arguments". __But then why doesn't this work?__


```r
data.frame(x = 1:3, y = x^2)
```

```
## Error: object 'x' not found
```

Good to remember that `missing()` returns a logical re: whether an argument was supplied at call time.

Good to know his strategy when the default value is more involved to compute than your typical default value in a function definition. Here's what he does: "I usually set the default value to NULL and use `is.null()` to check if the argument was supplied".

### Lazy evaluation

Good to know: "If you want to ensure that an argument is evaluated you can use `force`".

*I didn't really understand the example used to illustrate forcing. "At this point, the loop is complete and the final value of x is 10. Therefore all of the adder functions will add 10 on to their input". That confuses me because it seems to conflict with this idea that a fresh copy of a function's environment is created at call time. See if others want to discuss.*

*The promise section hasn't totally sunk in yet. At least partially due to lack of examples and use cases. Is this relevant to my life? See if others want to discuss.*

### `...`

Good reminder that "To capture ... in a form that is easier to work with, you can use `list(...)`."

### Exercises

1.  Clarify the following list of odd function calls:

    
    ```r
    x <- sample(replace = TRUE, 20, x = c(1:10, NA))
    y <- runif(min = 0, max = 1, 20)
    cor(m = "k", y = y, u = "p", x = x)
    ```

`sample()` call: We'll be taking a sample with replacement from `x`, which is `c(1:10, NA)`, of size 20. Better code: `sample(c(1:10, NA), size = 20, replace = TRUE)`.

`runif()`call: We'll be drawing 20 random uniform variates. Better code: `runif(20)`. Pet peeve of mine: people who specify arguments at their default values. Especially for functions where the default are blindingly obvious. It's like crying "Wolf!".

`cor()` call: We're computing Kendall's tau statistic for `(x, y)`, as defined in the calling environment, for pairwise complete observations. Better code: `cor(x, y, use = "pairwise.complete.obs", method = "kendall")`. I believe in spelling out arguments and values that I don't call everyday. The clarity is worth the strenuous typing.

1.  What does this function return? Why? Which principle does it illustrate?
  
    
    ```r
    f1 <- function(x = {y <- 1; 2}, y = 0) {
      x + y
    }
    f1()
    ```

I predict it will return `(x = 2) + (y = 1 (was 0 to begin)) = 3`. And it does. This illustrates lazy evaluation.

1.  What does this function return? Why? Which principle does it illustrate?

    
    ```r
    f2 <- function(x = z) {
      z <- 100
      x
    }
    f2()
    ```
    
I predict it will return 100. And it does. Illustrates dynamic lookup and lazy evaluation.
    
## Special calls

### Infix functions

Good to know: "All user created infix functions names must start and end with `%` and R comes with the following infix functions predefined: `%%`, `%*%`, `%/%`, `%in%`, `%o%`,  `%x%`. (The complete list of built-in infix operators that don't need `%` is: `::, :::, $, @, ^, *, /, +, -, >, >=, <, <=, ==, !=, !, &, &&, |, ||, ~, <-, <<-`)".

This would be good to discuss further: "There's one infix function that I use very often. It's inspired by Ruby's `||` logical or operator, although it works a little differently in R because Ruby has a more flexible definition of what evaluates to `TRUE` in an if statement. It's useful as a way of providing a default value in case the output of another function is `NULL`:""


```r
`%||%` <- function(a, b) if (!is.null(a)) a else b
function_that_might_return_null() %||% default value
```

What's the use case for this?

### Exercises

1. Create a list of all the replacement functions found in the base package. 
   Which ones are primitive functions?
   

```r
objs <- mget(ls("package:base"), inherits = TRUE)
funs <- Filter(is.function, objs)
repl_funs <- funs[grepl("<-$", names(funs))]
str(repl_funs, max.level = 1, list.len = 6)
```

```
## List of 34
##  $ [[<-            :.Primitive("[[<-") 
##  $ [<-             :.Primitive("[<-") 
##  $ @<-             :.Primitive("@<-") 
##  $ <-              :.Primitive("<-") 
##  $ <<-             :.Primitive("<<-") 
##  $ $<-             :.Primitive("$<-") 
##   [list output truncated]
```

```r
prim_repl_funs <- Filter(is.primitive, repl_funs)
str(prim_repl_funs, max.level = 1, list.len = 6)
```

```
## List of 17
##  $ [[<-          :.Primitive("[[<-") 
##  $ [<-           :.Primitive("[<-") 
##  $ @<-           :.Primitive("@<-") 
##  $ <-            :.Primitive("<-") 
##  $ <<-           :.Primitive("<<-") 
##  $ $<-           :.Primitive("$<-") 
##   [list output truncated]
```

2. What are valid names for user created infix functions?

"All user created infix functions names must start and end with `%`".

__I AM HERE!__

3. Create an infix `xor()` operator.

4. Create infix versions of the set functions `intersect()`, `union()`, and 
   `setdiff()`.

5. Create a replacement function that modifies a random location in a vector.

## Return values
