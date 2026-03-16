# Make NA values in factor columns explicit

Converts NA values in any factors in the dataframe into a new level -
This is a thin wrapper for
[`forcats::fct_explicit_na()`](https://forcats.tidyverse.org/reference/fct_explicit_na.html)
but with missing value level added regardless of whether any values
missing. This forces an empty row in count tables.

## Usage

``` r
explicit_na(df, na_level = "<missing>", hide_if_empty = FALSE)
```

## Arguments

- df:

  the data frame

- na_level:

  a label for NA valued factors

- hide_if_empty:

  dont add a missing data category if no data is missing

## Value

the dataframe with all factor columns containing explicit na values

## Examples

``` r
# before
missing_diamonds %>% dplyr::group_by(cut) %>% dplyr::count()
#> # A tibble: 6 × 2
#> # Groups:   cut [6]
#>   cut           n
#>   <ord>     <int>
#> 1 Fair       1454
#> 2 Good       4462
#> 3 Very Good 10816
#> 4 Premium   12460
#> 5 Ideal     19361
#> 6 NA         5387
# after
missing_diamonds %>% explicit_na() %>% dplyr::group_by(cut) %>% dplyr::count()
#> # A tibble: 6 × 2
#> # Groups:   cut [6]
#>   cut           n
#>   <fct>     <int>
#> 1 Fair       1454
#> 2 Good       4462
#> 3 Very Good 10816
#> 4 Premium   12460
#> 5 Ideal     19361
#> 6 <missing>  5387
```
