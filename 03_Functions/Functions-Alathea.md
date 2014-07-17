# Functions
Alathea  
2014-07-16  

***

## Exercises

### What function allows you to tell if an object is a function? What function allows you to tell if a function is a primitive function?

`str` or `is.function` will identify functions.  `is.primitive` tells whether or not a function is primitive.

### This code makes a list of all functions in the base package.


```r
objs <- mget(ls("package:base"), inherits = TRUE)
funs <- Filter(is.function, objs)
```

### Use it to answer the following questions:

#### Which base function has the most arguments?

The arguments can be obtained using the function `formals`.


```r
require(plyr)
```

```
## Loading required package: plyr
```

```r
# create a vector of lengths
arg_length <- laply(funs, function(x)(length(formals(x))))

# find the max
max_args <- which(arg_length == max(arg_length))

# get the name of the function
names(funs[max_args])
```

```
## [1] "scan"
```

#### How many base functions have no arguments? What’s special about those functions.


```r
length(which(arg_length == 0))
```

```
## [1] 221
```

#### How could you adapt the code to find all primitive functions?


```r
prims <- Filter(is.primitive, objs)
names(prims)
```

```
##   [1] "^"               "~"               "<"              
##   [4] "<<-"             "<="              "<-"             
##   [7] "="               "=="              ">"              
##  [10] ">="              "|"               "||"             
##  [13] "-"               ":"               "!"              
##  [16] "!="              "/"               "("              
##  [19] "["               "[<-"             "[["             
##  [22] "[[<-"            "{"               "@"              
##  [25] "@<-"             "$"               "$<-"            
##  [28] "*"               "&"               "&&"             
##  [31] "%/%"             "%*%"             "%%"             
##  [34] "+"               "abs"             "acos"           
##  [37] "acosh"           "all"             "any"            
##  [40] "anyNA"           "Arg"             "as.call"        
##  [43] "as.character"    "as.complex"      "as.double"      
##  [46] "as.environment"  "asin"            "asinh"          
##  [49] "as.integer"      "as.logical"      "as.numeric"     
##  [52] "as.raw"          "atan"            "atanh"          
##  [55] "attr"            "attr<-"          "attributes"     
##  [58] "attributes<-"    "baseenv"         "break"          
##  [61] "browser"         "c"               "call"           
##  [64] "ceiling"         "class"           "class<-"        
##  [67] "Conj"            "cos"             "cosh"           
##  [70] "cospi"           "cummax"          "cummin"         
##  [73] "cumprod"         "cumsum"          "digamma"        
##  [76] "dim"             "dim<-"           "dimnames"       
##  [79] "dimnames<-"      "emptyenv"        "enc2native"     
##  [82] "enc2utf8"        "environment<-"   "exp"            
##  [85] "expm1"           "expression"      "floor"          
##  [88] "for"             "function"        "gamma"          
##  [91] "gc.time"         "globalenv"       "if"             
##  [94] "Im"              "interactive"     "invisible"      
##  [97] "is.array"        "is.atomic"       "is.call"        
## [100] "is.character"    "is.complex"      "is.double"      
## [103] "is.environment"  "is.expression"   "is.finite"      
## [106] "is.function"     "is.infinite"     "is.integer"     
## [109] "is.language"     "is.list"         "is.logical"     
## [112] "is.matrix"       "is.na"           "is.name"        
## [115] "is.nan"          "is.null"         "is.numeric"     
## [118] "is.object"       "is.pairlist"     "is.raw"         
## [121] "is.recursive"    "isS4"            "is.single"      
## [124] "is.symbol"       "lazyLoadDBfetch" "length"         
## [127] "length<-"        "levels<-"        "lgamma"         
## [130] "list"            "log"             "log10"          
## [133] "log1p"           "log2"            "max"            
## [136] "min"             "missing"         "Mod"            
## [139] "names"           "names<-"         "nargs"          
## [142] "next"            "nzchar"          "oldClass"       
## [145] "oldClass<-"      "on.exit"         "pos.to.env"     
## [148] "proc.time"       "prod"            "quote"          
## [151] "range"           "Re"              "rep"            
## [154] "repeat"          "retracemem"      "return"         
## [157] "round"           "seq_along"       "seq.int"        
## [160] "seq_len"         "sign"            "signif"         
## [163] "sin"             "sinh"            "sinpi"          
## [166] "sqrt"            "standardGeneric" "storage.mode<-" 
## [169] "substitute"      "sum"             "switch"         
## [172] "tan"             "tanh"            "tanpi"          
## [175] "tracemem"        "trigamma"        "trunc"          
## [178] "unclass"         "untracemem"      "UseMethod"      
## [181] "while"           "xtfrm"
```

