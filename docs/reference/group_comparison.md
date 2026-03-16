# Extract one or more comparisons for inserting into text.

At some point we need to take information from the tables produced by
`huxtableone` and place it into the main text of the document. It is
annoying if this cannot be done automatically. the `group_comparison()`
function enables extraction of one or more head to head comparisons and
provides a fairly flexible mechanism for building the precise format
desired.

## Usage

``` r
group_comparison(
  t1_signif,
  variable = NULL,
  subgroup = NULL,
  intervention = NULL,
  percent_fmt = "%1.1f%%",
  p_format = names(.pvalue.defaults),
  no_summary = FALSE,
  summary_glue = NULL,
  summary_arrange = NULL,
  summary_sep = ", ",
  summary_last = " versus ",
  no_signif = FALSE,
  signif_glue = NULL,
  signif_sep = NULL,
  signif_last = NULL
)
```

## Arguments

- t1_signif:

  a `t1_signif` as produced by
  [`as_t1_signif()`](https://ai4ci.github.io/huxtableone/reference/as_t1_signif.md)
  or `compare_population(..., raw_output = TRUE)`.

- variable:

  a variable or set of variables to compare. If missing a set of
  approriate values is displayed based on the columns of `t1_signif`

- subgroup:

  a subgroup or set of subgroups to compare.

- intervention:

  the side or sides of the intervention to select. N.b. using this
  effectively prevents any statistical comparison as only one side will
  be available.

- percent_fmt:

  a `sprintf` format string that is applied to probability fields in the
  summary data to convert to percentages.

- p_format:

  the format of the p-values: one of "sampl", "nejm", "jama", "lancet",
  "aim" but any value here is overridden by the
  `option("huxtableone.pvalue_formatter"=function(...))`

- no_summary:

  only extract significance test values

- summary_glue:

  a glue specification that maps the summary statistics to a readable
  string.

- summary_arrange:

  an expression by which to order the summary output

- summary_sep:

  a separator to combine the summary output (see
  [`glue::glue_collapse()`](https://glue.tidyverse.org/reference/glue_collapse.html))

- summary_last:

  a separator to combine the last 2 summary outputs (see
  [`glue::glue_collapse()`](https://glue.tidyverse.org/reference/glue_collapse.html))

- no_signif:

  do not try and include significance in the output. Sometimes this is
  the only option if there is not enough of the comparison to retained
  by the `variable`, `subgroup`, and `intervention` filters.
  (Specifically if there is only a comparison between different
  subgroups, as the p-values will be for the different comparison
  between intervention groups.)

- signif_glue:

  a glue specification that maps the combined summary output with the
  result of the significance tests, to given a complete comparison.

- signif_sep:

  a separator to combine complete comparisons (see
  [`glue::glue_collapse()`](https://glue.tidyverse.org/reference/glue_collapse.html))

- signif_last:

  a separator to combine the last 2 complete comparisons (see
  [`glue::glue_collapse()`](https://glue.tidyverse.org/reference/glue_collapse.html))

## Value

ideally a single string but various things will be returned depending on
hos much input is constrained, and sometimes will provide guidance about
what next to do. The intention is the function to be used interactively
until a satisfactory result is obtained.

## Examples

``` r
tmp = diamonds %>%
  dplyr::group_by(is_colored) %>%
  set_units(price,units="£") %>%
  compare_population(-color, raw_output=TRUE)
#> median_iqr summary for carat
#> subtype_count summary for cut
#> subtype_count summary for clarity
#> median_iqr summary for depth
#> median_iqr summary for table
#> median_iqr summary for price
#> median_iqr summary for x
#> median_iqr summary for y
#> median_iqr summary for z
#> 2-sided wilcoxon test on carat
#> chi-sq trend test on cut
#> chi-sq trend test on clarity
#> 2-sided wilcoxon test on depth
#> 2-sided wilcoxon test on table
#> 2-sided wilcoxon test on price
#> 2-sided wilcoxon test on x
#> 2-sided wilcoxon test on y
#> 2-sided wilcoxon test on z

# The tabular output is retrieved by converting to a huxtable
# as_huxtable(tmp, layout="simple")

# An unqualified group_comparison call gives informative messages
# about what can be compared:
tmp %>% group_comparison()
#> * `variable` can be any of:
#> `carat`,`cut`,`clarity`,`depth`,`table`,`price`,`x`,`y`,`z`
#> * `subgroup` can be any of:
#> `Fair`,`Good`,`Very Good`,`Premium`,`Ideal`,`I1`,`SI2`,`SI1`,`VS2`,`VS1`,`VVS2`,`VVS1`,`IF`
#> * `intervention` can be any of:
#> `clear`,`colored`
#> * `summary_glue` can use any of the following variables:
#> `variable`, `unit`, `is_colored`, `q.0.025`, `q.0.05`, `q.0.25`, `q.0.5`, `q.0.75`, `q.0.95`, `q.0.975`, `n`, `N`, `.order2`, `subgroup`, `x`, `prob.0.5`, `prob.0.025`, `prob.0.975`, `N_total`, `N_present`

# filtering down the data gets us to a specific comparison:
tmp %>% group_comparison(variable = "cut", subgroup="Fair") %>% dplyr::glimpse()
#> * `intervention` can be any of:
#> `clear`,`colored`
#> * `summary_glue` can use any of the following variables:
#> `variable`, `unit`, `is_colored`, `subgroup`, `n`, `x`, `prob.0.5`, `prob.0.025`, `prob.0.975`, `.order2`, `N`, `N_total`, `N_present`
#> Rows: 2
#> Columns: 13
#> Groups: variable, subgroup, is_colored [2]
#> $ variable   <chr> "cut", "cut"
#> $ unit       <chr> "", ""
#> $ is_colored <fct> clear, colored
#> $ subgroup   <fct> Fair, Fair
#> $ n          <int> 16572, 37368
#> $ x          <int> 387, 1223
#> $ prob.0.5   <chr> "2.3%", "3.3%"
#> $ prob.0.025 <chr> "2.1%", "3.1%"
#> $ prob.0.975 <chr> "2.6%", "3.5%"
#> $ .order2    <int> 1, 1
#> $ N          <int> 16572, 37368
#> $ N_total    <int> 53940, 53940
#> $ N_present  <int> 53940, 53940

# With further interactive exploration the
# data available for that comparison can be made into a glue string
tmp %>% group_comparison(variable = "cut", subgroup="Fair", intervention = "clear",
  summary_glue = "{is_colored}: {x}/{n} ({prob.0.5}%)",
  signif_glue = "{variable}={subgroup}; {text}; Overall p-value for '{variable}': {p.value}.")
#> [1] "clear: 387/16572 (2.3%%)"

# group comparisons above using many individual subgroups are a bit confusing because
# the p-value is at the variable level. This is less of an issue for continuous
# or binary values.
tmp %>% group_comparison(
  variable = "price",
  summary_glue = "{is_colored}: {unit}{q.0.5}; IQR: {q.0.25} \u2014 {q.0.75} (n={n})",
  signif_glue = "{variable}: {text}; P-value {p.value}.")
#> * `intervention` can be any of:
#> `clear`,`colored`
#> price: clear: £1781; IQR: 894.75 — 4092 (n=16572) versus colored: £2777.5; IQR: 990 — 6006.25 (n=37368); P-value <0.001.

# Sometimes we only want to extract a p-value:
tmp %>%
  group_comparison(variable = "cut", subgroup="Fair", no_summary=TRUE) %>%
  dplyr::glimpse()
#> * `intervention` can be any of:
#> `clear`,`colored`
#> * `signif_glue` can use any of the following variables:
#> `variable`, `p.value`, `p.method`
#> Rows: 1
#> Columns: 3
#> Groups: variable [1]
#> $ variable <chr> "cut"
#> $ p.value  <chr> "0.6"
#> $ p.method <chr> "Chi-squared Test for Trend in Proportions"
```
