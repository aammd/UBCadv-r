# Environments
Alathea  
2014-07-21  

***

## Exercises

### List three ways in which an environment differs from a list.

* The environment has a parent
* Objects in an environment have no order
* Every object in an environment has to have a unique name

### If you don’t supply an explicit environment, where do `ls()` and `rm()` look? Where does `<-` make bindings?

They look in the current environment (usually `globalenv()`) and that is also where `<-` makes bindings.  Within a functions, the bindings are made in the function's environment.

### Using `parent.env()` and a loop (or a recursive function), verify that the ancestors of `globalenv()` include `baseenv()` and `emptyenv()`. Use the same basic idea to implement your own version of `search()`.


```r
ancestors <- function(environ = globalenv())
{
  p <- parent.env(environ)
  print(environmentName(p))
  
  if(identical(p, emptyenv())) return(invisible())
  else ancestors(p)
}

ancestors()
```

```
## [1] "package:stats"
## [1] "package:graphics"
## [1] "package:grDevices"
## [1] "package:utils"
## [1] "package:datasets"
## [1] "package:methods"
## [1] "Autoloads"
## [1] "base"
## [1] "R_EmptyEnv"
```

### Modify `where()` to find all environments that contain a binding for `name`.

I'm not entirely sure I understand the question, but:


```r
my_where <- function(env = parent.frame()) {
  if (identical(env, emptyenv())) {
    # Base case
    stop("Can't find 'name'", call. = FALSE)
  } else if (exists("name", envir = env, inherits = FALSE)) {
    # Success case
    env 
  } else {
    # Recursive case
    my_where(parent.env(env))
  }
}

name <- "Alathea"
my_where()
```

```
## <environment: R_GlobalEnv>
```

### Write your own version of `get()` using a function written in the style of `where()`.


```r
my_get <- function(obj_name, env = parent.frame(), recursive = TRUE)
{
  if (exists(obj_name, envir = env, inherits = FALSE)) {
    eval(parse(text = obj_name))
  } else if (!recursive) {
    stop("Can't find ", obj_name, " in ", environmentName(env), call. = FALSE)
  } else if (identical(env, emptyenv())) {
    stop(obj_name, " does not exist", call. = FALSE)
  } else my_get(obj_name, parent.env(env))
}

name <- "Alathea"

get("name")
```

```
## [1] "Alathea"
```

```r
my_get("name")
```

```
## [1] "Alathea"
```

### Write a function called `fget()` that finds only function objects. It should have two arguments, `name` and `env`, and should obey the regular scoping rules for functions: if there’s an object with a matching name that’s not a function, look in the parent. For an added challenge, also add an inherits argument which controls whether the function recurses up the parents or only looks in one environment.


```r
fget <- function(name, env, recursive = FALSE){
  g <- get(name)
  if(!is.function(g)) stop("The 'name' argument must specify a function.")
  return(g)
}

fget("sum")
```

```
## function (..., na.rm = FALSE)  .Primitive("sum")
```

### Write your own version of `exists(inherits = FALSE)` (Hint: use `ls()`). Write a recursive version that behaves like `exists(inherits = TRUE)`s.


```r
my_exists_local <- function(name)
{
  if(!is.character(name)) stop("'name' must be a character string.  Please try again.")
  
  name_list <- ls(envir = parent.frame())
  
  if(any(name_list == name)) {
    print(paste("Yes it exists and the value is:", name, sep = " "))
  } else {
    print("No it doesn't exist.")
  }
}

my_exists_global <- function(name, environ = parent.frame())
{
  if(!is.character(name)) stop("'name' must be a character string.  Please try again.")
  
  name_list <- ls(envir = environ)
  if(identical(environ, emptyenv())) {
    print("No it doesnt exist.")
  } else if(any(name_list == name)) {
    print(paste("Yes it exists and the value is:", get(name), sep = " "))
  } else {
    environ = parent.env(environ)
    my_exists_global(name, environ)
  }
}

assign("do_i_exist", "yes i do exist.", envir = baseenv())

my_exists_local("do_i_exist")
```

```
## [1] "No it doesn't exist."
```

```r
my_exists_global("do_i_exist")
```

```
## [1] "Yes it exists and the value is: yes i do exist."
```

### Write an enhanced version of `str()` that provides more information about functions. Show where the function was found and what environment it was defined in.


```r
str_plus <- function(fun)
{
  if(!is.function(fun)) stop("Input must be a function.")
 
  require(pryr)
  
  output <- list(structure = str(fun), 
                 binding_env = where(as.character(substitute(fun))), 
                 enclosing_env = environment(fun)
                 )
  output  
}

str_plus(str_plus)
```

```
## function (fun)  
##  - attr(*, "srcref")=Class 'srcref'  atomic [1:8] 1 13 12 1 13 1 1 12
##   .. ..- attr(*, "srcfile")=Classes 'srcfilecopy', 'srcfile' <environment: 0x9f58184>
```

```
## $structure
## NULL
## 
## $binding_env
## <environment: R_GlobalEnv>
## 
## $enclosing_env
## <environment: R_GlobalEnv>
```

### What does this function do? How does it differ from `<<-` and why might you prefer it?


```r
rebind <- function(name, value, env = parent.frame()) {
  if (identical(env, emptyenv())) {
    stop("Can't find ", name, call. = FALSE)
  } else if (exists(name, envir = env, inherits = FALSE)) {
    assign(name, value, envir = env)
  } else {
    rebind(name, value, parent.env(env))
  }
}
rebind("a", 10)
#> Error: Can't find a
a <- 5
rebind("a", 10)
a
#> [1] 10
```

This allows you to bind a value outside of the function, while having control over the environment.  `<<-` will either replace an existing value or bind in the global environment.

### Create a version of `assign()` that will only bind new names, never re-bind old names. Some programming languages only do this, and are known as single assignment laguages.


```r
single_assign <- function(name, value, env = globalenv())
{
  if(!is.character(name)) stop("`name` must be a character string.")
  if(exists(name, inherits = TRUE)) stop("This name has already been used.")
  
  assign(name, value, envir = env)
  return(invisible())
}

single_assign("assign01", "hi")
single_assign("assign01", "bye")
```

### Write an assignment function that can do active, delayed, and locked bindings. What might you call it? What arguments should it take? Can you guess which sort of assignment it should do based on the input?


```r
flex_assign <- function(name)
{
  
}
```

***

## Reading Notes

### Quiz

#### List three ways that an environment is different to a list.

from Hadley:

> * Every object in an environment has a unique name.
> * The objects in an environment are not ordered (i.e. it doesn’t make sense to ask what the first object in an environment is).
> * An environment has a parent.
> * Environments have reference semantics.

