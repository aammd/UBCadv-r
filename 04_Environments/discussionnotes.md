    library(pryr)

html preview -- Bookmark

We are *very* confused about the "unique names" vs "multiple names can
point to the same object".

    e <- new.env()
    e$a <- FALSE
    e$b <- "a"
    e$c <- 2.3
    e$d <- 1:3

    e$a <- e$d

    address(e$a)

    ## [1] "0x2177150"

    address(e$d)

    ## [1] "0x2177150"

    e$a[3] <- 3

    address(e$a)

    ## [1] "0x2c4d1f0"

    address(e$d)

    ## [1] "0x2177150"

    e$a; e$d

    ## [1] 1 2 3

    ## [1] 1 2 3

Seems like you actually *can* have two names for the same object, until
one changes.

one interesting use of environments is employed in [this
gist](https://gist.github.com/aammd/2d76c4657ab88901c0ed) written by
[Rich Fitzjohn](http://nicercode.github.io/about/), formerly of UBC. In
it Rich emulates a Lisp-style "linked list" using Reference Semantics
(whatever those are).
