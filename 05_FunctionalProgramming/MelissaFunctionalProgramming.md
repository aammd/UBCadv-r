rm(list=ls()) library(pryr)

**Motivation**

    set.seed(1014)
    df <- data.frame(replicate(6, sample(c(1:10, -99), 6, rep = TRUE)))
    names(df) <- letters[1:6]
    df

    ##    a  b c   d   e f
    ## 1  1  6 1   5 -99 1
    ## 2 10  4 4 -99   9 3
    ## 3  7  9 5   4   1 4
    ## 4  2  9 3   8   6 8
    ## 5  1 10 5   9   8 6
    ## 6  6  2 1   3   8 5

    fix_missing <- function(x) {
      x[x == -99] <- NA
      x
    }
    df[] <- lapply(df, fix_missing)

-lapply() is called a functional, because it takes a function as an
argument -Another functional programming technique: storing functions in
lists

    summary <- function(x) {
      funs <- c(mean, median, sd, mad, IQR)
      lapply(funs, function(f) f(x, na.rm = TRUE))
    }

    summary(df)

    ## Warning: argument is not numeric or logical: returning NA

    ## Error: need numeric data

    summary <- function(x) {
     c(mean(x, na.rm = TRUE),
       median(x, na.rm = TRUE),
       sd(x, na.rm = TRUE),
       mad(x, na.rm = TRUE),
       IQR(x, na.rm = TRUE))
    }

Question?- the summary above didnt apply the functions to all the
columns right?

**Anonymous functions**

1.  Given a function, like "mean", match.fun() lets you find a function.
    Given a function, can you find its name? Why doesn’t that make sense
    in R?

No because functions in R are not immediately bound to a name. This is
why you can have anonymous functions.

    match.fun(sd)

    ## function (x, na.rm = FALSE) 
    ## sqrt(var(if (is.vector(x)) x else as.double(x), na.rm = na.rm))
    ## <bytecode: 0x7fecc41efce0>
    ## <environment: namespace:stats>

1.  Use lapply() and an anonymous function to find the coefficient of
    variation (the standard deviation divided by the mean) for all
    columns in the mtcars dataset.

<!-- -->

    lapply(mtcars, FUN=function(x) sd(x)/mean(x))

    ## $mpg
    ## [1] 0.3
    ## 
    ## $cyl
    ## [1] 0.2886
    ## 
    ## $disp
    ## [1] 0.5372
    ## 
    ## $hp
    ## [1] 0.4674
    ## 
    ## $drat
    ## [1] 0.1487
    ## 
    ## $wt
    ## [1] 0.3041
    ## 
    ## $qsec
    ## [1] 0.1001
    ## 
    ## $vs
    ## [1] 1.152
    ## 
    ## $am
    ## [1] 1.228
    ## 
    ## $gear
    ## [1] 0.2001
    ## 
    ## $carb
    ## [1] 0.5743

1.  Use integrate() and an anonymous function to find the area under the
    curve for the following functions. Use Wolfram Alpha to check your
    answers.

y = x \^ 2 - x, x in [0, 10]

    integrate(f= function(x) x^2-x, lower=0, upper=10)

    ## 283.3 with absolute error < 3.1e-12

y = sin(x) + cos(x), x in [-π, π]

    integrate(f= function(x) sin(x) + cos(x), lower=-pi, upper=pi)

    ## 5.232e-16 with absolute error < 6.3e-14

y = exp(x) / x, x in [10, 20]

    integrate(f= function(x) exp(x)/x, lower=10, upper=20)

    ## 25613160 with absolute error < 2.8e-07

1.  A good rule of thumb is that an anonymous function should fit on one
    line and shouldn’t need to use {}. Review your code. Where could you
    have used an anonymous function instead of a named function? Where
    should you have used a named function instead of an anonymous
    function?

**Closures**

-Closures get their name because they enclose the environment of the
parent function and can access all its variables -unenclose() This
function replaces variables defined in the enclosing environment with
their values -Function factories are particularly well suited to maximum
likelihood problems -Having variables at two levels allows you to
maintain state across function invocations. This is possible because
while the execution environment is refreshed every time, the enclosing
environment is constant.

    i <- 0
    new_counter2 <- function() {
      i <<- i + 1
      i
    }

    new_counter2()

    ## [1] 1

    new_counter2()

    ## [1] 2

Counter 2 does increase everytime the function is called because i is
changed in the global environment using \<\<- However, new\_counter has
to be called for the counter to increase and it doesnt create another
function.

    new_counter3 <- function() {
      i <- 0
      function() {
        i <- i + 1
        i
      }
    }

    counter_one<-new_counter3()
    counter_one()

    ## [1] 1

    counter_one()

    ## [1] 1

In this case, in counter 3, a new function is created, however, since
the counter is not modified in the closure but on the calling
environment, it is restarted every time the function counter\_one is
called. Not using \<\<- means the counter is not modified in the parent
environment.

1.  Why are functions created by other functions called closures?

Because they enclose the environment of the parent environment, and they
can use all of the parents objects.

1.  What does the following statistical function do? What would be a
    better name for it? (The existing name is a bit of a hint.)

