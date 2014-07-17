---
Title: "Functions"
Author: "Kim"
Date: '2014-07-16'
---

***

### Discussion Notes

***

### Quiz

1. What are the three components of a function?

    **the body, the formals, and the environment. body=code that makes up the function, formals = arguments to the function that specify how it is called, and environment = the location of the function's variables**

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

    **11 b/c it takes the 10 in, sends it to the inner function, adds 1 then spits it back out BUT I DON'T UNDERSTAND THE USE OF THE SECOND PARENTHESES**

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

7. What function do you use to ensure that a cleanup action occurs regardless of how a function terminates?

    **`on.exit()`**

### Exercises

1. What function allows you to tell if an object is a function? What function allows you to tell if a function is a primitive function?

    **type the function's name in itself; same to see if it's primitive**

2. This code makes a list of all functions in the base package.

    ```
    objs <- mget(ls("package:base"), inherits = TRUE)
    funs <- Filter(is.function, objs)
    ```
    Use it to answer the following questions:

    1a. Which base function has the most arguments?

    2a. How many base functions have no arguments? What’s special about those functions.

    3a. How could you adapt the code to find all primitive functions?

3. What are the three important components of a function?

4. When does printing a function not show what environment it was created in?




### Next Exercises

1. What does the following code return? Why? What does each of the three c’s mean?

    ```
    c <- 10
    c(c = c)
    ```

    **returns 10 because the function c is read outside the parentheses and within the parentheses is read as an object c.**


2. What are the four principles that govern how R looks for values?

    **name masking - takes the most recently named thing,
    functions vs variables - looks for things in terms of what makes sense in their context as either a function or an object,
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

    **does NOT produce 21^2 but instead 202 which comes from 10^2 + 1 * 2 because even though the functions occur outer to inner, the inner ones aren't run until R reads into them and gets their definition, so the squaring happens first**



    **Why does this eliminate the need for the "in"?
    ```
    `for`(i, 1:2, print(i))
    #> [1] 1
    #> [1] 2
    ```
    **


### More Exercises

1. Clarify the following list of odd function calls:

    ```
    x <- sample(replace = TRUE, 20, x = c(1:10, NA))
    y <- runif(min = 0, max = 1, 20)
    cor(m = "k", y = y, u = "p", x = x)
    ```

2. What does this function return? Why? Which principle does it illustrate?

    ```
    f1 <- function(x = {y <- 1; 2}, y = 0) {
      x + y
    }
    f1()
    ```

3. What does this function return? Why? Which principle does it illustrate?

    ```
    f2 <- function(x = z) {
      z <- 100
      x
    }
    f2()
    ```

### So so many Exercises

1. Create a list of all the replacement functions found in the base package. Which ones are primitive functions?

2. What are valid names for user created infix functions?

3. Create an infix xor() operator.

4. Create infix versions of the set functions intersect(), union(), and setdiff().

5. Create a replacement function that modifies a random location in a vector.


### Last Exercises

1. How does the chdir parameter of source() compare to in_dir()? Why might you prefer one approach to the other?

2. What function undoes the action of library()? How do you save and restore the values of options() and par()?

3. Write a function that opens a graphics device, runs the supplied code, and closes the graphics device (always, regardless of whether or not the plotting code worked).

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

You might want to compare this function to the real capture.output() and think about the simplifications I’ve made. Is the code easier to understand or harder? Have I removed important functionality?

5. Compare capture.output() to capture.output2(). How do the functions differ? What features have I removed to make the key ideas easier to see? How have I rewritten the key ideas to be easier to understand?
