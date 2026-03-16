# Compares the population against an intervention

The population comparison is a summary of the co-variates in a data set
with no reference to outcome, but comparing intervention groups. It will
report summary statistics for continuous and counts for categorical
data, for each of the intervention groups, and reports on the
significance of the association in relation to the intervention groups.
It gives a clear summary of whether data is correlated to intervention.

## Usage

``` r
as_t1_signif(
  df,
  ...,
  label_fn = NULL,
  units = extract_units(df),
  override_type = list(),
  override_method = list()
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

  the columns of variables we wish to summarise. This can be given as a
  `tidyselect` specification (see
  [`utils::vignette("syntax", package = "tidyselect")`](https://tidyselect.r-lib.org/articles/syntax.html)),
  identifying the columns. Alternatively it can be given as a formula of
  the nature

  `outcome ~ intervention + covariate_1 + covariate_2 + ...` .

  which may be more convenient if you are going on to do a model fit
  later. If the latter format the left hand side is ignored (outcomes
  are not usual in this kind of table).

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

- override_method:

  if you want to override the comparison method for a particular
  variable the options are "chi-sq trend","fisher","t-test","2-sided
  wilcoxon","2-sided ks","anova","kruskal-wallis","no comparison" and
  you specify this on a column by column bases with a named list (e.g
  `c("Petal.Width"="t-test")`)

## Value

a `t1_signif` dataframe.

## Examples

``` r
tmp = iris %>% dplyr::group_by(Species) %>% as_t1_signif(tidyselect::everything())
#> mean_sd summary for Sepal.Length
#> mean_sd summary for Sepal.Width
#> median_iqr summary for Petal.Length
#> median_iqr summary for Petal.Width
#> anova test on Sepal.Length
#> anova test on Sepal.Width
#> kruskal-wallis test on Petal.Length
#> kruskal-wallis test on Petal.Width
tmp = diamonds %>% dplyr::group_by(is_colored) %>% as_t1_signif(tidyselect::everything())
#> median_iqr summary for carat
#> subtype_count summary for cut
#> subtype_count summary for color
#> subtype_count summary for clarity
#> median_iqr summary for depth
#> median_iqr summary for table
#> median_iqr summary for price
#> median_iqr summary for x
#> median_iqr summary for y
#> median_iqr summary for z
#> 2-sided wilcoxon test on carat
#> chi-sq trend test on cut
#> chi-sq trend test on color
#> chi-sq trend test on clarity
#> 2-sided wilcoxon test on depth
#> 2-sided wilcoxon test on table
#> 2-sided wilcoxon test on price
#> 2-sided wilcoxon test on x
#> 2-sided wilcoxon test on y
#> 2-sided wilcoxon test on z
```
