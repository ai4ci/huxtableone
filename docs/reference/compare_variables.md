# Get summary comparisons and statistics between variables as raw data.

Get summary comparisons and statistics between variables as raw data.

## Usage

``` r
compare_variables(
  df,
  ...,
  label_fn = ~.x,
  override_type = list(),
  p_format = names(.pvalue.defaults)
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

  the outcomes are specified either as a `tidyselect` specification, in
  which case the grouping of the `df` input determines the intervention
  and the output is the same a
  [`compare_population()`](https://ai4ci.github.io/huxtableone/reference/compare_population.md)
  call with a tidyselect. Alternatively a set of formulae can be
  provided that specify the outcomes on the left hand side, e.g.
  `outcome1 ~ intervention + cov1, outcome2 ~ intervention + cov1, ...`
  in this case the `intervention` must be the same for all formulae and
  used to determine the comparison groups.

- label_fn:

  (optional) a function for mapping a co-variate column name to
  printable label. This is by default a no-operation and the output
  table will contain the dataframe column names as labels. A simple
  alternative would be some form of
  [dplyr::case_when](https://dplyr.tidyverse.org/reference/case_when.html)
  lookup, or a string function such as
  [stringr::str_to_sentence](https://stringr.tidyverse.org/reference/case.html).
  (N.b. this function must be vectorised). Any value provided here will
  be overridden by the `options("huxtableone.labeller" = my_label_fn)`
  which allows global setting of the labeller.

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

- p_format:

  the format of the p-values: one of "sampl", "nejm", "jama", "lancet",
  "aim" but any value here is overridden by the
  `option("huxtableone.pvalue_formatter"=function(...))`

## Value

a `huxtable` formatted table.
