# crochet

[![Travis-CI Build Status](https://travis-ci.org/agrueneberg/crochet.svg?branch=master)](https://travis-ci.org/agrueneberg/crochet)
[![CRAN_Status_Badge](http://www.r-pkg.org/badges/version/crochet)](https://cran.r-project.org/package=crochet)
[![Rdoc](http://www.rdocumentation.org/badges/version/crochet)](http://www.rdocumentation.org/packages/crochet)

Functions to help implement the extraction / subsetting / indexing function `[` and replacement function `[<-` of custom matrix-like types (based on S3, S4, etc.), modeled as closely to the base `matrix` class as possible (with tests to prove it).


## Example

`extract` is a function that accepts two arguments `extract_vector` (in the form of `function(x, i)`) and `extract_matrix` (in the form of `function(x, i, j)`), and returns a function that can be used as a method for `[` for a custom type.

The following example creates a dummy matrix `b` and an instance `a` of a custom type called `TestMatrix`. `TestMatrix` is an S3 "class" that in addition to the `[` implements methods for `dim` and `dimnames`. In this case, the `extract_vector` and `extract_matrix` function close over `b` and simply delegate the subsetting. Note that the `[` character is not allowed in a variable name, so it needs to be escaped with backticks.

```R
b <- matrix(data = rnorm(25), nrow = 5, ncol = 5)
dimnames(b) <- list(letters[1:5], letters[1:5])

a <- structure(list(), class = "TestMatrix")

dim.TestMatrix <- function(x) c(5, 5)

dimnames.TestMatrix <- function(x) dimnames(b)

extract_vector <- function(x, i) {
    # Dispatch to b instead to x for this demo
    b[i, drop = FALSE]
}

extract_matrix <- function(x, i, j) {
    # Dispatch to b instead to x for this demo
    b[i, j, drop = FALSE]
}

`[.TestMatrix` <- extract(extract_vector = extract_vector, extract_matrix = extract_matrix)

b[1, ] # Get the subset from the source
a[1, ] # Get the subset through the extract function
```
