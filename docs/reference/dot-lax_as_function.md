# Permissive as_function replacement.

like
[`rlang::as_function`](https://rlang.r-lib.org/reference/as_function.html)
but only interprets functions or formulae and ignores primitives and
characters.

## Usage

``` r
.lax_as_function(fn)
```

## Arguments

- fn:

  something to interpret as a function or a primitive

## Value

the function or primitive

## Unit tests



    f1 = .lax_as_function(tolower)
    f2 = .lax_as_function(~ tolower(.x))
    f3 = .lax_as_function("hello world")

    testthat::expect_equal(f1, tolower)
    testthat::expect_equal(f2("HELLO WORLD"), f1("HELLO WORLD"))
    testthat::expect_equal(f3, f1("HELLO WORLD"))
