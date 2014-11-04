# Non-Standard Evaluation
Alathea DL  
2014-11-04  

## Exercises

### One important feature of `deparse()` to be aware of when programming is that it can return multiple strings if the input is too long. For example, the following call produces a vector of length two. Why does this happen? Carefully read the documentation. Can you write a wrapper around `deparse()` so that it always returns a single string?

I'm guessing the reason it will return multiple strings has to do with the `width.cutoff` argument, which has a default of `60L`.

One way to deal with this would be to write a wrapper that pastes a bunch of strings together.


```r
g <- function(x) deparse(substitute(x))

g(a + b + c + d + e + f + g + h + i + j + k + l + m +
  n + o + p + q + r + s + t + u + v + w + x + y + z)
```

```
## [1] "a + b + c + d + e + f + g + h + i + j + k + l + m + n + o + p + "
## [2] "    q + r + s + t + u + v + w + x + y + z"
```

```r
to_string <- function(x)
{
  paste(x[1:length(x)], collapse = " ")
}

to_string(g(a + b + c + d + e + f + g + h + i + j + k + l +
            m + n + o + p + q + r + s + t + u + v + w + x + y + z))
```

```
## [1] "a + b + c + d + e + f + g + h + i + j + k + l + m + n + o + p +      q + r + s + t + u + v + w + x + y + z"
```

I'm not sure why the large gap between p and q is created.

***

### Why does `as.Date.default()` use `substitute()` and `deparse()`? Why does `pairwise.t.test()` use them? Read the source code.

`as.Date.default` uses them in order to return the value of `x` in an error if the user makes a mistake.


```r
function (x, ...) 
{
    if (inherits(x, "Date")) 
        return(x)
    if (is.logical(x) && all(is.na(x))) 
        return(structure(as.numeric(x), class = "Date"))
    stop(gettextf("do not know how to convert '%s' to class %s", 
        deparse(substitute(x)), dQuote("Date")), domain = NA)
}
```

`pairwise.t.test` uses this to take your parameters and convert them into a "data name".

***

### `pairwise.t.test()` assumes that `deparse()` always returns a length one character vector. Can you construct an input that violates this expectation? What happens?

Not going to do this one.  This should only effect the data name, though, and may give some kind of weird looking output for that value.

***

### `f()`, defined above, just calls `substitute()`. Why can’t we use it to define `g()`? In other words, what will the following code return? First make a prediction. Then run the code and think about the results.


```r
f <- function(x) substitute(x)
g <- function(x) deparse(f(x))
g(1:10)
g(x)
g(x + y ^ 2 / z + exp(a * sin(b)))
```

I thought `g(x)` would deparse `f(x)` instead of running the code.  But actually it deparses the output of `f(x)`, thereby always returning `x`.

***

### Predict the results of the following lines of code:


```r
eval(quote(eval(quote(eval(quote(2 + 2))))))
eval(eval(quote(eval(quote(eval(quote(2 + 2)))))))
quote(eval(quote(eval(quote(eval(quote(2 + 2)))))))
```

***

### `subset2()` has a bug if you use it with a single column data frame. What should the following code return? How can you modify `subset2()` so it returns the correct type of object?


```r
sample_df2 <- data.frame(x = 1:10)
subset2(sample_df2, x > 8)
#> [1]  9 10
```

***

### The real subset function (`subset.data.frame()`) removes missing values in the condition. Modify `subset2()` to do the same: drop the offending rows.

***

### What happens if you use `quote()` instead of `substitute()` inside of `subset2()`?

### The second argument in `subset()` allows you to select variables. It treats variable names as if they were positions. This allows you to do things like `subset(mtcars, , -cyl)` to drop the cylinder variable, or `subset(mtcars, , disp:drat)` to select all the variables between disp and drat. How does this work? I’ve made this easier to understand by extracting it out into its own function.


```r
select <- function(df, vars) {
  vars <- substitute(vars)
  var_pos <- setNames(as.list(seq_along(df)), names(df))
  pos <- eval(vars, var_pos)
  df[, pos, drop = FALSE]
}
select(mtcars, -cyl)
```

***

### What does `evalq()` do? Use it to reduce the amount of typing for the examples above that use both `eval()` and `quote()`.

***

### `plyr::arrange()` works similarly to `subset()`, but instead of selecting rows, it reorders them. How does it work? What does `substitute(order(...))` do? Create a function that does only that and experiment with it.

***

### What does `transform()` do? Read the documentation. How does it work? Read the source code for `transform.data.frame()`. What does `substitute(list(...))` do?

***

### `plyr::mutate()` is similar to `transform()` but it applies the transformations sequentially so that transformation can refer to columns that were just created:


```r
df <- data.frame(x = 1:5)
transform(df, x2 = x * x, x3 = x2 * x)
plyr::mutate(df, x2 = x * x, x3 = x2 * x)
```

How does mutate work? What’s the key difference between `mutate()` and `transform()`?

***

### What does `with()` do? How does it work? Read the source code for `with.default()`. What does `within()` do? How does it work? Read the source code for `within.data.frame()`. Why is the code so much more complex than `with()`?

***

### The following R functions all use NSE. For each, describe how it uses NSE, and read the documentation to determine its escape hatch.
        
* `rm()`
* `library()` and `require()`
* `substitute()`
* `data()`
* `data.frame()`

***

### Base functions `match.fun()`, `page()`, and `ls()` all try to automatically determine whether you want standard or non-standard evaluation. Each uses a different approach. Figure out the essence of each approach then compare and contrast.

***

### Add an escape hatch to `plyr::mutate()` by splitting it into two functions. One function should capture the unevaluated inputs. The other should take a data frame and list of expressions and perform the computation.

***

### What’s the escape hatch for `ggplot2::aes()`? What about `plyr::()`? What do they have in common? What are the advantages and disadvantages of their differences?

***

### The version of `subset2_q()` I presented is a simplification of real code. Why is the following version better?


```r
subset2_q <- function(x, cond, env = parent.frame()) {
  r <- eval(cond, x, env)
  x[r, ]
}
```

Rewrite `subset2()` and `subscramble()` to use this improved version.

***

### Use `subs()` to convert the LHS to the RHS for each of the following pairs:


```r
a + b + c -> a * b * c
f(g(a, b), c) -> (a + b) * c
f(a < b, c, d) -> if (a < b) c else d
```

***

### For each of the following pairs of expressions, describe why you can’t use `subs()` to convert one to the other.


```r
a + b + c -> a + b * c
f(a, b) -> f(a, b, c)
f(a, b, c) -> f(a, b)
```

***

### How does `pryr::named_dots()` work? Read the source.

***

### What does the following function do? What’s the escape hatch? Do you think that this is an appropriate use of NSE?


```r
nl <- function(...) {
  dots <- named_dots(...)
  lapply(dots, eval, parent.frame())
}
```

***

### Instead of relying on promises, you can use formulas created with `~` to explicitly capture an expression and its environment. What are the advantages and disadvantages of making quoting explicit? How does it impact referential transparency?

***

### Read the standard non-standard evaluation rules found at [http://developer.r-project.org/nonstandard-eval.pdf](http://developer.r-project.org/nonstandard-eval.pdf).


## Reading Notes

* `deparse(substitute())` takes the code you typed and creates a vector out of it

## Discussion Notes
