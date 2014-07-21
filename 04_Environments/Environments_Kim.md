---
Title: "Environments"
Author: "Kim"
Date: '2014-07-17'
---

***

### Discussion Notes

Question for the group - if you assign something to an object, and then in a new environment
you reassign it as something new, then you remove it just from that env., does it go back to your 
first value or is it now NULL?

What about the case we had last week with c (concatenate) and c as an object. Which one does where find, or how do I direct where to the one I want to find?

**NB The enclosing environment determines how the function finds values; the binding environments determine how we find the function.**

**COOL! delayed binding and active binding with `%<d-%` and `%<a-%` respectively, e.g. `x %<a-% runif(1)`**

***

### Quiz

1.  List three ways that an environment is different to a list.

	**I thought he gave 4: every object has a unique name, objects aren't ordere w/in an env., 
	every env. has a parent (except empty), and env.s have reference semantics**	

1.  What is the parent of the global environment? What is the only 
    environment that doesn't have a parent?
    
    **the last package that you attached with `require()` or `library()` is the parent of the global
    env. and empty is the only env. w/out a parent**
    
1.  What is the enclosing environment of a function? Why is it 
    important?
    
    **it is the env. in which a function was created, and is important because 
    every function has 1 and only 1 - or is it important because it is used for lexical scoping?
     Probably the latter.**

1.  How do you determine the environment from which a function was called?

	**parent.frame()  This function returns the environment where the function was called. 
	We can also use this function to look up the value of names in that environment. 
	  N.B. Looking up variables in the calling environment rather than in the enclosing 
	environment is called dynamic scoping.**

1.  How are `<-` and `<<-` different?
	**assign an object in the current environment versus globally assign an 
	object so that it is accessible everywhere (or before globally assigning, will
	 search to see if that object already exists and reassign it as thus. BUT does that 
	 still make it globally available or not?**

### Exercices 1

1.  List three ways in which an environment differs from a list.

	**Same as quiz question 1: I thought he gave 4: every object has a unique name, objects aren't ordere w/in an env., 
	every env. has a parent (except empty), and env.s have reference semantics**
	
1.  If you don't supply an explicit environment, where do `ls()` and `rm()`
    look? Where does `<-` make bindings?
    
    **In the global environment or the current environment?**

1.  Using `parent.env()` and a loop (or a recursive function), verify that the 
    ancestors of `globalenv()` include `baseenv()` and `emptyenv()`. Use the 
    same basic idea to implement your own version of `search()`.
    
    **haven't tried this**
    
### Exercices 2

**I don't have time to write all these functions, still useful to learn what he's discussing about environments.**

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
    
    **The *enclosing* environment is the environment where the function was created. Every function has one and only one enclosing environment
    Binding a function to a name with <- defines a *binding* environment
    Calling a function creates an ephemeral *execution* environment that stores variables created during execution
    Every execution environment is associated with a *calling* environment, which tells you where the function was called.
     The distinction between enc and binding envs is important because of masking, i.e. if the same name
     is used for different functions, namespace becomes important.**
     
     **this example really helped me: sd() used var() in its definition, but if I rewrite
     var() elsewhere, sd() will be okay because when sd() looks for var() it finds it first 
     in its namespace environment so never looks in the globalenv()**
    
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
