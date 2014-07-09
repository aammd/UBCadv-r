    library(dplyr)

    ## 
    ## Attaching package: 'dplyr'
    ## 
    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag
    ## 
    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

    library(tidyr)
    library(magrittr)

    ## 
    ## Attaching package: 'magrittr'
    ## 
    ## The following object is masked from 'package:tidyr':
    ## 
    ##     extract
    ## 
    ## The following object is masked from 'package:dplyr':
    ## 
    ##     %>%

Exercises 1
-----------

1.  Fix each of the following common data frame subsetting errors:

        #mtcars[mtcars$cyl = 4, ]
        mtcars[mtcars$cyl == 4, ]
        #mtcars[-1:4, ]
        mtcars[-(1:4), ]
           # mtcars[mtcars$cyl <= 5]
        mtcars[mtcars$cyl <= 5,]
        #     mtcars[mtcars$cyl == 4 | 6, ]
        mtcars[mtcars$cyl == 4 |mtcars$cyl ==  6, ]

2.  Why does `x <- 1:5; x[NA]` yield five missing values? Hint: why is
    it different from `x[NA_real_]`?

**Because `NA` is a logical vector, and therefore is recycled to the
length of `x` (i.e. 5)**

    x <- 1:5; x[NA] ; x[NA_real_]

    ## [1] NA NA NA NA NA

    ## [1] NA

1.  What does `upper.tri()` return? How does subsetting a matrix with it
    work? Do we need any additional subsetting rules to describe its
    behaviour?

        x <- outer(1:5, 1:5, FUN = "*")
        x[upper.tri(x)]

