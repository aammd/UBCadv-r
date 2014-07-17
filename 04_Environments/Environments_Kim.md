---
Title: "Environments"
Author: "Kim"
Date: '2014-07-17'
---

***

### Discussion Notes

***

### Quiz

1.  List three ways that an environment is different to a list.

1.  What is the parent of the global environment? What is the only 
    environment that doesn't have a parent?
    
1.  What is the enclosing environment of a function? Why is it 
    important?

1.  How do you determine the environment from which a function was called?

1.  How are `<-` and `<<-` different?

### Exercices 1

1.  List three ways in which an environment differs from a list.

1.  If you don't supply an explicit environment, where do `ls()` and `rm()`
    look? Where does `<-` make bindings?

1.  Using `parent.env()` and a loop (or a recursive function), verify that the 
    ancestors of `globalenv()` include `baseenv()` and `emptyenv()`. Use the 
    same basic idea to implement your own version of `search()`.
    
### Exercices 2

1.  Modify `where()` to find all environments that contain a binding for
    `name`.

1.  Write your own version of `get()` using a function written in the style 
    of `where()`.

1.  Write a function called `fget()` that finds only function objects. It 
    should have two arguments, `name` and `env`, and should obey the regular 
    scoping rules for functions: if there's an object with a matching name 
    that's not a function, look in the parent. For an added challenge, also 
    add an `inherits` argument which controls whether the function recurses up 
    the parents or only looks in one environment.

1.  Write your own version of `exists(inherits = FALSE)` (Hint: use `ls()`). 
    Write a recursive version that behaves like `exists(inherits = TRUE)`.
    
### Exercices 3

1.  List the four environments associated with a function. What does each one
    do? Why is the distinction between enclosing and binding environments
    particularly important?
    
1.  Draw a diagram that shows the enclosing environments of this function:
    
    ```{r, eval = FALSE}
    f1 <- function(x1) {
      f2 <- function(x2) {
        f3 <- function(x3) {
          x1 + x2 + x3
        }
        f3(3)
      }
      f2(2)
    }
    f1(1)
    ```
    
1.  Expand your previous diagram to show function bindings.

1.  Expand it again to show the execution and calling environments.

1.  Write an enhanced version of `str()` that provides more information 
    about functions. Show where the function was found and what environment 
    it was defined in.
    
### Exercises 4

1.  What does this function do? How does it differ from `<<-` and why
    might you prefer it?
    
    ```{r, error = TRUE}
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
    a <- 5
    rebind("a", 10)
    a
    ```

1.  Create a version of `assign()` that will only bind new names, never 
    re-bind old names. Some programming languages only do this, and are known 
    as [single assignment laguages][single assignment].

1.  Write an assignment function that can do active, delayed and locked 
    bindings. What might you call it? What arguments should it take? Can you 
    guess which sort of assignment it should do based on the input?
