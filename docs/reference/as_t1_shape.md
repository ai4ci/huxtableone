# Summarise a data set

The data set description is a simple summary of the data formats, types
and missingness

## Usage

``` r
as_t1_shape(df, ..., label_fn = NULL, units = extract_units(df))
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

## Value

a `t1_shape` data frame.

## Examples

``` r
tmp = iris %>% as_t1_shape(
  tidyselect::everything()
)
```
