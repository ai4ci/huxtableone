# Format a p-value

Uses the default formatter set globally in
`options("huxtableone.pvalue_formatter")` in preference the one defined
by `p_format` which is only used if no default is set.

## Usage

``` r
format_pvalue(p.value, p_format = names(.pvalue.defaults))
```

## Arguments

- p.value:

  the p-value to be formatted

- p_format:

  a name of a p-value formatter (one of sampl, nejm, jama, lancet, aim)

## Value

a formatted P-value