### What are the three important components of a function?

`formals()`: the arguments
`body()`: the function content
`environment()`: the environment where the variables are stored

### When does printing a function not show what environment it was created in?

If the function has a custom print method, or if it is a primitive function.

### What does the following code return? Why? What does each of the three c’s mean?


```r
c <- 10
c(c = c)
```

The first `c()` runs the combine function to create a vector with an item named `c` with value 10

### What are the four principles that govern how R looks for values?

name masking, functions vs. variables, a fresh start, dynamic lookup

### What does the following function return? Make a prediction before running the code yourself.


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

The function will go down to the lowest level and find `x ^ 2` so `f(x) <- 100`. Next, R will arrive at the command `f(x) + 1` and get `100 + 1 = 101` and finally multiply by two to get `202`

### Clarify the following list of odd function calls:

#### `x <- sample(replace = TRUE, 20, x = c(1:10, NA))`

`x <- sample(c(1:10, NA), 20, replace = TRUE)`

#### `y <- runif(min = 0, max = 1, 20)`

`y <- runif(20, 0, 1)`

#### `cor(m = "k", y = y, u = "p", x = x)`

`cor(x, y, use = "pairwise.complete.obs", method = "kendall")`

### What does this function return? Why? Which principle does it illustrate?


```r
f1 <- function(x = {y <- 1; 2}, y = 0) {
  x + y
}
f1()
```

### What does this function return? Why? Which principle does it illustrate?


```r
f2 <- function(x = z) {
  z <- 100
  x
}
f2()
```

```
## [1] 100
```

### Create a list of all the replacement functions found in the base package. Which ones are primitive functions?


```r
require(plyr)

objs <- mget(ls("package:base"), inherits = TRUE)
prims <- Filter(is.primitive, objs)
```

### What are valid names for user created infix functions?

Anything surrounded in `%%` e.g. `%my_function%`

### Create an infix `xor()` operator.


```r
xor(TRUE, FALSE)
```

```
## [1] TRUE
```

```r
xor(TRUE, TRUE)
```

```
## [1] FALSE
```

```r
`%xor%` <- function(x, y)
{
  xor(x, y)
}

TRUE %xor% TRUE
```

```
## [1] FALSE
```

```r
TRUE %xor% FALSE
```

```
## [1] TRUE
```

### Create infix versions of the set functions `intersect()`, `union()`, and `setdiff()`.


```r
a <- seq(5, 6)
b <- seq(6, 7)

`%=%` <- function(x, y)
{
  intersect(x, y)
}

`%+%` <- function(x, y)
{
  union(x, y)
}

`%-%` <- function(x, y)
{
  setdiff(x, y)
}

a %=% b
```

```
## [1] 6
```

```r
a %+% b
```

```
## [1] 5 6 7
```

```r
a %-% b
```

```
## [1] 5
```

### Create a replacement function that modifies a random location in a vector.


```r
`timestwo<-` <- function(x, value = 2 * x[index])
{
  index <- sample(1:length(x), 1)
  x[index] <- value
  x
}

# for some reason, R is not finding this function
a <- seq(1:10)
#timestwo(a)
```

