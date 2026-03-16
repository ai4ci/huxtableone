# Title

Title

## Usage

``` r
set_units(df, ..., units)
```

## Arguments

- df:

  a dataframe

- ...:

  a tidyselect specification or a formula

- units:

  a list of unit as strings which must be either 1 or the same length as
  the columns matched by the tidyselect.

## Value

the dataframe with the `unit` attribute updated

## Examples

``` r
iris = iris %>% set_units(-Species, units="mm")
iris %>% extract_units()
#> Sepal.Length  Sepal.Width Petal.Length  Petal.Width 
#>         "mm"         "mm"         "mm"         "mm" 
#> attr(,"na.action")
#> Species 
#>       5 
#> attr(,"class")
#> [1] "omit"
```
