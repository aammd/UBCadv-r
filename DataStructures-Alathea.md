# Data Structures
Alathea  
July 2014  

***

### Quiz

*Take this short quiz to determine if you need to read this chapter. If the answers quickly come to mind, you can comfortably skip this chapter. You can check your answers in answers.*

*What are the three properties of a vector, other than its contents?*

I have no clue.

*What are the four common types of atomic vectors? What are the two rare types?*

Whattt?

*What are attributes? How do you get them and set them?*

Um...

*How is a list different from an atomic vector? How is a matrix different from a data frame?*

A list can contain different (heterogeneous) data types but a vector cannot.  Same thing for a matrix and a data frame.

*Can you have a list that is a matrix? Can a data frame have a column that is a matrix?*

I think maybe.

***

### Vectors

Did not know that R could distinguish doubles and integers:


```r
dbl_vect <- c(1, 2.5, 4.5)

# With the L suffix, you get an integer rather than a double
int_vect <- c(1L, 6L, 10L)

# Use TRUE and FALSE (or T and F) to create logical vectors
log_vect <- c(TRUE, FALSE, T, F)

typeof(dbl_vect)
```

```
## [1] "double"
```

```r
typeof(int_vect)
```

```
## [1] "integer"
```

```r
class(dbl_vect)
```

```
## [1] "numeric"
```

```r
attributes(dbl_vect)
```

```
## NULL
```

Logical to numeric:


```r
as.numeric(log_vect)
```

```
## [1] 1 0 1 0
```

#### Exercises
