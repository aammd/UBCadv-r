---
Title: "Functions"
Author: "Kim"
Date: '2014-07-16'
---

***

### Discussion Notes

***
##### Quiz

Think you know this material already? If you can answer the following questions correctly, you can safely skip this chapter. Find the answers at the end of the chapter in [answers](#oo-answers).

1. How do you tell what OO system (base, S3, S4, or RC) an object is 
   associated with?

1. How do you determine the base type (like integer or list) of an object?

1. What is a generic function?

1. What are the main differences between S3 and S4? What are the main 
   differences between S4 & RC?



### Exercises 1

1.  Read the source code for `t()` and `t.test()` and confirm that 
    `t.test()` is an S3 generic and not an S3 method. What happens if 
    you create an object with class `test` and call `t()` with it?

1.  What classes have a method for the `Math` group generic in base R? Read 
    the source code. How do the methods work?

1.  R has two classes for representing date time data, `POSIXct` and 
    `POSIXlt`, which both inherit from `POSIXt`. Which generics have 
    different behaviours for the two classes? Which generics share the same
    behaviour?

1.  Which base generic has the greatest number of defined methods?

1.  `UseMethod()` calls methods in a special way. Predict what the following
     code will return, then run it and read the help for `UseMethod()` to 
    figure out what's going on. Write down the rules in the simplest form
    possible.

    ```{r, eval = FALSE}
    y <- 1
    g <- function(x) {
      y <- 2
      UseMethod("g")
    }
    g.numeric <- function(x) y
    g(10)

    h <- function(x) {
      x <- 10
      UseMethod("h")
    }
    h.character <- function(x) paste("char", x)
    h.numeric <- function(x) paste("num", x)

    h("a")
    ```

1.  Internal generics don't dispatch on the implicit class of base types.
    Carefully read `?"internal generic"` to determine why the length of `f` 
    and `g` is different in the example below. What function helps 
    distinguish between the behaviour of `f` and `g`?

    ```{r, eval = FALSE}
    f <- function() 1
    g <- function() 2
    class(g) <- "function"
    
    class(f)
    class(g)

    length.function <- function(x) "function"
    length(f)
    length(g)
    ```


### Exercises 2

1.  Which S4 generic has the most methods defined for it? Which S4 class 
    has the most methods associated with it?

1.  What happens if you define a new S4 class that doesn't "contain" an 
    existing class?  (Hint: read about virtual classes in `?Classes`.)

1.  What happens if you pass an S4 object to an S3 generic? What happens 
    if you pass an S3 object to an S4 generic? (Hint: read `?setOldClass` 
    for the second case.)


### Exercises 3

1.  Use a field function to prevent the account balance from being directly
    manipulated. (Hint: create a "hidden" `.balance` field, and read the 
    help for the fields argument in `setRefClass()`.)

1.  I claimed that there aren't any RC classes in base R, but that was a 
    bit of a simplification. Use `getClasses()` and find which classes 
    `extend()` from `envRefClass`. What are the classes used for? (Hint: 
    recall how to look up the documentation for a class.)








## Quiz answers {#oo-answers}

1.  To determine the OO system of an object, you use a process of elimination.
    If `!is.object(x)`, it's a base object. If `!isS4(x)`, it's S3. If 
    `!is(x, "refClass")`, it's S4; otherwise it's RC.
    
1.  Use `typeof()` to determine the base class of an object.

1.  A generic function calls specific methods depending on the class of 
    it inputs. In S3 and S4 object systems, methods belong to generic 
    functions, not classes like in other programming languages.
    
1.  S4 is more formal than S3, and supports multiple inheritance and
    multiple dispatch. RC objects have reference semantics, and methods 
    belong to classes, not functions.