#### What is the parent of the global environment? What is the only environment that doesn’t have a parent?

The parent of the global environment is the package that was attached last.  The **empty** environment (`emptyenv`) has no parent.

#### What is the enclosing environment of a function? Why is it important?

#### How do you determine the environment from which a function was called?

`parent.frame()`

#### How are `<-` and `<<-` different?

`<-` sets a value in the current environment. `<<-` sets the value in all environments (i think)

### Environment basics

Variables can point to objects that are equivalent but stored in different places in the environment.  This could be a cause of reduced performance if they are pointing to two different giant objects.

Objects that have lost their name are automatically deleted by the garbage collector.

Use `search()` to get all the parents of the global environment.  Use `ls()` to show all of the bindings in the current environment or a selected environment. Use `ls.str()` to get the structure of the environment.


```r
search()
```

```
##  [1] ".GlobalEnv"        "package:pryr"      "package:stats"    
##  [4] "package:graphics"  "package:grDevices" "package:utils"    
##  [7] "package:datasets"  "package:methods"   "Autoloads"        
## [10] "package:base"
```

```r
ls()
```

```
##  [1] "ancestors"        "fget"             "flex_assign"     
##  [4] "metadata"         "my_exists_global" "my_exists_local" 
##  [7] "my_get"           "my_where"         "name"            
## [10] "str_plus"
```

```r
ls(baseenv())
```