It creates two functions depending on the value of lambda. One where if
lamba is zero, it creates a function that calculates the log of x,
otherwise, it creates a function that calculates x to the power of
lamba-1/lambda.

    bc <- function(lambda) {
      if (lambda == 0) {
        function(x) log(x)
      } else {
        function(x) (x ^ lambda - 1) / lambda
      }
    }

1.  What does approxfun() do? What does it return?

aproxfun() is a function that creates a function that interpolates
between data points. It returns a function

    x<-1:10
    y<-rnorm(10)

    plot(x,y)

![plot of chunk
unnamed-chunk-11](./MelissaFunctionalProgramming_files/figure-markdown_strict/unnamed-chunk-11.png)

    inter<-approxfun(x,y)
    inter

    ## function (v) 
    ## .approxfun(x, y, v, method, yleft, yright, f)
    ## <bytecode: 0x7fecc50fabc0>
    ## <environment: 0x7fecc50fbb18>

    inter(5:10)

    ## [1] -1.30454  0.73778  1.88850 -0.09745 -0.93585 -0.01595

1.  What does ecdf() do? What does it return?

ecdf creates a function that returns the percentiles for x

1.  Create a function that creates functions that compute the ith
    central moment of a numeric vector. You can test it by running the
    following code:

<!-- -->

    moment<-function(k){
      function(x){
        if(!is.numeric(x)){stop('x not numeric')}
        m<-(1/length(x))*((sum(x-mean(x)))^k)
        return(m)
      } 
    }

    m1 <- moment(1)
    m2 <- moment(2)

    x <- runif(100)
    stopifnot(all.equal(m1(x), 0))
    stopifnot(all.equal(m2(x), var(x) * 99 / 100))

    ## Error: all.equal(m2(x), var(x) * 99/100) is not TRUE

1.  Create a function pick() that takes an index, i, as an argument and
    returns a function with an argument x that subsets x with i.

<!-- -->

    pick<-function(i){
      function(x){
        x[[i]]
      }
    }

    lapply(mtcars, pick(5))

    ## $mpg
    ## [1] 18.7
    ## 
    ## $cyl
    ## [1] 8
    ## 
    ## $disp
    ## [1] 360
    ## 
    ## $hp
    ## [1] 175
    ## 
    ## $drat
    ## [1] 3.15
    ## 
    ## $wt
    ## [1] 3.44
    ## 
    ## $qsec
    ## [1] 17.02
    ## 
    ## $vs
    ## [1] 0
    ## 
    ## $am
    ## [1] 0
    ## 
    ## $gear
    ## [1] 3
    ## 
    ## $carb
    ## [1] 2

    # should do the same as this
    lapply(mtcars, function(x) x[[5]])

    ## $mpg
    ## [1] 18.7
    ## 
    ## $cyl
    ## [1] 8
    ## 
    ## $disp
    ## [1] 360
    ## 
    ## $hp
    ## [1] 175
    ## 
    ## $drat
    ## [1] 3.15
    ## 
    ## $wt
    ## [1] 3.44
    ## 
    ## $qsec
    ## [1] 17.02
    ## 
    ## $vs
    ## [1] 0
    ## 
    ## $am
    ## [1] 0
    ## 
    ## $gear
    ## [1] 3
    ## 
    ## $carb
    ## [1] 2

**List of functions**

-easier to work with groups of related functions -to summarise an object
in multiple ways

1.  Implement a summary function that works like base::summary(), but
    uses a list of functions. Modify the function so it returns a
    closure, making it possible to use it as a function factory.

<!-- -->

    summary(x)

    ## [1] 0.5113 0.5232 0.2815 0.3715 0.4791

    sum_Melissa<-list(
      Mel_min = function(x) min(x),
      Mel_1Qua = function(x) quantile(x,0.25),
      Mel_median = function(x) median(x),
      Mel_mean = function(x) mean(x),
      Mel_3Qua = function(x) quantile(x,0.75),
      Mel_max = function(x) max(x)
      )

    lapply(sum_Melissa,function(f) f(x))

    ## $Mel_min
    ## [1] 0.01148
    ## 
    ## $Mel_1Qua
    ##    25% 
    ## 0.2593 
    ## 
    ## $Mel_median
    ## [1] 0.5232
    ## 
    ## $Mel_mean
    ## [1] 0.5113
    ## 
    ## $Mel_3Qua
    ##    75% 
    ## 0.7384 
    ## 
    ## $Mel_max
    ## [1] 0.9971

    sum_Melissa_factory<-function(){
      list(
        Mel_min<- function(x) min(x),
      Mel_1Qua<- function(x) quantile(x,0.25),
      Mel_median<- function(x) median(x),
      Mel_mean<- function(x) mean(x),
      Mel_3Qua<- function(x) quantile(x,0.75),
      Mel_max<-function(x) max(x)
      ) 
    }


    m<-sum_Melissa_factory()
    m[[2]](1:10)

    ##  25% 
    ## 3.25

1.  Which of the following commands is equivalent to with(x, f(z))?

<!-- -->

    with(mtcars, mean(mpg))

    ## [1] 20.09

f(x\$z) or f(z) if mtcars is attached before