### How does the `chdir` parameter of `source()` compare to `in_dir()`? Why might you prefer one approach to the other?

`chdir` temporarily changes the working directory from within the `source()` function.

I can't even find `in_dir()`

### What function undoes the action of `library()`? How do you save and restore the values of `options()` and `par()`?

`detach` will remove the library.

`options()` and `par()` work the same weird way as `setwd()`.  You can save them while also changing them.  So `old <- options(newopts)` will actually save the old options while also setting the new ones.

### Write a function that opens a graphics device, runs the supplied code, and closes the graphics device (always, regardless of whether or not the plotting code worked).


```r
plotter <- function()
{
  x <- seq(1:10)
  y <- runif(10)
  plot(x, y)
  on.exit(dev.off())
}

plotter()
```

### We can use `on.exit()` to implement a simple version of `capture.output()`.


```r
capture.output2 <- function(code) {
  temp <- tempfile()
  on.exit(file.remove(temp), add = TRUE)
  
  sink(temp)
  on.exit(sink(), add = TRUE)
  
  force(code)
  readLines(temp)
}

capture.output2(cat("a", "b", "c", sep = "\n"))
#> [1] "a" "b" "c"
```

### You might want to compare this function to the real `capture.output()` and think about the simplifications I’ve made. Is the code easier to understand or harder? Have I removed important functionality?



### Compare `capture.output()` to `capture.output2()`. How do the functions differ? What features have I removed to make the key ideas easier to see? How have I rewritten the key ideas to be easier to understand?


***

## Reading Notes

### Quiz

#### What are the three components of a function?

body, formals, environment

#### What does the following code return?


```r
y <- 10
f1 <- function(x) {
  function() {
    x + 10
  }
}
f1(1)()
```

11

#### How would you more typically write this code?
**`` `+`(1, `*`(2, 3))``**

I think `1 + (2 * 3)`

#### How could you make this call easier to read?
**`mean(, TRUE, x = c(1:10, NA))`**

`mean(c(1:10), na.rm = TRUE)`

#### Does the following function throw an error when called?  Why/why not?

```r
f2 <- function(a, b) {
  a * 10
}
f2(10, stop("This is an error!"))
```

No, because the function is evaluated lazily so R never gets around to using `b` before completing evaluation.  You could force evaluation of `b` and then cause the error.

#### What is an infix function? How do you write it? What’s a replacement function? How do you write it?

Infix: It comes in between its arguments, like `+`.  You can make your own by surrounding it with `%%`

Replacement: They seem to modify their own argument.  The are defined like `xxx<-`.  They create a copy of the argument and then modify it, so don't use if you could lose a lot of performance by doing so.

#### What function do you use to ensure that a cleanup action occurs regardless of how a function terminates?

`on.exit()`

### Function components

body, formals, environment

functions can have a custom print method (as in modeling functions)

primitive functions do not have the three components; different methods are used to match arguments:
* `switch` = chooses an argument based on a given expression
* `call`

### Lexical scoping

components of lexical scoping:

* name masking
* functions vs. variables
* a fresh start
* dynamic lookup

if a function cannot find the value of a variable, it will check the next level up and so on

the same behavior is present when R is looking for a function

a fresh environment is created every time a function is run; R does not remember what happened the last time the function was run

`findGlobals()` from the package `codetools` is cool b/c you can look up the external dependencies of your functions




***

### Function arguments

order of argument matching:

1. exact name
2. prefix
3. position

`do.call()`: supply a function with a list of arguments

e.g. `do.call(mean, list(1:10, na.trim = TRUE))`

`missing()`: was the argument supplied?

or set the default to `NULL` and use `is.null()`

R only uses the function arguments it needs, unless you use `force()`

**promise**: and unevaluated argument

### Return Values

functions can have invisible return values which are displayed by wrapping `( )`

Clean up actions can be set with `on.exit()` but multiple calls of the function will override each other unless `add = TRUE` is set.

## Discussion Notes

***
