# Data Structures

## Vectors and Lists Exercises

*1) Integer, character, raw, double, logical, complex are the 6 types of atomic vectors. Lists are recursive, but atomic vectors are flat.
*2) `is.vector` and `is.numeric` are general tests, not specific tests for types. `is.list` and `is.character` test for exact types.
*3) ```{r}
    c(1, FALSE) # c(1, 0)
    c("a", 1) # c("a","1")
    c(list(1),"a") # list of 2
    c(TRUE, 1L) # c(1,1)
    ```
*4) `as.vector` simply removes attributes, while unlist simplifies the list structure.   
*5) 1 == "1" because R coerces 1 to "1" in the comparison. -1 < FALSE is true because  R coerces FALSE to 0 in the comparison. "one" < 2 is false because R coerces 2 to "2" and "2" comes before "one" alphabetically.
*6) ??

## Attributes

*1) Choosing attribute tags that are not `comment` print fine. `comment` is a special case that is not printed.
*2) `levels(f1) <- rev(levels(f1)` Reversing the factor levels reversed both the vector and the levels. (z:a, z:a)
*3) `rev(factor(letters))` Reversing the factor reversed the vector, but left the levels intact (f2). (z:a, a,z)
`factor(letters, levels = rev(letters))` reversed the levels, but not the vector (a:z, z:a)

What's the difference between `levels(f1) <- rev(levels(f1))` and `factor(letters, levels=rev(letters))`? The help says "Note that for a factor, replacing the levels via `levels(x) <- value` is not the same as (and is preferred to) `attr(x, "levels") <- value`." I don't understand why the first one actually modifies the factor and the other only acts on the levels.

## Matrices and Arrays

*1) `dim()` returns `NULL` when applied to a vector
*2) matrices are arrays
*3) x1 is 1 row by 1 column by 5 deep
    x2 is 1 row by 5 columns by 1 deep
    x3 is 5 rows by 1 column by 1 deep
    They are different from 1:5 because of their dimensionality.

I don't really understand the difference between `typeof()` and `class()`

## Dataframes
*1) Rows, columns, names
*2) as.matrix will coerce columns of different types to the same type.
*3) You can have a data frame with 0 rows, but not a data frame with 0 columns. ??
