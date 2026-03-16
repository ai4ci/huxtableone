# Get the defined name of a function

Get the defined name of a function

## Usage

``` r
.get_fn_name(fn)
```

## Arguments

- fn:

  a function

## Value

the name as a string, as the function was defined

## Unit tests



    f = tolower

    testthat::expect_equal(
      .get_fn_name(f),
      .get_fn_name(tolower)
    )

    testthat::expect_equal(
      .get_fn_name(.get_fn_name),
      ".get_fn_name"
    )

    testthat::expect_equal(
      .get_fn_name(function(x) x),
      "<unknown>"
    )
