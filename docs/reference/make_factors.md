# Convert discrete data to factors

It is simpler for presentation and sometimes more correct for discrete
valued data to be represented as factors. Such discrete valued data
might be logical values, character values, or numeric values with a
limited number of levels (e.g. scores). this function lets you convert
(a subset of) data frame columns into factors using

## Usage

``` r
make_factors(
  df,
  ...,
  .logical = c("yes", "no"),
  .numeric = "{name}={value}",
  .character = NULL
)
```

## Arguments

- df:

  a data frame

- ...:

  either a `tidyselect` specification or a formula with the right hand
  side defining the columns to convert (left hand side is ignored)

- .logical:

  (optional) a length 2 vector defining the levels of TRUE, then FALSE.

- .numeric:

  (optional) if provided it must either be a named list e.g.
  `c(column_name = "{name}:{value}", ..., .default="{value}")` pairs
  which define the way in which numeric columns are converted to factor
  levels. If a single value is given then all numerics are converted in
  the same way (this is the default). If there are some values that you
  are not certain you want to convert setting a limit on the maximum
  number of levels in a generated factor may be a good idea (i.e.
  `options("huxtableone.max_discrete_levels"=16)`) otherwise all values
  are converted

- .character:

  in general character columns are converted into a factor with the
  default levels. To explicitly set levels a named list can be given
  here which
  `c(colname_1 = c("level_1", "level_2", ...), colname_2 = ...)`

## Value

a dataframe with the columns converted to factors

## Examples

