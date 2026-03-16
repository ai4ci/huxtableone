# Summarise a population

The population description is a simple summary of the co-variates in a
data set with no reference to outcome, and not comparing intervention
(although it might contain intervention rates.) It will report summary
statistics for continuous and counts for categorical data,

## Usage

``` r
as_t1_summary(
  df,
  ...,
  label_fn = NULL,
  units = extract_units(df),
  override_type = list()
)
```

## Arguments

- df:

  a dataframe of individual observations. Grouping, if present, is
  ignored. (n.b. if you wanted to construct multiple summary tables a
  [`dplyr::group_map()`](https://dplyr.tidyverse.org/reference/group_map.html)
  call could be used)

- ...:

  the columns of variables we wish to summarise. This can be given as a
  `tidyselect` specification (see
  [`utils::vignette("syntax", package = "tidyselect")`](https://tidyselect.r-lib.org/articles/syntax.html)),
  identifying the columns. Alternatively it can be given as a formula of
  the nature

  `outcome ~ intervention + covariate_1 + covariate_2 + ...` .

  which may be more convenient if you are going on to do a model fit. If
  the latter format the left hand side is ignored (outcomes are not
  usual in this kind of table).

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

- units:

  (optional) a named list of units, following a
  `c(<colname_1> = "<unit_1>", <colname_2> = "<unit_2>", ...)` format.
  columns not present in this list are assumed to have no units. Units
  may be involved in the formatting of the summary output.

- override_type:

  (optional) a named list of data summary types. The default type for a
  column in a data set are calculated using heurisitics depending on the
  nature of the data (categorical or continuous), and result of
  normality tests. if you want to override this the options are
  "subtype_count","median_iqr","mean_sd","skipped" and you specify this
  on a column by column bases with a named list (e.g
  `c("Petal.Width"="mean_sd")`). Overriding the default does not check
  the type of data is correct for the summary type and will potentially
  cause errors if this is not done correctly.

## Value

a `t1_summary` data frame.

## Examples

``` r
tmp = iris %>% as_t1_summary(
  tidyselect::everything(),
  override_type = c(Petal.Length = "mean_sd", Petal.Width = "mean_sd")
)
#> mean_sd summary for Sepal.Length
#> mean_sd summary for Sepal.Width
#> mean_sd summary for Petal.Length
#> mean_sd summary for Petal.Width
#> subtype_count summary for Species
```
