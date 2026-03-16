# Convert a `t1_summary` object to a `huxtable`

Convert a `t1_summary` object to a `huxtable`

## Usage

``` r
# S3 method for class 't1_shape'
as_huxtable(
  x,
  ...,
  font_size = getOption("huxtableone.font_size", 8),
  font = .default_font(),
  footer_text = NULL,
  show_binary_value = NULL
)
```

## Arguments

- x:

  the `t1_summary` object as produced by describe_population

- ...:

  not used

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