```
##    [1] "^"                                 
##    [2] "~"                                 
##    [3] "<"                                 
##    [4] "<<-"                               
##    [5] "<="                                
##    [6] "<-"                                
##    [7] "="                                 
##    [8] "=="                                
##    [9] ">"                                 
##   [10] ">="                                
##   [11] "|"                                 
##   [12] "||"                                
##   [13] "-"                                 
##   [14] ":"                                 
##   [15] "::"                                
##   [16] ":::"                               
##   [17] "!"                                 
##   [18] "!="                                
##   [19] "/"                                 
##   [20] "("                                 
##   [21] "["                                 
##   [22] "[<-"                               
##   [23] "[["                                
##   [24] "[[<-"                              
##   [25] "{"                                 
##   [26] "@"                                 
##   [27] "@<-"                               
##   [28] "$"                                 
##   [29] "$<-"                               
##   [30] "*"                                 
##   [31] "&"                                 
##   [32] "&&"                                
##   [33] "%/%"                               
##   [34] "%*%"                               
##   [35] "%%"                                
##   [36] "+"                                 
##   [37] "abbreviate"                        
##   [38] "abs"                               
##   [39] "acos"                              
##   [40] "acosh"                             
##   [41] "addNA"                             
##   [42] "addTaskCallback"                   
##   [43] "agrep"                             
##   [44] "agrepl"                            
##   [45] "alist"                             
##   [46] "all"                               
##   [47] "all.equal"                         
##   [48] "all.equal.character"               
##   [49] "all.equal.default"                 
##   [50] "all.equal.factor"                  
##   [51] "all.equal.formula"                 
##   [52] "all.equal.language"                
##   [53] "all.equal.list"                    
##   [54] "all.equal.numeric"                 
##   [55] "all.equal.POSIXt"                  
##   [56] "all.equal.raw"                     
##   [57] "all.names"                         
##   [58] "all.vars"                          
##   [59] "any"                               
##   [60] "anyDuplicated"                     
##   [61] "anyDuplicated.array"               
##   [62] "anyDuplicated.data.frame"          
##   [63] "anyDuplicated.default"             
##   [64] "anyDuplicated.matrix"              
##   [65] "anyNA"                             
##   [66] "anyNA.numeric_version"             
##   [67] "anyNA.POSIXlt"                     
##   [68] "aperm"                             
##   [69] "aperm.default"                     
##   [70] "aperm.table"                       
##   [71] "append"                            
##   [72] "apply"                             
##   [73] "Arg"                               
##   [74] "args"                              
##   [75] "array"                             
##   [76] "arrayInd"                          
##   [77] "as.array"                          
##   [78] "as.array.default"                  
##   [79] "as.call"                           
##   [80] "as.character"                      
##   [81] "as.character.condition"            
##   [82] "as.character.Date"                 
##   [83] "as.character.default"              
##   [84] "as.character.error"                
##   [85] "as.character.factor"               
##   [86] "as.character.hexmode"              
##   [87] "as.character.numeric_version"      
##   [88] "as.character.octmode"              
##   [89] "as.character.POSIXt"               
##   [90] "as.character.srcref"               
##   [91] "as.complex"                        
##   [92] "as.data.frame"                     
##   [93] "as.data.frame.array"               
##   [94] "as.data.frame.AsIs"                
##   [95] "as.data.frame.character"           
##   [96] "as.data.frame.complex"             
##   [97] "as.data.frame.data.frame"          
##   [98] "as.data.frame.Date"                
##   [99] "as.data.frame.default"             
##  [100] "as.data.frame.difftime"            
##  [101] "as.data.frame.factor"              
##  [102] "as.data.frame.integer"             
##  [103] "as.data.frame.list"                
##  [104] "as.data.frame.logical"             
##  [105] "as.data.frame.matrix"              
##  [106] "as.data.frame.model.matrix"        
##  [107] "as.data.frame.numeric"             
##  [108] "as.data.frame.numeric_version"     
##  [109] "as.data.frame.ordered"             
##  [110] "as.data.frame.POSIXct"             
##  [111] "as.data.frame.POSIXlt"             
##  [112] "as.data.frame.raw"                 
##  [113] "as.data.frame.table"               
##  [114] "as.data.frame.ts"                  
##  [115] "as.data.frame.vector"              
##  [116] "as.Date"                           
##  [117] "as.Date.character"                 
##  [118] "as.Date.date"                      
##  [119] "as.Date.dates"                     
##  [120] "as.Date.default"                   
##  [121] "as.Date.factor"                    
##  [122] "as.Date.numeric"                   
##  [123] "as.Date.POSIXct"                   
##  [124] "as.Date.POSIXlt"                   
##  [125] "as.difftime"                       
##  [126] "as.double"                         
##  [127] "as.double.difftime"                
##  [128] "as.double.POSIXlt"                 
##  [129] "as.environment"                    
##  [130] "as.expression"                     
##  [131] "as.expression.default"             
##  [132] "as.factor"                         
##  [133] "as.function"                       
##  [134] "as.function.default"               
##  [135] "as.hexmode"                        
##  [136] "asin"                              
##  [137] "asinh"                             
##  [138] "as.integer"                        
##  [139] "[.AsIs"                            
##  [140] "as.list"                           
##  [141] "as.list.data.frame"                
##  [142] "as.list.Date"                      
##  [143] "as.list.default"                   
##  [144] "as.list.environment"               
##  [145] "as.list.factor"                    
##  [146] "as.list.function"                  
##  [147] "as.list.numeric_version"           
##  [148] "as.list.POSIXct"                   
##  [149] "as.logical"                        
##  [150] "as.logical.factor"                 
##  [151] "as.matrix"                         
##  [152] "as.matrix.data.frame"              
##  [153] "as.matrix.default"                 
##  [154] "as.matrix.noquote"                 
##  [155] "as.matrix.POSIXlt"                 
##  [156] "as.name"                           
##  [157] "asNamespace"                       
##  [158] "as.null"                           
##  [159] "as.null.default"                   
##  [160] "as.numeric"                        
##  [161] "as.numeric_version"                
##  [162] "as.octmode"                        
##  [163] "as.ordered"                        
##  [164] "as.package_version"                
##  [165] "as.pairlist"                       
##  [166] "as.POSIXct"                        
##  [167] "as.POSIXct.Date"                   
##  [168] "as.POSIXct.date"                   
##  [169] "as.POSIXct.dates"                  
##  [170] "as.POSIXct.default"                
##  [171] "as.POSIXct.numeric"                
##  [172] "as.POSIXct.POSIXlt"                
##  [173] "as.POSIXlt"                        
##  [174] "as.POSIXlt.character"              
##  [175] "as.POSIXlt.Date"                   
##  [176] "as.POSIXlt.date"                   
##  [177] "as.POSIXlt.dates"                  
##  [178] "as.POSIXlt.default"                
##  [179] "as.POSIXlt.factor"                 
##  [180] "as.POSIXlt.numeric"                
##  [181] "as.POSIXlt.POSIXct"                
##  [182] "as.qr"                             
##  [183] "as.raw"                            
##  [184] "asS3"                              
##  [185] "asS4"                              
##  [186] "assign"                            
##  [187] "as.single"                         
##  [188] "as.single.default"                 
##  [189] "as.symbol"                         
##  [190] "as.table"                          
##  [191] "as.table.default"                  
##  [192] "as.vector"                         
##  [193] "as.vector.factor"                  
##  [194] "atan"                              
##  [195] "atan2"                             
##  [196] "atanh"                             
##  [197] "attach"                            
##  [198] "attachNamespace"                   
##  [199] "attr"                              
##  [200] "attr<-"                            
##  [201] "attr.all.equal"                    
##  [202] "attributes"                        
##  [203] "attributes<-"                      
##  [204] "autoload"                          
##  [205] "autoloader"                        
##  [206] "backsolve"                         
##  [207] "baseenv"                           
##  [208] "basename"                          
##  [209] "besselI"                           
##  [210] "besselJ"                           
##  [211] "besselK"                           
##  [212] "besselY"                           
##  [213] "beta"                              
##  [214] "bindingIsActive"                   
##  [215] "bindingIsLocked"                   
##  [216] "bindtextdomain"                    
##  [217] "bitwAnd"                           
##  [218] "bitwNot"                           
##  [219] "bitwOr"                            
##  [220] "bitwShiftL"                        
##  [221] "bitwShiftR"                        
##  [222] "bitwXor"                           
##  [223] "body"                              
##  [224] "body<-"                            
##  [225] "bquote"                            
##  [226] "break"                             
##  [227] "browser"                           
##  [228] "browserCondition"                  
##  [229] "browserSetDebug"                   
##  [230] "browserText"                       
##  [231] "builtins"                          
##  [232] "by"                                
##  [233] "by.data.frame"                     
##  [234] "by.default"                        
##  [235] "bzfile"                            
##  [236] "c"                                 
##  [237] "call"                              
##  [238] "callCC"                            
##  [239] "capabilities"                      
##  [240] "casefold"                          
##  [241] "cat"                               
##  [242] "cbind"                             
##  [243] "cbind.data.frame"                  
##  [244] "c.Date"                            
##  [245] "ceiling"                           
##  [246] "character"                         
##  [247] "char.expand"                       
##  [248] "charmatch"                         
##  [249] "charToRaw"                         
##  [250] "chartr"                            
##  [251] "check_tzones"                      
##  [252] "chol"                              
##  [253] "chol2inv"                          
##  [254] "chol.default"                      
##  [255] "choose"                            
##  [256] "class"                             
##  [257] "class<-"                           
##  [258] "clearPushBack"                     
##  [259] "close"                             
##  [260] "closeAllConnections"               
##  [261] "close.connection"                  
##  [262] "close.srcfile"                     
##  [263] "close.srcfilealias"                
##  [264] "c.noquote"                         
##  [265] "c.numeric_version"                 
##  [266] "col"                               
##  [267] "colMeans"                          
##  [268] "colnames"                          
##  [269] "colnames<-"                        
##  [270] "colSums"                           
##  [271] "commandArgs"                       
##  [272] "comment"                           
##  [273] "comment<-"                         
##  [274] "complex"                           
##  [275] "computeRestarts"                   
##  [276] "conditionCall"                     
##  [277] "conditionCall.condition"           
##  [278] "conditionMessage"                  
##  [279] "conditionMessage.condition"        
##  [280] "conflicts"                         
##  [281] "Conj"                              
##  [282] "contributors"                      
##  [283] "cos"                               
##  [284] "cosh"                              
##  [285] "cospi"                             
##  [286] "c.POSIXct"                         
##  [287] "c.POSIXlt"                         
##  [288] "crossprod"                         
##  [289] "Cstack_info"                       
##  [290] "cummax"                            
##  [291] "cummin"                            
##  [292] "cumprod"                           
##  [293] "cumsum"                            
##  [294] "cut"                               
##  [295] "cut.Date"                          
##  [296] "cut.default"                       
##  [297] "cut.POSIXt"                        
##  [298] "data.class"                        
##  [299] "[<-.data.frame"                    
##  [300] "[.data.frame"                      
##  [301] "[[<-.data.frame"                   
##  [302] "[[.data.frame"                     
##  [303] "$<-.data.frame"                    
##  [304] "$.data.frame"                      
##  [305] "data.frame"                        
##  [306] "data.matrix"                       
##  [307] "-.Date"                            
##  [308] "[<-.Date"                          
##  [309] "[.Date"                            
##  [310] "[[.Date"                           
##  [311] "+.Date"                            
##  [312] "date"                              
##  [313] "debug"                             
##  [314] "debugonce"                         
##  [315] "default.stringsAsFactors"          
##  [316] "delayedAssign"                     
##  [317] "deparse"                           
##  [318] "det"                               
##  [319] "detach"                            
##  [320] "determinant"                       
##  [321] "determinant.matrix"                
##  [322] "dget"                              
##  [323] "diag"                              
##  [324] "diag<-"                            
##  [325] "diff"                              
##  [326] "diff.Date"                         
##  [327] "diff.default"                      
##  [328] "diff.POSIXt"                       
##  [329] "difftime"                          
##  [330] "/.difftime"                        
##  [331] "[.difftime"                        
##  [332] "*.difftime"                        
##  [333] "digamma"                           
##  [334] "dim"                               
##  [335] "dim<-"                             
##  [336] "dim.data.frame"                    
##  [337] "dimnames"                          
##  [338] "dimnames<-"                        
##  [339] "dimnames<-.data.frame"             
##  [340] "dimnames.data.frame"               
##  [341] "dir"                               
##  [342] "dir.create"                        
##  [343] "dirname"                           
##  [344] "$.DLLInfo"                         
##  [345] "do.call"                           
##  [346] "do_i_exist"                        
##  [347] "dontCheck"                         
##  [348] "double"                            
##  [349] "dput"                              
##  [350] "dQuote"                            
##  [351] "drop"                              
##  [352] "droplevels"                        
##  [353] "droplevels.data.frame"             
##  [354] "droplevels.factor"                 
##  [355] "dump"                              
##  [356] "duplicated"                        
##  [357] "duplicated.array"                  
##  [358] "duplicated.data.frame"             
##  [359] "duplicated.default"                
##  [360] "duplicated.matrix"                 
##  [361] "duplicated.numeric_version"        
##  [362] "duplicated.POSIXlt"                
##  [363] "dyn.load"                          
##  [364] "dyn.unload"                        
##  [365] "eapply"                            
##  [366] "eigen"                             
##  [367] "emptyenv"                          
##  [368] "enc2native"                        
##  [369] "enc2utf8"                          
##  [370] "encodeString"                      
##  [371] "Encoding"                          
##  [372] "Encoding<-"                        
##  [373] "enquote"                           
##  [374] "environment"                       
##  [375] "environment<-"                     
##  [376] "environmentIsLocked"               
##  [377] "environmentName"                   
##  [378] "env.profile"                       
##  [379] "eval"                              
##  [380] "eval.parent"                       
##  [381] "evalq"                             
##  [382] "exists"                            
##  [383] "exp"                               
##  [384] "expand.grid"                       
##  [385] "expm1"                             
##  [386] "expression"                        
##  [387] "F"                                 
##  [388] "factor"                            
##  [389] "[<-.factor"                        
##  [390] "[.factor"                          
##  [391] "[[<-.factor"                       
##  [392] "[[.factor"                         
##  [393] "factorial"                         
##  [394] "fifo"                              
##  [395] "file"                              
##  [396] "file.access"                       
##  [397] "file.append"                       
##  [398] "file.choose"                       
##  [399] "file.copy"                         
##  [400] "file.create"                       
##  [401] "file.exists"                       
##  [402] "file.info"                         
##  [403] "file.link"                         
##  [404] "file.path"                         
##  [405] "file.remove"                       
##  [406] "file.rename"                       
##  [407] "file.show"                         
##  [408] "file.symlink"                      
##  [409] "Filter"                            
##  [410] "Find"                              
##  [411] "findInterval"                      
##  [412] "find.package"                      
##  [413] "findPackageEnv"                    
##  [414] "findRestart"                       
##  [415] "floor"                             
##  [416] "flush"                             
##  [417] "flush.connection"                  
##  [418] "for"                               
##  [419] "force"                             
##  [420] "formals"                           
##  [421] "formals<-"                         
##  [422] "format"                            
##  [423] "format.AsIs"                       
##  [424] "formatC"                           
##  [425] "format.data.frame"                 
##  [426] "format.Date"                       
##  [427] "format.default"                    
##  [428] "format.difftime"                   
##  [429] "formatDL"                          
##  [430] "format.factor"                     
##  [431] "format.hexmode"                    
##  [432] "format.info"                       
##  [433] "format.libraryIQR"                 
##  [434] "format.numeric_version"            
##  [435] "format.octmode"                    
##  [436] "format.packageInfo"                
##  [437] "format.POSIXct"                    
##  [438] "format.POSIXlt"                    
##  [439] "format.pval"                       
##  [440] "format.summaryDefault"             
##  [441] "forwardsolve"                      
##  [442] "function"                          
##  [443] "gamma"                             
##  [444] "gc"                                
##  [445] "gcinfo"                            
##  [446] "gc.time"                           
##  [447] "gctorture"                         
##  [448] "gctorture2"                        
##  [449] "get"                               
##  [450] "getAllConnections"                 
##  [451] "getCallingDLL"                     
##  [452] "getCallingDLLe"                    
##  [453] "getConnection"                     
##  [454] "getDLLRegisteredRoutines"          
##  [455] "getDLLRegisteredRoutines.character"
##  [456] "getDLLRegisteredRoutines.DLLInfo"  
##  [457] "getElement"                        
##  [458] "geterrmessage"                     
##  [459] "getExportedValue"                  
##  [460] "getHook"                           
##  [461] "getLoadedDLLs"                     
##  [462] "getNamespace"                      
##  [463] "getNamespaceExports"               
##  [464] "getNamespaceImports"               
##  [465] "getNamespaceInfo"                  
##  [466] "getNamespaceName"                  
##  [467] "getNamespaceUsers"                 
##  [468] "getNamespaceVersion"               
##  [469] "getNativeSymbolInfo"               
##  [470] "getOption"                         
##  [471] "getRversion"                       
##  [472] "getSrcLines"                       
##  [473] "getTaskCallbackNames"              
##  [474] "gettext"                           
##  [475] "gettextf"                          
##  [476] "getwd"                             
##  [477] "gl"                                
##  [478] "globalenv"                         
##  [479] "gregexpr"                          
##  [480] "grep"                              
##  [481] "grepl"                             
##  [482] "grepRaw"                           
##  [483] "gsub"                              
##  [484] "gzcon"                             
##  [485] "gzfile"                            
##  [486] "|.hexmode"                         
##  [487] "!.hexmode"                         
##  [488] "[.hexmode"                         
##  [489] "&.hexmode"                         
##  [490] "I"                                 
##  [491] "iconv"                             
##  [492] "iconvlist"                         
##  [493] "icuSetCollate"                     
##  [494] "identical"                         
##  [495] "identity"                          
##  [496] "if"                                
##  [497] "ifelse"                            
##  [498] "Im"                                
##  [499] "importIntoEnv"                     
##  [500] "%in%"                              
##  [501] "inherits"                          
##  [502] "integer"                           
##  [503] "interaction"                       
##  [504] "interactive"                       
##  [505] "intersect"                         
##  [506] "intToBits"                         
##  [507] "intToUtf8"                         
##  [508] "inverse.rle"                       
##  [509] "invisible"                         
##  [510] "invokeRestart"                     
##  [511] "invokeRestartInteractively"        
##  [512] "is.array"                          
##  [513] "is.atomic"                         
##  [514] "isatty"                            
##  [515] "isBaseNamespace"                   
##  [516] "is.call"                           
##  [517] "is.character"                      
##  [518] "is.complex"                        
##  [519] "is.data.frame"                     
##  [520] "isdebugged"                        
##  [521] "is.double"                         
##  [522] "is.element"                        
##  [523] "is.environment"                    
##  [524] "is.expression"                     
##  [525] "is.factor"                         
##  [526] "is.finite"                         
##  [527] "is.function"                       
##  [528] "isIncomplete"                      
##  [529] "is.infinite"                       
##  [530] "is.integer"                        
##  [531] "is.language"                       
##  [532] "is.list"                           
##  [533] "is.loaded"                         
##  [534] "is.logical"                        
##  [535] "is.matrix"                         
##  [536] "is.na"                             
##  [537] "is.na<-"                           
##  [538] "is.na.data.frame"                  
##  [539] "is.na<-.default"                   
##  [540] "is.na<-.factor"                    
##  [541] "is.name"                           
##  [542] "isNamespace"                       
##  [543] "is.nan"                            
##  [544] "is.na.numeric_version"             
##  [545] "is.na.POSIXlt"                     
##  [546] "is.null"                           
##  [547] "is.numeric"                        
##  [548] "is.numeric.Date"                   
##  [549] "is.numeric.difftime"               
##  [550] "is.numeric.POSIXt"                 
##  [551] "is.numeric_version"                
##  [552] "is.object"                         
##  [553] "ISOdate"                           
##  [554] "ISOdatetime"                       
##  [555] "isOpen"                            
##  [556] "is.ordered"                        
##  [557] "is.package_version"                
##  [558] "is.pairlist"                       
##  [559] "is.primitive"                      
##  [560] "is.qr"                             
##  [561] "is.R"                              
##  [562] "is.raw"                            
##  [563] "is.recursive"                      
##  [564] "isRestart"                         
##  [565] "isS4"                              
##  [566] "isSeekable"                        
##  [567] "is.single"                         
##  [568] "is.symbol"                         
##  [569] "isSymmetric"                       
##  [570] "isSymmetric.matrix"                
##  [571] "is.table"                          
##  [572] "isTRUE"                            
##  [573] "is.unsorted"                       
##  [574] "is.vector"                         
##  [575] "jitter"                            
##  [576] "julian"                            
##  [577] "julian.Date"                       
##  [578] "julian.POSIXt"                     
##  [579] "kappa"                             
##  [580] "kappa.default"                     
##  [581] "kappa.lm"                          
##  [582] "kappa.qr"                          
##  [583] "kronecker"                         
##  [584] "l10n_info"                         
##  [585] "labels"                            
##  [586] "labels.default"                    
##  [587] "lapply"                            
##  [588] "La.svd"                            
##  [589] "La_version"                        
##  [590] "lazyLoad"                          
##  [591] "lazyLoadDBexec"                    
##  [592] "lazyLoadDBfetch"                   
##  [593] "lbeta"                             
##  [594] "lchoose"                           
##  [595] "length"                            
##  [596] "length<-"                          
##  [597] "length<-.factor"                   
##  [598] "length.POSIXlt"                    
##  [599] "LETTERS"                           
##  [600] "letters"                           
##  [601] "levels"                            
##  [602] "levels<-"                          
##  [603] "levels.default"                    
##  [604] "levels<-.factor"                   
##  [605] "lfactorial"                        
##  [606] "lgamma"                            
##  [607] "library"                           
##  [608] "library.dynam"                     
##  [609] "library.dynam.unload"              
##  [610] "licence"                           
##  [611] "license"                           
##  [612] "list"                              
##  [613] "list2env"                          
##  [614] "list.dirs"                         
##  [615] "list.files"                        
##  [616] "[.listof"                          
##  [617] "load"                              
##  [618] "loadedNamespaces"                  
##  [619] "loadingNamespaceInfo"              
##  [620] "loadNamespace"                     
##  [621] "local"                             
##  [622] "lockBinding"                       
##  [623] "lockEnvironment"                   
##  [624] "log"                               
##  [625] "log10"                             
##  [626] "log1p"                             
##  [627] "log2"                              
##  [628] "logb"                              
##  [629] "logical"                           
##  [630] "lower.tri"                         
##  [631] "ls"                                
##  [632] "makeActiveBinding"                 
##  [633] "make.names"                        
##  [634] "make.unique"                       
##  [635] "Map"                               
##  [636] "mapply"                            
##  [637] "margin.table"                      
##  [638] "match"                             
##  [639] "match.arg"                         
##  [640] "match.call"                        
##  [641] "match.fun"                         
##  [642] "Math.data.frame"                   
##  [643] "Math.Date"                         
##  [644] "Math.difftime"                     
##  [645] "Math.factor"                       
##  [646] "Math.POSIXt"                       
##  [647] "mat.or.vec"                        
##  [648] "matrix"                            
##  [649] "max"                               
##  [650] "max.col"                           
##  [651] "mean"                              
##  [652] "mean.Date"                         
##  [653] "mean.default"                      
##  [654] "mean.difftime"                     
##  [655] "mean.POSIXct"                      
##  [656] "mean.POSIXlt"                      
##  [657] "memCompress"                       
##  [658] "memDecompress"                     
##  [659] "mem.limits"                        
##  [660] "memory.profile"                    
##  [661] "merge"                             
##  [662] "merge.data.frame"                  
##  [663] "merge.default"                     
##  [664] "message"                           
##  [665] "mget"                              
##  [666] "min"                               
##  [667] "missing"                           
##  [668] "Mod"                               
##  [669] "mode"                              
##  [670] "mode<-"                            
##  [671] "month.abb"                         
##  [672] "month.name"                        
##  [673] "months"                            
##  [674] "months.Date"                       
##  [675] "months.POSIXt"                     
##  [676] "mostattributes<-"                  
##  [677] "names"                             
##  [678] "names<-"                           
##  [679] "namespaceExport"                   
##  [680] "namespaceImport"                   
##  [681] "namespaceImportClasses"            
##  [682] "namespaceImportFrom"               
##  [683] "namespaceImportMethods"            
##  [684] "names<-.POSIXlt"                   
##  [685] "names.POSIXlt"                     
##  [686] "nargs"                             
##  [687] "nchar"                             
##  [688] "NCOL"                              
##  [689] "ncol"                              
##  [690] "Negate"                            
##  [691] "new.env"                           
##  [692] "next"                              
##  [693] "NextMethod"                        
##  [694] "ngettext"                          
##  [695] "nlevels"                           
##  [696] "noquote"                           
##  [697] "[.noquote"                         
##  [698] "norm"                              
##  [699] "normalizePath"                     
##  [700] "NROW"                              
##  [701] "nrow"                              
##  [702] "numeric"                           
##  [703] "[.numeric_version"                 
##  [704] "[[<-.numeric_version"              
##  [705] "[[.numeric_version"                
##  [706] "numeric_version"                   
##  [707] "nzchar"                            
##  [708] "%o%"                               
##  [709] "objects"                           
##  [710] "|.octmode"                         
##  [711] "!.octmode"                         
##  [712] "[.octmode"                         
##  [713] "&.octmode"                         
##  [714] "oldClass"                          
##  [715] "oldClass<-"                        
##  [716] "OlsonNames"                        
##  [717] "on.exit"                           
##  [718] "open"                              
##  [719] "open.connection"                   
##  [720] "open.srcfile"                      
##  [721] "open.srcfilealias"                 
##  [722] "open.srcfilecopy"                  
##  [723] "Ops.data.frame"                    
##  [724] "Ops.Date"                          
##  [725] "Ops.difftime"                      
##  [726] "Ops.factor"                        
##  [727] "Ops.numeric_version"               
##  [728] "Ops.ordered"                       
##  [729] "Ops.POSIXt"                        
##  [730] "options"                           
##  [731] "order"                             
##  [732] "ordered"                           
##  [733] "outer"                             
##  [734] "packageEvent"                      
##  [735] "packageHasNamespace"               
##  [736] "packageStartupMessage"             
##  [737] "$.package_version"                 
##  [738] "package_version"                   
##  [739] "packBits"                          
##  [740] "pairlist"                          
##  [741] "parent.env"                        
##  [742] "parent.env<-"                      
##  [743] "parent.frame"                      
##  [744] "parse"                             
##  [745] "parseNamespaceFile"                
##  [746] "paste"                             
##  [747] "paste0"                            
##  [748] "path.expand"                       
##  [749] "path.package"                      
##  [750] "pi"                                
##  [751] "pipe"                              
##  [752] "pmatch"                            
##  [753] "pmax"                              
##  [754] "pmax.int"                          
##  [755] "pmin"                              
##  [756] "pmin.int"                          
##  [757] "polyroot"                          
##  [758] "Position"                          
##  [759] "[<-.POSIXct"                       
##  [760] "[.POSIXct"                         
##  [761] "[[.POSIXct"                        
##  [762] "[<-.POSIXlt"                       
##  [763] "[.POSIXlt"                         
##  [764] "-.POSIXt"                          
##  [765] "+.POSIXt"                          
##  [766] "pos.to.env"                        
##  [767] "pretty"                            
##  [768] "pretty.default"                    
##  [769] "prettyNum"                         
##  [770] "print"                             
##  [771] "print.AsIs"                        
##  [772] "print.by"                          
##  [773] "print.condition"                   
##  [774] "print.connection"                  
##  [775] "print.data.frame"                  
##  [776] "print.Date"                        
##  [777] "print.default"                     
##  [778] "print.difftime"                    
##  [779] "print.DLLInfo"                     
##  [780] "print.DLLInfoList"                 
##  [781] "print.DLLRegisteredRoutines"       
##  [782] "print.factor"                      
##  [783] "print.function"                    
##  [784] "print.hexmode"                     
##  [785] "print.libraryIQR"                  
##  [786] "print.listof"                      
##  [787] "print.NativeRoutineList"           
##  [788] "print.noquote"                     
##  [789] "print.numeric_version"             
##  [790] "print.octmode"                     
##  [791] "print.packageInfo"                 
##  [792] "print.POSIXct"                     
##  [793] "print.POSIXlt"                     
##  [794] "print.proc_time"                   
##  [795] "print.restart"                     
##  [796] "print.rle"                         
##  [797] "print.simple.list"                 
##  [798] "print.srcfile"                     
##  [799] "print.srcref"                      
##  [800] "print.summaryDefault"              
##  [801] "print.summary.table"               
##  [802] "print.table"                       
##  [803] "print.warnings"                    
##  [804] "prmatrix"                          
##  [805] "proc.time"                         
##  [806] "prod"                              
##  [807] "prop.table"                        
##  [808] "provideDimnames"                   
##  [809] "psigamma"                          
##  [810] "pushBack"                          
##  [811] "pushBackLength"                    
##  [812] "q"                                 
##  [813] "qr"                                
##  [814] "qr.coef"                           
##  [815] "qr.default"                        
##  [816] "qr.fitted"                         
##  [817] "qr.Q"                              
##  [818] "qr.qty"                            
##  [819] "qr.qy"                             
##  [820] "qr.R"                              
##  [821] "qr.resid"                          
##  [822] "qr.solve"                          
##  [823] "qr.X"                              
##  [824] "quarters"                          
##  [825] "quarters.Date"                     
##  [826] "quarters.POSIXt"                   
##  [827] "quit"                              
##  [828] "quote"                             
##  [829] "range"                             
##  [830] "range.default"                     
##  [831] "rank"                              
##  [832] "rapply"                            
##  [833] "raw"                               
##  [834] "rawConnection"                     
##  [835] "rawConnectionValue"                
##  [836] "rawShift"                          
##  [837] "rawToBits"                         
##  [838] "rawToChar"                         
##  [839] "rbind"                             
##  [840] "rbind.data.frame"                  
##  [841] "rcond"                             
##  [842] "Re"                                
##  [843] "readBin"                           
##  [844] "readChar"                          
##  [845] "read.dcf"                          
##  [846] "readline"                          
##  [847] "readLines"                         
##  [848] "readRDS"                           
##  [849] "readRenviron"                      
##  [850] "Recall"                            
##  [851] "Reduce"                            
##  [852] "regexec"                           
##  [853] "regexpr"                           
##  [854] "reg.finalizer"                     
##  [855] "registerS3method"                  
##  [856] "registerS3methods"                 
##  [857] "regmatches"                        
##  [858] "regmatches<-"                      
##  [859] "remove"                            
##  [860] "removeTaskCallback"                
##  [861] "rep"                               
##  [862] "rep.Date"                          
##  [863] "repeat"                            
##  [864] "rep.factor"                        
##  [865] "rep.int"                           
##  [866] "replace"                           
##  [867] "rep_len"                           
##  [868] "replicate"                         
##  [869] "rep.numeric_version"               
##  [870] "rep.POSIXct"                       
##  [871] "rep.POSIXlt"                       
##  [872] "require"                           
##  [873] "requireNamespace"                  
##  [874] "restartDescription"                
##  [875] "restartFormals"                    
##  [876] "retracemem"                        
##  [877] "return"                            
##  [878] "rev"                               
##  [879] "rev.default"                       
##  [880] "R.home"                            
##  [881] "rle"                               
##  [882] "rm"                                
##  [883] "RNGkind"                           
##  [884] "RNGversion"                        
##  [885] "round"                             
##  [886] "round.Date"                        
##  [887] "round.POSIXt"                      
##  [888] "row"                               
##  [889] "rowMeans"                          
##  [890] "rownames"                          
##  [891] "row.names"                         
##  [892] "row.names<-"                       
##  [893] "rownames<-"                        
##  [894] "row.names<-.data.frame"            
##  [895] "row.names.data.frame"              
##  [896] "row.names<-.default"               
##  [897] "row.names.default"                 
##  [898] "rowsum"                            
##  [899] "rowsum.data.frame"                 
##  [900] "rowsum.default"                    
##  [901] "rowSums"                           
##  [902] "R_system_version"                  
##  [903] "R.Version"                         
##  [904] "R.version"                         
##  [905] "R.version.string"                  
##  [906] "sample"                            
##  [907] "sample.int"                        
##  [908] "sapply"                            
##  [909] "save"                              
##  [910] "save.image"                        
##  [911] "saveRDS"                           
##  [912] "scale"                             
##  [913] "scale.default"                     
##  [914] "scan"                              
##  [915] "search"                            
##  [916] "searchpaths"                       
##  [917] "seek"                              
##  [918] "seek.connection"                   
##  [919] "seq"                               
##  [920] "seq_along"                         
##  [921] "seq.Date"                          
##  [922] "seq.default"                       
##  [923] "seq.int"                           
##  [924] "seq_len"                           
##  [925] "seq.POSIXt"                        
##  [926] "sequence"                          
##  [927] "serialize"                         
##  [928] "setdiff"                           
##  [929] "setequal"                          
##  [930] "setHook"                           
##  [931] "setNamespaceInfo"                  
##  [932] "set.seed"                          
##  [933] "setSessionTimeLimit"               
##  [934] "setTimeLimit"                      
##  [935] "setwd"                             
##  [936] "showConnections"                   
##  [937] "shQuote"                           
##  [938] "sign"                              
##  [939] "signalCondition"                   
##  [940] "signif"                            
##  [941] "simpleCondition"                   
##  [942] "simpleError"                       
##  [943] "[.simple.list"                     
##  [944] "simpleMessage"                     
##  [945] "simpleWarning"                     
##  [946] "simplify2array"                    
##  [947] "sin"                               
##  [948] "single"                            
##  [949] "sinh"                              
##  [950] "sink"                              
##  [951] "sink.number"                       
##  [952] "sinpi"                             
##  [953] "slice.index"                       
##  [954] "socketConnection"                  
##  [955] "socketSelect"                      
##  [956] "solve"                             
##  [957] "solve.default"                     
##  [958] "solve.qr"                          
##  [959] "sort"                              
##  [960] "sort.default"                      
##  [961] "sort.int"                          
##  [962] "sort.list"                         
##  [963] "sort.POSIXlt"                      
##  [964] "source"                            
##  [965] "split"                             
##  [966] "split<-"                           
##  [967] "split<-.data.frame"                
##  [968] "split.data.frame"                  
##  [969] "split.Date"                        
##  [970] "split<-.default"                   
##  [971] "split.default"                     
##  [972] "split.POSIXct"                     
##  [973] "sprintf"                           
##  [974] "sqrt"                              
##  [975] "sQuote"                            
##  [976] "srcfile"                           
##  [977] "srcfilealias"                      
##  [978] "srcfilecopy"                       
##  [979] "srcref"                            
##  [980] "standardGeneric"                   
##  [981] "stderr"                            
##  [982] "stdin"                             
##  [983] "stdout"                            
##  [984] "stop"                              
##  [985] "stopifnot"                         
##  [986] "storage.mode"                      
##  [987] "storage.mode<-"                    
##  [988] "strftime"                          
##  [989] "strptime"                          
##  [990] "strsplit"                          
##  [991] "strtoi"                            
##  [992] "strtrim"                           
##  [993] "structure"                         
##  [994] "strwrap"                           
##  [995] "sub"                               
##  [996] "subset"                            
##  [997] "subset.data.frame"                 
##  [998] "subset.default"                    
##  [999] "subset.matrix"                     
## [1000] "substitute"                        
## [1001] "substr"                            
## [1002] "substr<-"                          
## [1003] "substring"                         
## [1004] "substring<-"                       
## [1005] "sum"                               
## [1006] "summary"                           
## [1007] "summary.connection"                
## [1008] "Summary.data.frame"                
## [1009] "summary.data.frame"                
## [1010] "Summary.Date"                      
## [1011] "summary.Date"                      
## [1012] "summary.default"                   
## [1013] "Summary.difftime"                  
## [1014] "Summary.factor"                    
## [1015] "summary.factor"                    
## [1016] "summary.matrix"                    
## [1017] "Summary.numeric_version"           
## [1018] "Summary.ordered"                   
## [1019] "Summary.POSIXct"                   
## [1020] "summary.POSIXct"                   
## [1021] "Summary.POSIXlt"                   
## [1022] "summary.POSIXlt"                   
## [1023] "summary.proc_time"                 
## [1024] "summary.srcfile"                   
## [1025] "summary.srcref"                    
## [1026] "summary.table"                     
## [1027] "suppressMessages"                  
## [1028] "suppressPackageStartupMessages"    
## [1029] "suppressWarnings"                  
## [1030] "svd"                               
## [1031] "sweep"                             
## [1032] "switch"                            
## [1033] "sys.call"                          
## [1034] "sys.calls"                         
## [1035] "Sys.chmod"                         
## [1036] "Sys.Date"                          
## [1037] "sys.frame"                         
## [1038] "sys.frames"                        
## [1039] "sys.function"                      
## [1040] "Sys.getenv"                        
## [1041] "Sys.getlocale"                     
## [1042] "Sys.getpid"                        
## [1043] "Sys.glob"                          
## [1044] "Sys.info"                          
## [1045] "sys.load.image"                    
## [1046] "Sys.localeconv"                    
## [1047] "sys.nframe"                        
## [1048] "sys.on.exit"                       
## [1049] "sys.parent"                        
## [1050] "sys.parents"                       
## [1051] "Sys.readlink"                      
## [1052] "sys.save.image"                    
## [1053] "Sys.setenv"                        
## [1054] "Sys.setFileTime"                   
## [1055] "Sys.setlocale"                     
## [1056] "Sys.sleep"                         
## [1057] "sys.source"                        
## [1058] "sys.status"                        
## [1059] "system"                            
## [1060] "system2"                           
## [1061] "system.file"                       
## [1062] "system.time"                       
## [1063] "Sys.time"                          
## [1064] "Sys.timezone"                      
## [1065] "Sys.umask"                         
## [1066] "Sys.unsetenv"                      
## [1067] "Sys.which"                         
## [1068] "T"                                 
## [1069] "t"                                 
## [1070] "table"                             
## [1071] "tabulate"                          
## [1072] "tan"                               
## [1073] "tanh"                              
## [1074] "tanpi"                             
## [1075] "tapply"                            
## [1076] "taskCallbackManager"               
## [1077] "tcrossprod"                        
## [1078] "t.data.frame"                      
## [1079] "t.default"                         
## [1080] "tempdir"                           
## [1081] "tempfile"                          
## [1082] "testPlatformEquivalence"           
## [1083] "textConnection"                    
## [1084] "textConnectionValue"               
## [1085] "tolower"                           
## [1086] "topenv"                            
## [1087] "toString"                          
## [1088] "toString.default"                  
## [1089] "toupper"                           
## [1090] "trace"                             
## [1091] "traceback"                         
## [1092] "tracemem"                          
## [1093] "tracingState"                      
## [1094] "transform"                         
## [1095] "transform.data.frame"              
## [1096] "transform.default"                 
## [1097] "trigamma"                          
## [1098] "trunc"                             
## [1099] "truncate"                          
## [1100] "truncate.connection"               
## [1101] "trunc.Date"                        
## [1102] "trunc.POSIXt"                      
## [1103] "try"                               
## [1104] "tryCatch"                          
## [1105] "typeof"                            
## [1106] "unclass"                           
## [1107] "undebug"                           
## [1108] "union"                             
## [1109] "unique"                            
## [1110] "unique.array"                      
## [1111] "unique.data.frame"                 
## [1112] "unique.default"                    
## [1113] "unique.matrix"                     
## [1114] "unique.numeric_version"            
## [1115] "unique.POSIXlt"                    
## [1116] "units"                             
## [1117] "units<-"                           
## [1118] "units<-.difftime"                  
## [1119] "units.difftime"                    
## [1120] "unix.time"                         
## [1121] "unlink"                            
## [1122] "unlist"                            
## [1123] "unloadNamespace"                   
## [1124] "unlockBinding"                     
## [1125] "unname"                            
## [1126] "unserialize"                       
## [1127] "unsplit"                           
## [1128] "untrace"                           
## [1129] "untracemem"                        
## [1130] "unz"                               
## [1131] "upper.tri"                         
## [1132] "url"                               
## [1133] "UseMethod"                         
## [1134] "utf8ToInt"                         
## [1135] "vapply"                            
## [1136] "vector"                            
## [1137] "Vectorize"                         
## [1138] "version"                           
## [1139] "warning"                           
## [1140] "warnings"                          
## [1141] "[.warnings"                        
## [1142] "weekdays"                          
## [1143] "weekdays.Date"                     
## [1144] "weekdays.POSIXt"                   
## [1145] "which"                             
## [1146] "which.max"                         
## [1147] "which.min"                         
## [1148] "while"                             
## [1149] "with"                              
## [1150] "withCallingHandlers"               
## [1151] "with.default"                      
## [1152] "within"                            
## [1153] "within.data.frame"                 
## [1154] "within.list"                       
## [1155] "withRestarts"                      
## [1156] "withVisible"                       
## [1157] "write"                             
## [1158] "writeBin"                          
## [1159] "writeChar"                         
## [1160] "write.dcf"                         
## [1161] "writeLines"                        
## [1162] "%x%"                               
## [1163] "xor"                               
## [1164] "xor.hexmode"                       
## [1165] "xor.octmode"                       
## [1166] "xpdrows.data.frame"                
## [1167] "xtfrm"                             
## [1168] "xtfrm.AsIs"                        
## [1169] "xtfrm.Date"                        
## [1170] "xtfrm.default"                     
## [1171] "xtfrm.difftime"                    
## [1172] "xtfrm.factor"                      
## [1173] "xtfrm.numeric_version"             
## [1174] "xtfrm.POSIXct"                     
## [1175] "xtfrm.POSIXlt"                     
## [1176] "xtfrm.Surv"                        
## [1177] "xzfile"                            
## [1178] "zapsmall"
```

