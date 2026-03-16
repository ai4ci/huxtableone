# Search the call stack for a first parameter matching a specified class

Finds the first instance of a function in the call stack that was called
with a object of `.class`. This can be used to find dataframe parameters
in tidy calls

## Usage

``` r
.search_call_stack(.class, nframe = sys.nframe() - 1)
```

## Arguments

- .class:

  the class name to find

- nframe:

  where to start the search

## Value

a `.class` object

## Unit tests



    h = function() {
     df = .search_call_stack(.class = "data.frame")
     return(nrow(df))
    }

    g = function() {h()}

    f = function(x) {g()}

    testthat::expect_equal( f(datasets::iris), nrow(datasets::iris))
