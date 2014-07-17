# Jenny's reading of 03_Functions
Jenny Bryan  
17 July, 2014  



Week 02 2014-07-10 we read [Functions](http://adv-r.had.co.nz/Functions.html). Source is found [here on github](https://github.com/hadley/adv-r/blob/master/Functions.rmd).

## Taking the quiz

*I made myself enter these answers before reading the chapter, most especially before reading the answers. I annotated/corrected my original answers as I read on.*

1.  What are the three components of a function?

  * the formal arguments
  * the parent environment
  * the function body? return value?
  
*Not bad! "The three components of a function are its body, arguments and environment."*

1.  What does the following code return?

    
    ```r
    y <- 10
    f1 <- function(x) {
      function() {
        x + 10
      }
    }
    f1(1)()
    ```
    
    * Hmmm ... 11?
    
*Correct.*

1.  How would you more typically write this code?

    
    ```r
    `+`(1, `*`(2, 3))
    ```
    
    * `(2 * 3) + 1`

*Correct.*
    
1.  How could you make this call easier to read?

    
    ```r
    mean(, TRUE, x = c(1:10, NA))
    ```

  * `mean(c(1:10, NA), na.rm = TRUE)`

*Correct.*

1.  Does the following function throw an error when called? Why/why not?

    
    ```r
    f2 <- function(a, b) {
      a * 10
    }
    f2(10, stop("This is an error!"))
    ```

  * No, because lazy evaluation implies that `b` will only be evaluated when it comes up in the function. Which it does not.
  
*Correct.*

1.  What is an infix function? How do you write it? What's a replacement 
    function? How do you write it?
    
    * Not sure about the infix thing.
    * A replacement function requires making an assignment to the `[<-` operator, or something like that.

*Well, he sort of punted on the answer as well and sends the reader to sections on [infix](#infix-functions) and [replacement functions](#replacement-functions). I'll look forward to reading them.*

1.  What function do you use to ensure that a cleanup action occurs 
    regardless of how a function terminates?
    
    * `on.exit()`
    
*Correct.*
