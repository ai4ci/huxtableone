# Convert a `t1_signif` S3 class to a huxtable

This is responsible for printing the significance test results and
comparison

## Usage

``` r
# S3 method for class 't1_signif'
as_huxtable(
  x,
  ...,
  layout = "compact",
  override_percent_dp = list(),
  override_real_dp = list(),
  p_format = names(.pvalue.defaults),
  font_size = getOption("huxtableone.font_size", 8),
  font = .default_font(),
  footer_text = NULL,
  show_binary_value = NULL
)
```

## Arguments

- x:

  the `t1_signif` result as calculated by `compare_population(...)`

- ...:

  not used

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

## Value

a formatted table as a `huxtable`

## Examples

``` r
library(huxtableone)
tmp = iris %>% dplyr::group_by(Species) %>%
  as_t1_signif(tidyselect::everything()) %>%
  huxtable::as_huxtable()
#> mean_sd summary for Sepal.Length
#> mean_sd summary for Sepal.Width
#> median_iqr summary for Petal.Length
#> median_iqr summary for Petal.Width
#> anova test on Sepal.Length
#> anova test on Sepal.Width
#> kruskal-wallis test on Petal.Length
#> kruskal-wallis test on Petal.Width
#> Warning: The requested font(s): [Arial], are not present on the system. Alternatives will be used.
#> This warning is displayed once per session.
```
