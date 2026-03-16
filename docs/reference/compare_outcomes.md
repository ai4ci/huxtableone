# Compares multiple outcomes against an intervention in a summary table

The outcome table is a simple summary of a binary or categorical outcome
in a data set compared by intervention groups. The comparison is
independent of any covariates, and is a preliminary output prior to more
formal statistical analysis or model fitting.

## Usage

``` r
compare_outcomes(
  df,
  ...,
  label_fn = NULL,
  units = extract_units(df),
  override_type = list(),
  layout = "compact",
  override_percent_dp = list(),
  override_real_dp = list(),
  p_format = names(.pvalue.defaults),
  font_size = getOption("huxtableone.font_size", 8),
  font = .default_font(),
  footer_text = NULL,
  show_binary_value = NULL,
  raw_output = FALSE
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
  and the output is the same as a
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

- layout:

  (optional) various layouts are defined as default. As of this version
  of `huxtableone` they are
  "relaxed","compact","micro","simple","single","missing". The layouts
  can be customised using the options
  `options("huxtableone.format_list"=list(...)")`, and this is described
  in more detail in the vignettes.

- override_percent_dp:

  (optional) a named list of overrides for the default precision of
  formatting percentages, following a
  `c(<colname_1> = 2, <colname_2> = 4, ...)` format. columns not present
  in this list will use the defaults defined in the layout. See the
  vignette on customisation.

- override_real_dp:

  (optional) a named list of overrides for the default precision of
  formatting real values, following a
  `c(<colname_1> = 2, <colname_2> = 4, ...)` format. columns not present
  in this list will use the defaults defined in the layout. See the
  `utils::vignette("customisation", package="huxtableone")`.

- p_format:

  the format of the p-values: one of "sampl", "nejm", "jama", "lancet",
  "aim" but any value here is overridden by the
  `option("huxtableone.pvalue_formatter"=function(...))`

- font_size:

  (optional) the font size for the table in points

- font:

  (optional) the font family for the table (which will be matched to
  closest on your system)

- footer_text:

  any text that needs to be added at the end of the table, setting this
  to FALSE dsables the whole footer (as does
  `options("huxtableone.hide_footer"=TRUE)`).

- show_binary_value:

  if set this will filter the display of covariates where the number of
  possibilities is exactly 2 to this value.

- raw_output:

  return comparison as `t1_signif` dataframe rather than formatted table

## Value

a `huxtable` formatted table.

## Details

It reports summary counts for the outcomes and a measure of significance
of the relationship between outcome and intervention. Interpretation of
significance tests, should include Bonferroni adjustment.
