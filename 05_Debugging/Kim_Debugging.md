---
Title: "Debugging"
Author: "Kim"
Date: '2014-08-06'
---

***

### Notes

fatal errors are caused by stop() functions while normal errors were caused by the function not being able to continue

defensive programming's aim is to let you fail fast so you can pinpoint problems as they arise

The worst scenario is that your code might crash R completely, leaving you with no way to interactively debug your code. This indicates a bug in underlying C code and can be hard to debug.

***

##### Quiz

1. How can you find out where an error occured?

	**`traceback()` should do the job.  Hadley also says binary search.**

1. What does `browser()` do? List the five useful single-key commands
   that you can use inside of a `browser()` environment.
   
   **let's you interactively debug - pauses execution of your commands where the error occurred and lets you investigate the state at that point.
     __ n executes the next step in the function, s is step into which if the next 
    step is a function takes you into that function to work through each line, 
    f finishes execution of the current loop/function, c is for continue which leaves the debugging
     tool and continues regular execution of the function, and q stops debugging and terminates the function to return to the global
      workspace. 'enter' also repeats the previous command and `where` prints the stack trace of active calls (interactive equivalent of traceback)**
   
1. What function do you use to ignore errors in block of code?

	**`try()` wrap it around whatever function is causing the error and the 
	error message will still be printed but it will continue to execute the 
	function. (can also suppress the error message with silent=TRUE) and for larger blocks of code, can do `try({ })`**

1. Why might you want to create an error with a custom S3 class?

	**I couldn't figure this out, here is Hadley's answer: Because you can 
	then capture specific types of error with `tryCatch()`, rather than relying 
	on the comparison of error strings, which is risky, especially when messages 
	are translated**



### Exercises 1

* Compare the following two implementations of `message2error()`. What is the
  main advantage of `withCallingHandlers()` in this scenario? (Hint: look
  carefully at the traceback.)

    ```{r}
    message2error <- function(code) {
      withCallingHandlers(code, message = function(e) stop(e))
    }
    message2error <- function(code) {
      tryCatch(code, message = function(e) stop(e))
    }
    ```

	**withCallingHandlers() goes into the function to show you the message that came with the function that had an error**
	
	```
	### this is WITH CALLING HANDLERS
	> message2error(message("hello!"))
	Error in message("hello!") : hello!
	> traceback()
	9: stop(e) at #2
	8: (function (e) 
	   stop(e))(list(message = "hello!\n", call = message("hello!")))
	7: signalCondition(cond)
	6: doWithOneRestart(return(expr), restart)
	5: withOneRestart(expr, restarts[[1L]])
	4: withRestarts({
	       signalCondition(cond)
	       defaultHandler(cond)
	   }, muffleMessage = function() NULL)
	3: message("hello!")
	2: withCallingHandlers(code, message = function(e) stop(e)) at #2
	1: message2error(message("hello!"))
	### this is TRY CATCH
	> message2error <- function(code) {
	+     tryCatch(code, message = function(e) stop(e))
	+   }
	> message2error(message("hello!"))
	Error in message("hello!") : hello!
	> traceback()
	6: stop(e) at #2
	5: value[[3L]](cond)
	4: tryCatchOne(expr, names, parentenv, handlers[[1L]])
	3: tryCatchList(expr, classes, parentenv, handlers)
	2: tryCatch(code, message = function(e) stop(e)) at #2
	1: message2error(message("hello!"))
	>
	```
	
### Exercises 2

**I gave up on these exercises currently. I'd rather debug my own code than other code that I haven't written :/**

* The goal of the `col_means()` function defined below is to compute the means
  of all numeric columns in a data frame.

    ```{r}
    col_means <- function(df) {
      numeric <- sapply(df, is.numeric)
      numeric_cols <- df[, numeric]

      data.frame(lapply(numeric_cols, mean))
    }
    
    ### CHANGE TO: (this fixes only some of the below cases)
    col_means <- function(df) {
      if (!is.data.frame(df)) stop('not a data frame') ## Davor also adds this to throw an error

      numeric <- vapply(df, is.numeric)
      numeric_cols <- df[, numeric, drop=FALSE]

      data.frame(lapply(numeric_cols, mean))
    }
    ```

    However, the function is not robust to unusual inputs. Look at
    the following results, decide which ones are incorrect, and modify
    `col_means()` to be more robust. (Hint: there are two function calls
    in `col_means()` that are particularly prone to problems.)

    ```{r, eval = FALSE}
    col_means(mtcars)  **works fine*
    col_means(mtcars[, 0])  **error, cannot take 0th column**
    col_means(mtcars[0, ])  **works, but is taking mean of column names (row 0) so all NaNs**
    col_means(mtcars[, "mpg", drop = F])  **? works?**
    col_means(1:10)  **error, 1:10 has no columns?**
    col_means(as.matrix(mtcars))
    col_means(as.list(mtcars))

    mtcars2 <- mtcars
    mtcars2[-1] <- lapply(mtcars2[-1], as.character)
    col_means(mtcars2)
    ```

* The following function "lags" a vector, returning a version of `x` that is `n`
  values behind the original. Improve the function so that it (1) returns a
  useful error message if `n` is not a vector, and (2) has reasonable behaviour
  when `n` is 0 or longer than `x`.

    ```{r}
    lag <- function(x, n = 1L) {
      xlen <- length(x)
      c(rep(NA, n), x[seq_len(xlen - n)])
    }
    ```
