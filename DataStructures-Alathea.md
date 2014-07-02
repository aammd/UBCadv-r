# Data Structures
Alathea  
July 2014  

***

### Discussion Notes

***

### Quiz

*Take this short quiz to determine if you need to read this chapter. If the answers quickly come to mind, you can comfortably skip this chapter. You can check your answers in answers.*

1.  *What are the three properties of a vector, other than its contents?* **I have no clue.**
1.  *What are the four common types of atomic vectors? What are the two rare types?* **Whattt?**
1.  *What are attributes? How do you get them and set them?* **Um...**
1.  *How is a list different from an atomic vector? How is a matrix different from a data frame?* **A list can contain different (heterogeneous) data types but a vector cannot.  Same thing for a matrix and a data frame.**
1.  *Can you have a list that is a matrix? Can a data frame have a column that is a matrix?* **I think maybe.**

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

- *What are the six types of atomic vector? How does a list differ from an atomic vector?*

Types of atomic vector = logical, double, integer, character and two rare types.  A list can be heterogeneous, unlike an atomic vector.

- *What makes `is.vector()` and `is.numeric()` fundamentally different to `is.list()` and `is.character()`?*

`is.vector` and `is.numeric` are not specific tests for vectors but `is.character` and `is.list` can be used to test for character vector, or a list

- *Why do you need to use `unlist()` to convert a list to an atomic vector? Why doesn’t `as.vector()` work?*

Lists are also vectors so `as.vector` will not coerce to an atomic vector, just leave as a list


```r
x <- list("a", "b", "c")
as.vector(x)
```

```
## [[1]]
## [1] "a"
## 
## [[2]]
## [1] "b"
## 
## [[3]]
## [1] "c"
```

-  *Why is `1 == "1"` true? Why is `-1 < FALSE` true? Why is `"one" < 2` false?*

Probably to do with coercion.  `1` is coerced to `"1"`.  `FALSE` is coerced to `1`.  `2` is coerced to `"2"` and you cannot compare character vectors with `>` `<`

- *Why is the default missing value, NA, a logical vector? What’s special about logical vectors? (Hint: think about `c(FALSE, NA_character_)`.)*

Maybe because it is the least flexible data type... `c(FALSE, NA_character_)` would cause `FALSE` to change data type.

***

### Attributes


```r
test_data <- data.frame("Species" = LETTERS[1:26], 
                        "Abundance" = sample(0:50, 26, replace=TRUE))
attr(test_data, "Description") <- "Species Abundance Data, Brazil 2014"

head(test_data)
```

```
##   Species Abundance
## 1       A        30
## 2       B         6
## 3       C        37
## 4       D        44
## 5       E        27
## 6       F         5
```

```r
attributes(test_data)
```

```
## $names
## [1] "Species"   "Abundance"
## 
## $row.names
##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23
## [24] 24 25 26
## 
## $class
## [1] "data.frame"
## 
## $Description
## [1] "Species Abundance Data, Brazil 2014"
```

#### Factors

**"While factors look (and often behave) like character vectors, they are actually integers"**!

#### Exercises

- *An early draft used this code to illustrate structure():*


```r
structure(1:5, comment = "my attribute")
```

*But when you print that object you don’t see the comment attribute. Why? Is the attribute missing, or is there something else special about it? (Hint: try using help)*


```r
attributes(structure)
```

```
## NULL
```

```r
help(attributes)
```

`comment`: "These functions set and query a comment attribute for any R objects. This is typically useful for data.frames or model fits.

Contrary to other attributes, the comment is not printed (by `print` or `print.default`)."