**`upper.tri()` makes a logical matrix of class `logical`, which is used
to subset x (same size and dimensions. We don't need new subsetting
rules because matrices are vectors!**

1.  Why does `mtcars[1:20]` return a error? How does it differ from the
    similar `mtcars[1:20, ]`?

    **Because there are 11, not 20, columns in `mtcars` and this code is
    asking for the first 20 columns using vector notation.
    `mtcars[1:20,]` asks for the first 20 rows, which is no problem
    because there are 30.**

2.  Implement your own function that extracts the diagonal entries from
    a matrix (it should behave like `diag(x)` where `x` is a matrix).

<!-- -->

    x  <-  outer(1:5, 1:5, FUN = "*")

    andrew_diag <- function(mat){
      require(dplyr)
      require(magrittr)
      
      mat %>%
        ncol %>%
        seq_len %>%
        l(x -> cbind(x,x)) %>%
        mat[.]
      
      }

    andrew_diag(x)

    ## [1]  1  4  9 16 25

1.  What does `df[is.na(df)] <- 0` do? How does it work?

**uses vector notation to subset a dataframe, as if it were a matrix,
and replaces all NA values with 0**

Exercises 2
-----------

1.  Given a linear model, e.g. `mod <- lm(mpg ~ wt, data = mtcars)`,
    extract the residual degrees of freedom. Extract the R squared from
    the model summary (`summary(mod)`)

<!-- -->

    mod <- lm(mpg ~ wt, data = mtcars)
    summary(mod)[[c("fstatistic", "dendf")]]

    ## [1] 30

    ## here is a little randomization test:
    replicate(n = 20,{data.frame(x = runif(n = 20,10,20)) %>%
                        mutate(y = x * 2 + rnorm(20)) %>%
                        lm(y ~ x, data = .)
    },simplify = FALSE
    ) %>% 
      lapply(summary) %>%
      sapply("[[",i = c("fstatistic", "value"))

    ##  [1]  708.6  519.8  592.3  595.1  635.7  381.9  352.6  334.5  591.9  589.8
    ## [11]  829.3  456.1  291.2  961.8 1098.5  776.4  773.5  583.3  917.8  458.7

Exercises 3
===========

### Exercises

1.  How would you randomly permute the columns of a data frame? (This is
    an important technique in random forests). Can you simultaneously
    permute the rows and columns in one step?

<!-- -->

    df <- data.frame(a = 1:5,
                     b = 6:10,
                     c = 11:15)

    df[sample(ncol(df))]

    ##    b  c a
    ## 1  6 11 1
    ## 2  7 12 2
    ## 3  8 13 3
    ## 4  9 14 4
    ## 5 10 15 5

    df[,sample(ncol(df))]

    ##   a  b  c
    ## 1 1  6 11
    ## 2 2  7 12
    ## 3 3  8 13
    ## 4 4  9 14
    ## 5 5 10 15

    df[sample(nrow(df)),sample(ncol(df))]

    ##    b  c a
    ## 1  6 11 1
    ## 5 10 15 5
    ## 4  9 14 4
    ## 3  8 13 3
    ## 2  7 12 2

1.  How would you select a random sample of `m` rows from a data frame?
    What if the sample had to be contiguous (i.e. with an initial row, a
    final row, and every row in between)?

<!-- -->

    contin_samp <- function(df,m){
      df %>% 
        nrow %>% 
        subtract(m) %>% 
        sample(1) %>%
        add(seq_len(m)) %>% 
        df[.,]
      }

    data(mtcars)

    contin_samp(mtcars,4)

    ##                   mpg cyl disp  hp drat    wt  qsec vs am gear carb
    ## AMC Javelin      15.2   8  304 150 3.15 3.435 17.30  0  0    3    2
    ## Camaro Z28       13.3   8  350 245 3.73 3.840 15.41  0  0    3    4
    ## Pontiac Firebird 19.2   8  400 175 3.08 3.845 17.05  0  0    3    2
    ## Fiat X1-9        27.3   4   79  66 4.08 1.935 18.90  1  1    4    1

    testdata <- data.frame(a = 1:100, b = 2:101)

    contin_samp(testdata,5)

    ##     a  b
    ## 37 37 38
    ## 38 38 39
    ## 39 39 40
    ## 40 40 41
    ## 41 41 42

1.  How could you put the columns in a data frame in alphaetical order?

<!-- -->

    mtcars[order(names(mtcars))]

    ##                     am carb cyl  disp drat gear  hp  mpg  qsec vs    wt
    ## Mazda RX4            1    4   6 160.0 3.90    4 110 21.0 16.46  0 2.620
    ## Mazda RX4 Wag        1    4   6 160.0 3.90    4 110 21.0 17.02  0 2.875
    ## Datsun 710           1    1   4 108.0 3.85    4  93 22.8 18.61  1 2.320
    ## Hornet 4 Drive       0    1   6 258.0 3.08    3 110 21.4 19.44  1 3.215
    ## Hornet Sportabout    0    2   8 360.0 3.15    3 175 18.7 17.02  0 3.440
    ## Valiant              0    1   6 225.0 2.76    3 105 18.1 20.22  1 3.460
    ## Duster 360           0    4   8 360.0 3.21    3 245 14.3 15.84  0 3.570
    ## Merc 240D            0    2   4 146.7 3.69    4  62 24.4 20.00  1 3.190
    ## Merc 230             0    2   4 140.8 3.92    4  95 22.8 22.90  1 3.150
    ## Merc 280             0    4   6 167.6 3.92    4 123 19.2 18.30  1 3.440
    ## Merc 280C            0    4   6 167.6 3.92    4 123 17.8 18.90  1 3.440
    ## Merc 450SE           0    3   8 275.8 3.07    3 180 16.4 17.40  0 4.070
    ## Merc 450SL           0    3   8 275.8 3.07    3 180 17.3 17.60  0 3.730
    ## Merc 450SLC          0    3   8 275.8 3.07    3 180 15.2 18.00  0 3.780
    ## Cadillac Fleetwood   0    4   8 472.0 2.93    3 205 10.4 17.98  0 5.250
    ## Lincoln Continental  0    4   8 460.0 3.00    3 215 10.4 17.82  0 5.424
    ## Chrysler Imperial    0    4   8 440.0 3.23    3 230 14.7 17.42  0 5.345
    ## Fiat 128             1    1   4  78.7 4.08    4  66 32.4 19.47  1 2.200
    ## Honda Civic          1    2   4  75.7 4.93    4  52 30.4 18.52  1 1.615
    ## Toyota Corolla       1    1   4  71.1 4.22    4  65 33.9 19.90  1 1.835
    ## Toyota Corona        0    1   4 120.1 3.70    3  97 21.5 20.01  1 2.465
    ## Dodge Challenger     0    2   8 318.0 2.76    3 150 15.5 16.87  0 3.520
    ## AMC Javelin          0    2   8 304.0 3.15    3 150 15.2 17.30  0 3.435
    ## Camaro Z28           0    4   8 350.0 3.73    3 245 13.3 15.41  0 3.840
    ## Pontiac Firebird     0    2   8 400.0 3.08    3 175 19.2 17.05  0 3.845
    ## Fiat X1-9            1    1   4  79.0 4.08    4  66 27.3 18.90  1 1.935
    ## Porsche 914-2        1    2   4 120.3 4.43    5  91 26.0 16.70  0 2.140
    ## Lotus Europa         1    2   4  95.1 3.77    5 113 30.4 16.90  1 1.513
    ## Ford Pantera L       1    4   8 351.0 4.22    5 264 15.8 14.50  0 3.170
    ## Ferrari Dino         1    6   6 145.0 3.62    5 175 19.7 15.50  0 2.770
    ## Maserati Bora        1    8   8 301.0 3.54    5 335 15.0 14.60  0 3.570
    ## Volvo 142E           1    2   4 121.0 4.11    4 109 21.4 18.60  1 2.780