``` r
iris %>%
  make_factors(tidyselect::ends_with("Length"), .numeric = "{name}={round(value)}") %>%
  dplyr::glimpse()
#> Rows: 150
#> Columns: 5
#> $ Sepal.Length <ord> Sepal.Length=5, Sepal.Length=5, Sepal.Length=5, Sepal.Len…
#> $ Sepal.Width  <dbl> 3.5, 3.0, 3.2, 3.1, 3.6, 3.9, 3.4, 3.4, 2.9, 3.1, 3.7, 3.…
#> $ Petal.Length <ord> Petal.Length=1, Petal.Length=1, Petal.Length=1, Petal.Len…
#> $ Petal.Width  <dbl> 0.2, 0.2, 0.2, 0.2, 0.2, 0.4, 0.3, 0.2, 0.2, 0.1, 0.2, 0.…
#> $ Species      <fct> setosa, setosa, setosa, setosa, setosa, setosa, setosa, s…

# Convert everything in diamonds to be a factor, rounding all
# the numeric values and converting all the names to upper case
tmp = diamonds %>%
  dplyr::mutate(is_colored = color > "F") %>%
  make_factors(tidyselect::everything(), .numeric="{toupper(name)}={round(value)}")

# as we included `price` which has very many levels one factor is unuseable with 11602 levels:
length(levels(tmp$price))
#> [1] 11602

# we could explicitly exclude it from the `tidyselect` syntax `...` parameter:
diamonds %>% dplyr::mutate(is_colored = color > "F") %>%
  make_factors(-price, .numeric="{toupper(name)}={round(value)}") %>%
  dplyr::glimpse()
#> Rows: 53,940
#> Columns: 11
#> $ carat      <ord> CARAT=0, CARAT=0, CARAT=0, CARAT=0, CARAT=0, CARAT=0, CARAT…
#> $ cut        <ord> Ideal, Premium, Good, Premium, Good, Very Good, Very Good, …
#> $ color      <ord> E, E, E, I, J, J, I, H, E, H, J, J, F, J, E, E, I, J, J, J,…
#> $ clarity    <ord> SI2, SI1, VS1, VS2, SI2, VVS2, VVS1, SI1, VS2, VS1, SI1, VS…
#> $ depth      <ord> DEPTH=62, DEPTH=60, DEPTH=57, DEPTH=62, DEPTH=63, DEPTH=63,…
#> $ table      <ord> TABLE=55, TABLE=61, TABLE=65, TABLE=58, TABLE=58, TABLE=57,…
#> $ price      <int> 326, 326, 327, 334, 335, 336, 336, 337, 337, 338, 339, 340,…
#> $ x          <ord> X=4, X=4, X=4, X=4, X=4, X=4, X=4, X=4, X=4, X=4, X=4, X=4,…
#> $ y          <ord> Y=4, Y=4, Y=4, Y=4, Y=4, Y=4, Y=4, Y=4, Y=4, Y=4, Y=4, Y=4,…
#> $ z          <ord> Z=2, Z=2, Z=2, Z=3, Z=3, Z=2, Z=2, Z=3, Z=2, Z=2, Z=3, Z=2,…
#> $ is_colored <fct> no, no, no, yes, yes, yes, yes, yes, no, yes, yes, yes, no,…

# or alternatively we set a limit on the maximum number of factors, which
# in this example picks up the `depth` and `table` columns as exceeding this
# new limit:

old = options("huxtableone.max_discrete_levels"=16)
diamonds %>% dplyr::mutate(is_colored = color > "F") %>%
  make_factors(tidyselect::everything(), .numeric="{toupper(name)}={round(value)}") %>%
  dplyr::glimpse()
#> Skipping factor conversion for depth because is has 28 levels and the maximum number allowed is set to 16
#> Skipping factor conversion for table because is has 29 levels and the maximum number allowed is set to 16
#> Skipping factor conversion for price because is has 11602 levels and the maximum number allowed is set to 16
#> Rows: 53,940
#> Columns: 11
#> $ carat      <ord> CARAT=0, CARAT=0, CARAT=0, CARAT=0, CARAT=0, CARAT=0, CARAT…
#> $ cut        <ord> Ideal, Premium, Good, Premium, Good, Very Good, Very Good, …
#> $ color      <ord> E, E, E, I, J, J, I, H, E, H, J, J, F, J, E, E, I, J, J, J,…
#> $ clarity    <ord> SI2, SI1, VS1, VS2, SI2, VVS2, VVS1, SI1, VS2, VS1, SI1, VS…
#> $ depth      <dbl> 61.5, 59.8, 56.9, 62.4, 63.3, 62.8, 62.3, 61.9, 65.1, 59.4,…
#> $ table      <dbl> 55, 61, 65, 58, 58, 57, 57, 55, 61, 61, 55, 56, 61, 54, 62,…
#> $ price      <int> 326, 326, 327, 334, 335, 336, 336, 337, 337, 338, 339, 340,…
#> $ x          <ord> X=4, X=4, X=4, X=4, X=4, X=4, X=4, X=4, X=4, X=4, X=4, X=4,…
#> $ y          <ord> Y=4, Y=4, Y=4, Y=4, Y=4, Y=4, Y=4, Y=4, Y=4, Y=4, Y=4, Y=4,…
#> $ z          <ord> Z=2, Z=2, Z=2, Z=3, Z=3, Z=2, Z=2, Z=3, Z=2, Z=2, Z=3, Z=2,…
#> $ is_colored <fct> no, no, no, yes, yes, yes, yes, yes, no, yes, yes, yes, no,…

options(old)

# converting a character vector. Here we specify `.character` as a list giving the
# possible levels of `alpha2`. Values outside of this list are converted to `NA`

set.seed(100)
eg_character = tibble::tibble(
  alpha1 =  sample(letters,50,replace=TRUE),
  alpha2 = sample(LETTERS,50,replace=TRUE)
)

eg_character %>%
  make_factors(tidyselect::everything(), .character = list(alpha2 = LETTERS[3:20]))
#> # A tibble: 50 × 2
#>    alpha1 alpha2
#>    <fct>  <fct> 
#>  1 j      NA    
#>  2 w      K     
#>  3 f      G     
#>  4 p      NA    
#>  5 s      I     
#>  6 y      F     
#>  7 z      T     
#>  8 n      N     
#>  9 l      K     
#> 10 w      M     
#> # ℹ 40 more rows

```
