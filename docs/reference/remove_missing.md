# Remove variables that fail a missing data test from models

Comparing missingness by looking at a table is good but we also want to
update models to exclude missing data from the predictors.

## Usage

``` r
remove_missing(
  df,
  ...,
  label_fn = NULL,
  significance_limit = 0.05,
  missingness_limit = 0.1
)
```

## Arguments

- df:

  a dataframe of individual observations. If using the `tidyselect`
  syntax data grouping defines the intervention group and should be
  present. if the formula interface is used the first variable in the
  right hand side of the formula is used as the intervention, in which
  case grouping is ignored.

- ...:

  a list of formulae that specify the models that we want to check

- label_fn:

  (optional) a function for mapping a co-variate column name to
  printable label. This is by default a no-operation and the output
  table will contain the dataframe column names as labels. A simple
  alternative would be some form of
  [dplyr::case_when](https://dplyr.tidyverse.org/reference/case-and-replace-when.html)
  lookup, or a string function such as
  [stringr::str_to_sentence](https://stringr.tidyverse.org/reference/case.html).
  (N.b. this function must be vectorised). Any value provided here will
  be overridden by the `options("huxtableone.labeller" = my_label_fn)`
  which allows global setting of the labeller.

- significance_limit:

  the limit at which we reject the hypothesis that the data is missing
  at random.

- missingness_limit:

  the limit at which too much data is missing to include the predictor.

## Value

a list of formulae with missing parameters removed

## Examples

``` r
df = iris %>%
  dplyr::mutate(Petal.Width = ifelse(
    stats::runif(dplyr::n()) < dplyr::case_when(
      Species == "setosa" ~ 0.2,
      Species == "virginica" ~ 0.1,
      TRUE~0
    ),
    NA,
    Petal.Width
  ))
remove_missing(df, ~ Species + Petal.Width + Sepal.Width, ~ Species + Petal.Length + Sepal.Length)
#> subtype_count summary for Petal.Width
#> subtype_count summary for Sepal.Width
#> subtype_count summary for Petal.Length
#> subtype_count summary for Sepal.Length
#> fisher test on Petal.Width
#> fisher test on Sepal.Width
#> fisher test on Petal.Length
#> fisher test on Sepal.Length
#> More than 10% of data is missing for variables Petal.Width.
#> Data is missing not at random (compared to Species) at a p-value<0.013 (0.05 over 4 comparisons) for variables Petal.Width.
#> [[1]]
#> . ~ Species + Sepal.Width
#> <environment: 0x60c93554f208>
#> 
#> [[2]]
#> . ~ Species + Petal.Length + Sepal.Length
#> <environment: 0x60c93554f208>
#> 
```