```r
ls.str()
```

```
## ancestors : function (environ = globalenv())  
## fget : function (name, env, recursive = FALSE)  
## flex_assign : function (name)  
## metadata : List of 4
##  $ title : chr "Environments"
##  $ author: chr "Alathea"
##  $ date  : chr "2014-07-21"
##  $ output:List of 1
## my_exists_global : function (name, environ = parent.frame())  
## my_exists_local : function (name)  
## my_get : function (obj_name, env = parent.frame(), recursive = TRUE)  
## my_where : function (env = parent.frame())  
## name :  chr "Alathea"
## str_plus : function (fun)
```

Use `rm()` to remove a binding from the environment.


```r
a <- new.env()
y <- 11
exists("y", envir = a)
```

```
## [1] TRUE
```

```r
a$y
```

```
## NULL
```

```r
exists("x", envir = a, inherits = FALSE)
```

```
## [1] FALSE
```


### Recursing over Environments


```r
require(pryr)

x <- 10
where("x")
```

```
## <environment: R_GlobalEnv>
```

### Function environments

The four function environments:

1. enclosing (`environment(f)`)
2. binding: which environment(s) have a binding to the function (`where(f)`)
3. execution: a temporary environment created during execution of the function, then thrown away; child functions can access everything in the execution environment and this environment is now their enclosing environment.
4. calling (`parent.frame()`)

Regular scoping rules only look in the function enclosing environment, not calling environment.

### Binding names to values

Some names are reserved `?Reserved` and some can't be used at all... unless you override the rules by enclosing your function name in backticks `` `:) <- "smile"` ``

Delayed bindings: `%<d-%` (from `pryr`) or `delayedAssign()`

Active bindings are re-evaluated everytime they are accessed: `%<a-%` (from `pryr`) or `makeActiveBinding()`



***

## Discussion Notes
