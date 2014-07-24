---
Title: "Functions"
Author: "Kim"
Date: '2014-07-16'
---

***

### Discussion Notes

`missing()` is a useful function

`force()` is useful to require that an arguent in a function is evaluated

The backtick can be used to explicitly call something, like `+`

We are all stuck with the adders example... The newly created add function will add 2 numbers, e.g.
add(1)(2) gives 3, but with `adders <- lapply(1:10, add)` `adders[[1]](10)` gives 20, i.e. just 
calling the last element of list 1:10 rather than making functions to add 1-10 to anything you give it? 
Has to do with lazy evaluation - because x is lazily evaluated, it takes the 10 and adds 10 to it... weird.

Unevaluated argument = a promise

Remember Rich's point about logicals, `T <- FALSE` causes trouble down the line, but `c <- 10` `c(c,c)` doesn't
because R checks variables vs functions and that line of code produces 10 10.

`...` argument matches any arguments not otherwise matched

There are also some useful points about invisible returns

***

### Quiz

1. What are the three components of a function?

    **the body, the formals, and the environment. body=code that makes up the function, formals = 
    arguments to the function that specify how it is called, and environment = the location of the 
    function's variables**

2. What does the following code return?

    ```
    y <- 10
    f1 <- function(x) {
      function() {
        x + 10
      }
    }
    f1(1)()
    ```

    **11 b/c it takes the 10 in, sends it to the inner function, adds 1 then spits it 
    back out; second set of parentheses effectively calls the inner function**

3. How would you more typically write this code?

    ```
    `+`(1, `*`(2, 3))
    ```

    **1 + 2 x 3**

4. How could you make this call easier to read?

    `mean(, TRUE, x = c(1:10, NA))`

    **`mean(c(1:10,NA), na.rm=TRUE)`**

5. Does the following function throw an error when called? Why/why not?

    ```
    f2 <- function(a, b) {
      a * 10
    }
    f2(10, stop("This is an error!"))
    ```
    
    **No, because b is not used inside the function, so when the function is called, b is not evaluated.**

6. What is an infix function? How do you write it? What’s a replacement function? How do you write it?

	**infix functions are where the function name comes between its argument, so unlike mean() which goes in front of the 
	arguments, "+" goes between its arguments.  functions with % are infix functions, e.g. %in% and to create such a function, 
	one needs to use backticks, e.g.**
	```{r}
	`%+%` <- function(a, b) paste(a, b, sep = "")
	"new" %+% " string"
	```
	
	**replacement functions modify an object in place, e.g.  <- to assign a value to an object**
		

7. What function do you use to ensure that a cleanup action occurs regardless of how a function terminates?

    **`on.exit()` can be used as a way to guarantee that changes to the global state are restored when the function exists**

### Exercises

1. What function allows you to tell if an object is a function? What function allows you to tell if a function is a primitive function?

    **type the function's name in itself; same to see if it's primitive**

2. This code makes a list of all functions in the base package.

    ```
    objs <- mget(ls("package:base"), inherits = TRUE)
    funs <- Filter(is.function, objs)
    ```
    Use it to answer the following questions:

    1. Which base function has the most arguments?
    
        **`n_args <- sapply(funs, function(x) length(formals(x)))
        the_one <- which.max(n_args)
        names(funs)[the_one]`**

    2. How many base functions have no arguments? What’s special about those functions.

        **`length(no_args <- which(n_args == 0))`**
        
    3. How could you adapt the code to find all primitive functions?
    
    	**`str(prims <-lapply(funs, function(x) if(is.primitive(x)) x else NULL),
    	list.len = 6)`**

3. What are the three important components of a function?
	
	**body, environment, arguments**

4. When does printing a function not show what environment it was created in?

	**when created in the global environment**




### Next Exercises

1. What does the following code return? Why? What does each of the three c’s mean?

    ```
    c <- 10
    c(c = c)
    ```

    **returns 10 because the function c is read outside the parentheses and within the parentheses 
    is read as an object c.**


