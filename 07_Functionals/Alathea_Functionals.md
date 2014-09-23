# Functionals
Alathea  
2014-09-04  

# The Exercises

## Why are the following two invocations of `lapply()` equivalent?

```r
trims <- c(0, 0.1, 0.2, 0.5)
x <- rcauchy(100)

lapply(trims, function(trim) mean(x, trim = trim))
lapply(trims, mean, x = x)
```

## The function below scales a vector so it falls in the range [0, 1]. How would you apply it to every column of a data frame? How would you apply it to every numeric column in a data frame?

```r
scale01 <- function(x) {
  rng <- range(x, na.rm = TRUE)
  (x - rng[1]) / (rng[2] - rng[1])
}

not_scaled <- data.frame(a = runif(10, 1, 1000),
                         b = runif(10, 1, 1000))
not_scaled2 <- not_scaled
not_scaled2$c <- c(letters[1:10])

not_scaled

lapply(not_scaled, function(x) scale01(x))
lapply(not_scaled, function(x) scale01(x))
```

## Use both `for` loops and `lapply()` to fit linear models to the `mtcars` using the formulas stored in this list:


```r
formulas <- list(
  mpg ~ disp,
  mpg ~ I(1 / disp),
  mpg ~ disp + wt,
  mpg ~ I(1 / disp) + wt
)

for(i in 1:length(formulas))
{
  model <- lm(formulas[[i]], data = mtcars)
  print(summary(model))
}
```

```
## 
## Call:
## lm(formula = formulas[[i]], data = mtcars)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -4.892 -2.202 -0.963  1.627  7.231 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 29.59985    1.22972   24.07  < 2e-16 ***
## disp        -0.04122    0.00471   -8.75  9.4e-10 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 3.25 on 30 degrees of freedom
## Multiple R-squared:  0.718,	Adjusted R-squared:  0.709 
## F-statistic: 76.5 on 1 and 30 DF,  p-value: 9.38e-10
## 
## 
## Call:
## lm(formula = formulas[[i]], data = mtcars)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -3.738 -1.822 -0.075  1.049  4.610 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   10.752      0.799    13.4  3.1e-14 ***
## I(1/disp)   1557.674    114.894    13.6  2.5e-14 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.29 on 30 degrees of freedom
## Multiple R-squared:  0.86,	Adjusted R-squared:  0.855 
## F-statistic:  184 on 1 and 30 DF,  p-value: 2.49e-14
## 
## 
## Call:
## lm(formula = formulas[[i]], data = mtcars)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -3.409 -2.324 -0.768  1.772  6.348 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 34.96055    2.16454   16.15  4.9e-16 ***
## disp        -0.01772    0.00919   -1.93   0.0636 .  
## wt          -3.35083    1.16413   -2.88   0.0074 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.92 on 29 degrees of freedom
## Multiple R-squared:  0.781,	Adjusted R-squared:  0.766 
## F-statistic: 51.7 on 2 and 29 DF,  p-value: 2.74e-10
## 
## 
## Call:
## lm(formula = formulas[[i]], data = mtcars)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -2.709 -1.512 -0.619  1.514  4.231 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   19.024      3.452    5.51  6.1e-06 ***
## I(1/disp)   1142.560    199.843    5.72  3.5e-06 ***
## wt            -1.798      0.733   -2.45     0.02 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.12 on 29 degrees of freedom
## Multiple R-squared:  0.884,	Adjusted R-squared:  0.876 
## F-statistic:  110 on 2 and 29 DF,  p-value: 2.79e-14
```

```r
lapply(formulas, function(x) summary(lm(x, data = mtcars)))
```

```
## [[1]]
## 
## Call:
## lm(formula = x, data = mtcars)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -4.892 -2.202 -0.963  1.627  7.231 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 29.59985    1.22972   24.07  < 2e-16 ***
## disp        -0.04122    0.00471   -8.75  9.4e-10 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 3.25 on 30 degrees of freedom
## Multiple R-squared:  0.718,	Adjusted R-squared:  0.709 
## F-statistic: 76.5 on 1 and 30 DF,  p-value: 9.38e-10
## 
## 
## [[2]]
## 
## Call:
## lm(formula = x, data = mtcars)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -3.738 -1.822 -0.075  1.049  4.610 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   10.752      0.799    13.4  3.1e-14 ***
## I(1/disp)   1557.674    114.894    13.6  2.5e-14 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.29 on 30 degrees of freedom
## Multiple R-squared:  0.86,	Adjusted R-squared:  0.855 
## F-statistic:  184 on 1 and 30 DF,  p-value: 2.49e-14
## 
## 
## [[3]]
## 
## Call:
## lm(formula = x, data = mtcars)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -3.409 -2.324 -0.768  1.772  6.348 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 34.96055    2.16454   16.15  4.9e-16 ***
## disp        -0.01772    0.00919   -1.93   0.0636 .  
## wt          -3.35083    1.16413   -2.88   0.0074 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.92 on 29 degrees of freedom
## Multiple R-squared:  0.781,	Adjusted R-squared:  0.766 
## F-statistic: 51.7 on 2 and 29 DF,  p-value: 2.74e-10
## 
## 
## [[4]]
## 
## Call:
## lm(formula = x, data = mtcars)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -2.709 -1.512 -0.619  1.514  4.231 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   19.024      3.452    5.51  6.1e-06 ***
## I(1/disp)   1142.560    199.843    5.72  3.5e-06 ***
## wt            -1.798      0.733   -2.45     0.02 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.12 on 29 degrees of freedom
## Multiple R-squared:  0.884,	Adjusted R-squared:  0.876 
## F-statistic:  110 on 2 and 29 DF,  p-value: 2.79e-14
```

# Discussion Notes

# Reading Notes

A *functional* takes a function as input and returns a vector as output


```r
unlist(lapply(mtcars, class))
```

```
##       mpg       cyl      disp        hp      drat        wt      qsec 
## "numeric" "numeric" "numeric" "numeric" "numeric" "numeric" "numeric" 
##        vs        am      gear      carb 
## "numeric" "numeric" "numeric" "numeric"
```


```r
# slow loop
xs <- runif(1e3)
res <- c()
for (x in xs) {
  # This is slow!
  res <- c(res, sqrt(x))
}

#fast loop
res <- numeric(length(xs))
for (i in seq_along(xs)) {
  res[i] <- sqrt(xs[i])
}
```
