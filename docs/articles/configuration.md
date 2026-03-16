# huxtableone: Configuration

## Configuration and formatting options

This vignette provides examples of some of the formatting options. To
demonstrate them we will use the
[`survival::cgd`](https://rdrr.io/pkg/survival/man/cgd.html) dataset:

``` r
# set up the data 
gcd = survival::cgd %>% 
  # filter to include only the first visit
  dplyr::filter(enum==1) %>% 
  # make the steroids and propylac columns into a logical value
  # see later for a better way of doing this.
  dplyr::mutate(
    steroids = as.logical(steroids),
    propylac = as.logical(propylac)
  )
  

# A basic unstratified population description table is as follows:
formula = Surv(tstart, tstop, status) ~ treat + 
  sex + age + height + weight + inherit + steroids + hos.cat

gcd %>% compare_population(formula)
```

    ## Warning: The requested font(s): [Arial], are not present on the system. Alternatives will be used.
    ## This warning is displayed once per session.

[TABLE]

### Column labelling

- A custom labeller function can be defined for the table.

``` r
# set a table relabelling function
rename_cols = function(col) {
  dplyr::case_when(
    col == "hos.cat" ~ "Location",
    col == "steroids" ~ "Steroid treatment",
    TRUE ~ stringr::str_to_sentence(col)
  )
}

# set it using an option
# we are not going to reset this as we will use in all the subsequent examples:
options("huxtableone.labeller"=rename_cols)

gcd %>% compare_population(formula) 
```

[TABLE]

### Content format

- Change the decimal point
- Change the font and font size
- Change the labelling of the p-value column
- Change the format of the p-value
- Hide the daggers for the method for the p-value

``` r
old = options(
  # set a mid point as decimal point
  "huxtableone.dp"="\u00B7",
  "huxtableone.font"="Arial Narrow",
  "huxtableone.font_size"=12,
  "huxtableone.pvalue_column_name"="p-value",
  # the p-value formatter must be a function that takes a vector of numbers and returns
  # a vector of characters. The example here is a function that returns a function.
  "huxtableone.pvalue_formatter" = 
          scales::label_pvalue(accuracy = 0.01,decimal.mark = "\u00B7"),
  "huxtableone.show_pvalue_method"=FALSE
)


gcd %>% compare_population(formula) 
```

    ## Warning: The requested font(s): [Arial Narrow], are not present on the system. Alternatives will be used.
    ## This warning is displayed once per session.

[TABLE]

``` r
# reset
options(old)
```

### Summary types

The default statistics may seem wrong for the data, particularly the
decision around whether to present mean or median, which depends on the
detection of normality in the data. The presentation can be overridden
by supplying a named list to `override_type`, the names here being the
original column names to override. This won’t change the method of
detection of significance which depends on the detection of normality.
The test type and significance levels are also configurable.

``` r
# override_type - names list of column names and summary type out of options

# with this looser definition of normality (i.e. less likely to reject the null
# that the data is normally distributed), height and weight are found to be
# and hence the t-test is used.
old = options(
  "huxtableone.normality_test"="lillie",
  "huxtableone.normality_significance"=0.00001
)

gcd %>% compare_population(
    formula,
    # age is still not normally distributed but we can override it to be 
    # presented as a mean and SD.
    override_type = list(age="mean_sd")
  )
```

[TABLE]

``` r
options(old)
```

``` r
# the following option also controls which parametric test is chosen (between)
# wilcoxon and ks tests:
# options("huxtableone.tolerance_to_ties"=0.25)
```

### Customising the number of decimal places

Need to change this on a column by column basis (eg. here reals using a
named list) or on a systematic bases (e.g. percent). Specification can
either be as fixed (e.g. “2f”) or significant figures (e.g. “3g”). N.b.
This setting is independent of the p-value formatter.

``` r
gcd %>% compare_population(
    formula,
    # can supply either the "5f" (for 5 digits floating point) or "6g"
    # for 6 significant figures syntax:
    override_real_dp = list(age="0f",height="0f",weight="2f"),
    # or a plain set of numbers. If the option is unnamed it is applied to 
    # all the variables:
    override_percent_dp = 0
  )
```

    ## Warning: Using `by = character()` to perform a cross join was deprecated in dplyr 1.1.0.
    ## ℹ Please use `cross_join()` instead.
    ## ℹ The deprecated feature was likely used in the huxtableone package.
    ##   Please report the issue at <https://github.com/ai4ci/huxtableone/issues>.
    ## This warning is displayed once per session.
    ## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
    ## generated.

[TABLE]

### Summary format customisation

Standard layouts are defined, “relaxed”, “compact”, “micro”, “simple”,
“single”, “missing”, and these can be used in the layout parameter to
give a particular format to the columns and content of the table.

``` r
gcd %>% compare_population(
    formula,
    layout = "relaxed"
  )
```

[TABLE]

### Custom layouts

The “relaxed” standard layout is defined using a list. This is shown
below:

``` r
list(
    subtype_count = list(
        characteristic = "{level} % [95% CI]",
        Value = "{.sprintf_na('%1.1f%% [%1.1f%%—%1.1f%%]',prob.0.5*100,prob.0.025*100,prob.0.975*100)}",
        `Count (N={N})` = "{.sprintf_na('%d/%d',x,n)}"
    ),
    median_iqr = list(
        characteristic = "Median [IQR]",
        Value = "{.sprintf_na('%1.3g %s [%1.3g—%1.3g]',q.0.5,unit,q.0.25,q.0.75)}",
        `Count (N={N})` = "{.sprintf_na('%d',n)}"
    ),
    mean_sd = list(
        characteristic = "Mean ± SD", Value = "{.sprintf_na('%1.3g %s ± %1.3g',mean,unit,sd)}",
        `Count (N={N})` = "{.sprintf_na('%d',n)}"
    ),
    skipped = list(
        characteristic = "—", Value = "—",
        `Count (N={N})` = "{.sprintf_na('%d',n)}"
    )
)
```

We can produce a customised list based on this and supply it to a
formatting function as the `layout` parameter. The named list defines
the column name and the column contents, at the moment one item in this
list must be named `characteristic`. The column contents can refer to
the following variables:

- `subtype_count` can use `{level}`, `{prob.0.5}`, `{prob.0.025}`,
  `{prob.0.975}`, `{x}`, `{n}`, `{N}` - `x` is subgroup count, `n` is
  data count excluding missing, `N` includes missing.
- `median_iqr` can use `{q.0.5}`, `{q.0.25}`, …, `{unit}`, `{n}`,
  `{N}` - `n` excludes missing, `N` does not.
- `mean_sd` can use `{mean}`, `{sd}`, `{unit}`, `{n}`, `{N}` - `n`
  excludes missing, `N` does not.
- `skipped` can use `{unit}`, `{n}`, `{N}` - `n` excludes missing, `N`
  does not.

Other than the characteristic column, the column names are derived from
the names of the custom configuration list. The names can also be
configured using `glue` and this can use intervention level data like
`{N}` for the subgroup counts or data level variables such as
`{N_total}` which is the number of items across all groups or
`{N_missing}` for example.

There are a few useful formatting functions that the spec can also use
beyond the usual text processing functions:

- `.sprintf_na` - `sprintf`s a set of numbers replacing the output with
  `getOption("huxtableone.na","\u2014")` if all values are missing, and
  if some values are missing replacing each individual missing value
  with  
  `getOption("huxtableone.missing","<?>")`
- `.sprintf_no_na` - `sprintf`s a set of numbers replacing the output
  with `getOption("huxtableone.na","\u2014")` if any values are missing
- `.maybe` - returns a string if it is present or “” if NA

``` r
custom = list(
    subtype_count = list(
        characteristic = "{level}",
        "Value (N={N}/{N_total})" = "{.sprintf_na('%1.1f%% (%d/%d)',prob.0.5*100,x,n)}"
    ),
    median_iqr = list(
        characteristic = "Median (N)",
        "Value (N={N}/{N_total})" = "{.sprintf_na('%1.3g (%d)',q.0.5,n)}"
    ),
    mean_sd = list(
        characteristic = "Mean (N)", 
        "Value (N={N}/{N_total})" = "{.sprintf_na('%1.3g (%d)',mean,n)}"
    ),
    skipped = list(
        characteristic = "(N)", 
        "Value (N={N}/{N_total})" = "{.sprintf_na('— (%d)',n)}"
    )
)

# printing control the following options control missing values
# produced by the .sprintf_na function:
# getOption("huxtableone.missing","<?>")
# getOption("huxtableone.na","\u2014")


gcd %>% compare_population(
    formula,
    layout = custom
  )
```

[TABLE]

### Footer customisation

- Additional information can be added to the default footer. Handy for
  acronyms:

``` r
 gcd %>% compare_population(
   formula,
   footer_text = c(
      "IQR: Interquartile range; CI: Confidence interval",
      "Additional information could be supplied")
   )
```

[TABLE]

- or we can choose to hide the footer altogether:

``` r
old = options("huxtableone.hide_footer"=TRUE)

# or we can choose to hide the footer altogether
gcd %>% compare_population(formula)
```

|  |  | placebo | rIFN-g |  |
|----|----|----|----|----|
| Variable | Characteristic | Value (N=65) | Value (N=63) | P value |
| Sex | male % \[95% CI\] (n) | 81.5% \[70.4%—89.1%\] (53) | 81.0% \[69.6%—88.8%\] (51) | 1 † |
|  | female % \[95% CI\] (n) | 18.5% \[10.9%—29.6%\] (12) | 19.0% \[11.2%—30.4%\] (12) |  |
| Age | Median \[IQR\] | 14 \[7—24\] | 12 \[7—19.5\] | 0.56 †† |
| Height | Median \[IQR\] | 143 \[115—171\] | 139 \[119—167\] | 0.45 ††† |
| Weight | Median \[IQR\] | 36.1 \[21.6—63.7\] | 34.4 \[20.6—53.7\] | 0.4 ††† |
| Inherit | X-linked % \[95% CI\] (n) | 63.1% \[50.9%—73.8%\] (41) | 71.4% \[59.3%—81.1%\] (45) | 0.35 † |
|  | autosomal % \[95% CI\] (n) | 36.9% \[26.2%—49.1%\] (24) | 28.6% \[18.9%—40.7%\] (18) |  |
| Steroid treatment | false % \[95% CI\] (n) | 96.9% \[89.5%—99.2%\] (63) | 98.4% \[91.5%—99.7%\] (62) | 1 † |
|  | true % \[95% CI\] (n) | 3.1% \[0.8%—10.5%\] (2) | 1.6% \[0.3%—8.5%\] (1) |  |
| Location | US:NIH % \[95% CI\] (n) | 16.9% \[9.7%—27.8%\] (11) | 23.8% \[15.0%—35.6%\] (15) | 0.7 † |
|  | US:other % \[95% CI\] (n) | 49.2% \[37.5%—61.1%\] (32) | 49.2% \[37.3%—61.2%\] (31) |  |
|  | Europe:Amsterdam % \[95% CI\] (n) | 15.4% \[8.6%—26.1%\] (10) | 14.3% \[7.7%—25.0%\] (9) |  |
|  | Europe:other % \[95% CI\] (n) | 18.5% \[10.9%—29.6%\] (12) | 12.7% \[6.6%—23.1%\] (8) |  |

``` r
options(old)
```
