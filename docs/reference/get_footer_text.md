# Get footer text if available

The functions in `huxtableone` will record the methods used for
reporting in a scientific paper. This is both for normality assumption
tests and for significance tests.

## Usage

``` r
get_footer_text(df_output)
```

## Arguments

- df_output:

  a data frame that is the output of a `huxtableone` function

## Value

the footnotes if they exist as a list (NULL otherwise)

## Examples

``` r
iris %>% describe_population(tidyselect::everything()) %>% get_footer_text()
#> mean_sd summary for Sepal.Length
#> mean_sd summary for Sepal.Width
#> median_iqr summary for Petal.Length
#> median_iqr summary for Petal.Width
#> subtype_count summary for Species
#> NULL
iris %>% dplyr::group_by(Species) %>%
  compare_population(tidyselect::everything()) %>% get_footer_text()
#> mean_sd summary for Sepal.Length
#> mean_sd summary for Sepal.Width
#> median_iqr summary for Petal.Length
#> median_iqr summary for Petal.Width
#> anova test on Sepal.Length
#> anova test on Sepal.Width
#> kruskal-wallis test on Petal.Length
#> kruskal-wallis test on Petal.Width
#> NULL
```
