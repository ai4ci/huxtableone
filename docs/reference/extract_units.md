# Extracts units set as dataframe column attributes

Extracts units set as dataframe column attributes

## Usage

``` r
extract_units(df)
```

## Arguments

- df:

  the data frame from
  [`set_units()`](https://ai4ci.github.io/huxtableone/reference/set_units.md)

## Value

a named list of column / unit pairs.

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