2. What are the four principles that govern how R looks for values?

    **name masking - takes the most recently named thing,
    functions vs variables - looks for things in terms of what makes sense in their context 
    as either a function or an object,
    a fresh start - only things that exist currently can be looked for, i.e. within a function call,
    dynamic lookup - looks for the object as it has most recently been assigned**


3. What does the following function return? Make a prediction before running the code yourself.

    ```
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

    **does NOT produce 21^2 but instead 202 which comes from 10^2 + 1 * 2 because even though 
    the functions occur outer to inner, the inner ones are run first because R reads and comes 
    in from the outside then starts at the inside coming inside to outermost until finished, 
    so the squaring happens first**



    **Why does this eliminate the need for the "in"?**
    ```
    `for`(i, 1:2, print(i))
    #> [1] 1
    #> [1] 2
    ```
    


### More Exercises

1. Clarify the following list of odd function calls:

    `x <- sample(replace = TRUE, 20, x = c(1:10, NA))`
    
    **`x <- sample(c(1:10, NA), size=20, replace = TRUE)`**
    
    `y <- runif(min = 0, max = 1, 20)`
    
    **`y <- runif(n=20, min=0, max=1)`**
    
    `cor(m = "k", y = y, u = "p", x = x)`
    
    **`cor(x, y, use= "p", method= "pearson")`**

2. What does this function return? Why? Which principle does it illustrate?

    ```
    f1 <- function(x = {y <- 1; 2}, y = 0) {
      x + y
    }
    f1()
    ```
    **returns 3 because it evaluates x = {y <- 1; 2} which overwrites the y=0**

3. What does this function return? Why? Which principle does it illustrate?

    ```
    f2 <- function(x = z) {
      z <- 100
      x
    }
    f2()
    ```
    **returns 100 because it takes z as an argument, but when not provided uses dynamic lookup and 
    finds z=100, then returns x which according to the function = z**

### So so many Exercises

1. Create a list of all the replacement functions found in the base package. Which ones are primitive functions?

	**a la Jenny:**
	```
	objs <- mget(ls("package:base"), inherits = TRUE)
	funs <- Filter(is.function, objs)
	repl_funs <- funs[grepl("<-$", names(funs))]
	prim_repl_funs <- Filter(is.primitive, repl_funs)
	```
	
2. What are valid names for user created infix functions?
	
	**anything with a % as its start and end**

3. Create an infix xor() operator.

	**I don't know what this is asking**

4. Create infix versions of the set functions intersect(), union(), and setdiff().

	**haven't tried this yet**

5. Create a replacement function that modifies a random location in a vector.

	**```temp <- seq(1:10)
	temp[5] <- 500```**


### Last Exercises

1. How does the chdir parameter of source() compare to in_dir()? Why might you prefer one approach to the other?

	**`chdir` tells you if your file being sourced is a pathname, i.e. is not in the current working
	 directory while `in_dir` saves the invisible output from the previous working directory whenever a 
	 new working directory is set and can be useful for things such as `on.exit()`**

2. What function undoes the action of library()? How do you save and restore the values of options() and par()?

	**library modifies the search path when it loads a package, so maybe something like chdir would undo this?**
	
	**for `options()` and `par()`, assign them to an object when using new parameters, and it will save the previous settings
	 since those are invisibly returned**

3. Write a function that opens a graphics device, runs the supplied code, and closes the graphics device 
(always, regardless of whether or not the plotting code worked).

	```
	quartz()
	plot(1)
	dev.off()
	```

4. We can use on.exit() to implement a simple version of capture.output().

    ```
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

You might want to compare this function to the real capture.output() and think about the simplifications I’ve made. 
Is the code easier to understand or harder? Have I removed important functionality?

   **didn't do this one yet**

5. Compare capture.output() to capture.output2(). How do the functions differ? What features have I 
removed to make the key ideas easier to see? How have I rewritten the key ideas to be easier to understand?

	**didn't do this one yet**