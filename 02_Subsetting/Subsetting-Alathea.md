# Subsetting
Alathea  
2014-07-09  

***

### Discussion Notes

***

### Quiz

1. *What does subsetting a vector with: positive integers, negative integers, logical vector, character vector?*

**I guess this means what happens when you subset a vector with one of the above.  A positive integer will select the item at that index and a negative integer will remove the item at that index.  I don't know about the vectors.**

1. *What’s the difference between `[`, `[[` and `$` when applied to a list?*

**Subsetting with `[` will select a list at that location but `[[` selects the item within that list.  `$` works for a named list.**

1. *When should you use `drop = FALSE`?*

**No one knows!**

1. *If x is a matrix, what does `x[] <- 0` do? How is it different to `x <- 0`?*

**Don't know.**

1. *How can you use a named vector to relabel categorical variables?*

**Maybe `names(data_frame) <- names(vector)` or something like that.**

***

### Data Types

#### Atomic Vectors


```r
x <- c(1:10)
x[2]
```

```
## [1] 2
```

```r
x[c(5,9)]
```

```
## [1] 5 9
```

```r
x[-c(5:10)]
```

```
## [1] 1 2 3 4
```

```r
x[c(TRUE,FALSE,TRUE,TRUE,FALSE)]
```

```
## [1] 1 3 4 6 8 9
```

An interesting thing about subsetting with a logical vector is that if the logical vector is shorter than the vector being subsetted, it will act like a pattern and repeat over the entire vector (see above).  I guess this is called recycling.

You can use character vectors to select from a named vector.  I did not know that was possible!


```r
y <- c(1:10)
names(y) <- letters[1:10]
y
```

```
##  a  b  c  d  e  f  g  h  i  j 
##  1  2  3  4  5  6  7  8  9 10
```

#### Matrices and Arrays

Arrays are stored in column order.  If you subset with a number, e.g. `array[2]` it will count down through each item of each column.

#### Data Frames

List-type vs. Matrix type subestting:


```r
df <- data.frame(x = 1:3, y = 3:1, z = letters[1:3])

# There's an important difference if you select a single column:
# matrix subsetting simplifies by default, list subsetting does not.
str(df["x"])
```

```
## 'data.frame':	3 obs. of  1 variable:
##  $ x: int  1 2 3
```

```r
str(df[, "x"])
```

```
##  int [1:3] 1 2 3
```

