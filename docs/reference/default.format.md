# Default table layout functions

Customisation of output can use one of these entries as a starting
point. A custom layout should look like one of the entries in level 2 of
this nested list, containing 4 named entries, one for each type of table
summary.

## Usage

``` r
default.format
```

## Format

### `default.format`

A names list of lists:

- level one:

  The name of the table layout

- level two:

  The name of the summary type required. one of `subtype_count`,
  `median_iqr`,`mean_sd`,`skipped`

- level three:

  a named list of `column`=`glue specification` pairs. The `column`
  (itself a glue spec) might reference `N_total`, `N_present` or `.unit`
  but typically will be a fixed string- it defines the name of the table
  column to generate. The `glue specification` defines the layout of
  that column, and can use summary statistics as below

- subtype_count:

  can use `level`, `prob.0.5`, `prob.0.025`, `prob.0.975`, `unit`, `n`,
  `N`. `n` is subgroup count, `N` is data count.

- median_iqr:

  can use `q.0.5`, `q.0.25`, ..., `unit`, `n`, `N` - `n` excludes
  missing, `N` does not.

- mean_sd:

  can use `mean`, `sd`, `unit`, `n`, `N` - `n` excludes missing, `N`
  does not.

- skipped:

  can use `n`, `N` - `n` excludes missing, `N` does not.
