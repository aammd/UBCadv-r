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

### W

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



#### Does the following function throw an error when called?  Why/why not?

```r
f2 <- function(a, b) {
  a * 10
}
f2(10, stop("This is an error!"))
```



#### What is an infix function? How do you write it? What’s a replacement function? How do you write it?



#### What function do you use to ensure that a cleanup action occurs regardless of how a function terminates?


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

## Discussion Notes

***
