---
title: "Exercises for 'Expressions'"
author: "Davor Cubranic"
date: "18 November, 2014"
output:
  html_document:
    toc: no
    keep_md: TRUE
---



# Structure of expressions

1.  There's no existing base function that checks if an element is
    a valid component of an expression (i.e., it's a constant, name,
    call, or pairlist). Implement one by guessing the names of the "is"
    functions for calls, names, and pairlists.

    
    ```r
    is_valid_expression <- function(expr) {
        if (is.name(expr)) {
            return(TRUE)
        } else if (is.pairlist(expr) || is.call(expr)) {
            return(all(vapply(expr, is_valid_expression, logical(1))))
        } else if (is.atomic(expr) && length(expr) == 1L) {
            return(TRUE)
        } else {
            return(FALSE)
        }
    }
    is_valid_expression(quote(1+1))
    ```
    
    ```
    ## [1] TRUE
    ```
    
    ```r
    is_valid_expression(substitute(class(df), list(df = data.frame(x = 10))))
    ```
    
    ```
    ## [1] FALSE
    ```

1.  `pryr::ast()` uses non-standard evaluation. What's its escape hatch to
    standard evaluation?

    Function `pryr::call_tree`.

1.  What does the call tree of an if statement with multiple else conditions
    look like?

    
    ```r
    pryr::ast(if (1>3) foo else if (1 < 0) bar else baz)
    ```
    
    ```
    ## \- ()
    ##   \- `if
    ##   \- ()
    ##     \- `>
    ##     \-  1
    ##     \-  3
    ##   \- `foo
    ##   \- ()
    ##     \- `if
    ##     \- ()
    ##       \- `<
    ##       \-  1
    ##       \-  0
    ##     \- `bar
    ##     \- `baz
    ```

1.  Compare `ast(x + y %+% z)` to `ast(x ^ y %+% z)`. What do they
    tell you about the precedence of custom infix functions?
    
    ```r
    pryr::ast(x + y %+% z)
    ```
    
    ```
    ## \- ()
    ##   \- `+
    ##   \- `x
    ##   \- ()
    ##     \- `%+%
    ##     \- `y
    ##     \- `z
    ```
    
    ```r
    pryr::ast(x ^ y %+% z)
    ```
    
    ```
    ## \- ()
    ##   \- `%+%
    ##   \- ()
    ##     \- `^
    ##     \- `x
    ##     \- `y
    ##   \- `z
    ```

    Custom infix functions have a higher precendence that addition,
    but lower than exponentiation.
    
1.  Why can't an expression contain an atomic vector of length greater than one?
    Which one of the six types of atomic vector can't appear in an expression?
    Why?


# Names

1.  You can use `formals()` to both get and set the arguments of a function.
    Use `formals()` to modify the following function so that the default value
    of `x` is missing and `y` is 10.

    
    ```r
    g <- function(x = 20, y) {
      x + y
    }
    formals(g) <- alist(x=, y=10)
    formals(g)
    ```
    
    ```
    ## $x
    ## 
    ## 
    ## $y
    ## [1] 10
    ```

1.  Write an equivalent to `get()` using `as.name()` and `eval()`. Write an
    equivalent to `assign()` using `as.name()`, `substitute()`, and `eval()`.
    (Don't worry about the multiple ways of choosing an environment; assume
    that the user supplies it explicitly.)

    
    ```r
    get2 <- function(name, env) {
        eval(as.name(name), env)
    }
    get2('get2', .GlobalEnv)
    ```
    
    ```
    ## function(name, env) {
    ##     eval(as.name(name), env)
    ## }
    ```
    
    ```r
    assign2 <- function(name, value, env) {
        eval(substitute(x <- y, list(x = as.name(name), y = value)), env)
    }
    assign2('z', 43, .GlobalEnv)
    get2('z', .GlobalEnv)
    ```
    
    ```
    ## [1] 43
    ```

# Calls

1.  The following two calls look the same, but are actually different:

    
    ```r
    (a <- call("mean", 1:10))
    ```
    
    ```
    ## mean(1:10)
    ```
    
    ```r
    (b <- call("mean", quote(1:10)))
    ```
    
    ```
    ## mean(1:10)
    ```
    
    ```r
    identical(a, b)
    ```
    
    ```
    ## [1] FALSE
    ```

    What's the difference? Which one should you prefer?

    Call `a` has the argument that is not, strictly speaking, a valid
    expression, because it's a constant with length greater than one.
    So the second form is preferred.

    
    ```r
    is_valid_expression(a)
    ```
    
    ```
    ## [1] FALSE
    ```
    
    ```r
    is_valid_expression(b)
    ```
    
    ```
    ## [1] TRUE
    ```

1.  Implement a pure R version of `do.call()`.

    
    ```r
    do_call <- function(what, args, env = parent.frame()) {
        cc <- as.call(c(as.name(what), as.list(args)))
        eval(cc, env)
    }
    do_call('+', c(3, 42))
    ```
    
    ```
    ## [1] 45
    ```

1.  Concatenating a call and an expression with `c()` creates a list. Implement
    `concat()` so that the following code works to combine a call and
    an additional argument.

    
    ```r
    concat(quote(f), a = 1, b = quote(mean(a)))
    #> f(a = 1, b = mean(a))
    ```

1.  Since `list()`s don't belong in expressions, we could create a more
    convenient call constructor that automatically combines lists into the
    arguments. Implement `make_call()` so that the following code works.

    
    ```r
    make_call(quote(mean), list(quote(x), na.rm = TRUE))
    #> mean(x, na.rm = TRUE)
    make_call(quote(mean), quote(x), na.rm = TRUE)
    #> mean(x, na.rm = TRUE)
    ```

1.  How does `mode<-` work? How does it use `call()`?

1.  Read the source for `pryr::standardise_call()`. How does it work?
    Why is `is.primitive()` needed?

1.  `standardise_call()` doesn't work so well for the following calls.
    Why?

    
    ```r
    standardise_call(quote(mean(1:10, na.rm = TRUE)))
    ```
    
    ```
    ## mean(x = 1:10, na.rm = TRUE)
    ```
    
    ```r
    standardise_call(quote(mean(n = T, 1:10)))
    ```
    
    ```
    ## mean(x = 1:10, n = T)
    ```
    
    ```r
    standardise_call(quote(mean(x = 1:10, , TRUE)))
    ```
    
    ```
    ## mean(x = 1:10, , TRUE)
    ```

1.  Read the documentation for `pryr::modify_call()`. How do you think
    it works? Read the source code.

1.  Use `ast()` and experimentation to figure out the three arguments in an
    `if()` call. Which components are required? What are the arguments to
    the `for()` and `while()` calls?


# Capturing the current call


1.  Compare and contrast `update_model()` with `update.default()`.

1.  Why doesn't `write.csv(mtcars, "mtcars.csv", row = FALSE)` work?
    What property of argument matching has the original author forgotten?

1.  Rewrite `update.formula()` to use R code instead of C code.

1.  Sometimes it's necessary to uncover the function that called the
    function that called the current function (i.e., the grandparent, not
    the parent). How can you use `sys.call()` or `match.call()` to find
    this function?


# Pairlists

1.  How are `alist(a)` and `alist(a = )` different? Think about both the
    input and the output.

1.  Read the documentation and source code for `pryr::partial()`. What does it
    do? How does it work? Read the documentation and source code for
    `pryr::unenclose()`. What does it do and how does it work?

1.  The actual implementation of `curve()` looks more like

    
    ```r
    curve3 <- function(expr, xlim = c(0, 1), n = 100,
                       env = parent.frame()) {
      env2 <- new.env(parent = env)
      env2$x <- seq(xlim[1], xlim[2], length = n)
    
      y <- eval(substitute(expr), env2)
      plot(env2$x, y, type = "l", 
        ylab = deparse(substitute(expr)))
    }
    ```

    How does this approach differ from `curve2()` defined above?


# Parsing and deparsing

1.  What are the differences between `quote()` and `expression()`?

1.  Read the help for `deparse()` and construct a call that `deparse()`
    and `parse()` do not operate symmetrically on.

1.  Compare and contrast `source()` and `sys.source()`.

1.  Modify `simple_source()` so it returns the result of _every_ expression,
    not just the last one.

1.  The code generated by `simple_source()` lacks source references. Read
    the source code for `sys.source()` and the help for `srcfilecopy()`,
    then modify `simple_source()` to preserve source references. You can
    test your code by sourcing a function that contains a comment. If
    successful, when you look at the function, you'll see the comment and
    not just the source code.


# Walking the AST with recursive functions

1.  Why does `logical_abbr()` use a for loop instead of a functional
    like `lapply()`?

1.  `logical_abbr()` works when given quoted objects, but doesn't work when
    given an existing function, as in the example below. Why not? How could
    you modify `logical_abbr()` to work with functions? Think about what
    components make up a function.

    
    ```r
    f <- function(x = TRUE) {
      g(x + T)
    }
    logical_abbr(f)
    ```

1.  Write a function called `ast_type()` that returns either "constant",
    "name", "call", or "pairlist". Rewrite `logical_abbr()`, `find_assign()`,
    and `bquote2()` to use this function with `switch()` instead of nested if
    statements.

1.  Write a function that extracts all calls to a function. Compare your
    function to `pryr::fun_calls()`.

1.  Write a wrapper around `bquote2()` that does non-standard evaluation
    so that you don't need to explicitly `quote()` the input.

1.  Compare `bquote2()` to `bquote()`. There is a subtle bug in `bquote()`:
    it won't replace calls to functions with no arguments. Why?

    
    ```r
    bquote(.(x)(), list(x = quote(f)))
    ```
    
    ```
    ## .(x)()
    ```
    
    ```r
    bquote(.(x)(1), list(x = quote(f)))
    ```
    
    ```
    ## f(1)
    ```

1.  Improve the base `recurse_call()` template to also work with lists of
    functions and expressions (e.g., as from `parse(path_to_file))`.




