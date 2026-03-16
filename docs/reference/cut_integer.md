# Cut and label an integer valued quantity

Deals with some annoying issues classifying integer data sets, such as
ages, into groups. where you want to specify just the change over points
as integers and clearly label the resulting ordered factor.

## Usage

``` r
cut_integer(
  x,
  cut_points,
  glue = "{label}",
  lower_limit = -Inf,
  upper_limit = Inf,
  ...
)
```

## Arguments

- x:

  a vector of integer valued numbers, e.g. ages, counts

- cut_points:

  a vector of integer valued cut points which define the lower,
  inclusive boundary of each group

- glue:

  a glue spec that may be used to generate a label. It can use `{low}`,
  `{high}`, `{next_low}`, or `{label}` as values.

- lower_limit:

  the minimum value we should include (this is inclusive for the bottom
  category) (default -Inf)

- upper_limit:

  the maximum value we should include (this is also inclusive for the
  top category) (default Inf)

- ...:

  not used

## Value

an ordered factor of the integer

## Examples

``` r
cut_integer(stats::rbinom(20,20,0.5), c(5,10,15))
#>  [1] 5‒9   10‒14 10‒14 10‒14 10‒14 <5    5‒9   10‒14 5‒9   10‒14 5‒9   10‒14
#> [13] 10‒14 5‒9   10‒14 10‒14 10‒14 10‒14 10‒14 5‒9  
#> Levels: <5 < 5‒9 < 10‒14 < ≥15
cut_integer(floor(stats::runif(100,-10,10)), cut_points = c(2,3,4,6), lower_limit=0, upper_limit=10)
#>   [1] <NA> <NA> 6‒10 <NA> <NA> 6‒10 <NA> 2    <NA> 6‒10 <NA> 2    3    <NA> 6‒10
#>  [16] <NA> <NA> <NA> 0‒1  <NA> 0‒1  <NA> 2    <NA> <NA> 0‒1  <NA> 6‒10 <NA> <NA>
#>  [31] 6‒10 <NA> <NA> <NA> 6‒10 3    <NA> <NA> <NA> <NA> 6‒10 4‒5  6‒10 <NA> <NA>
#>  [46] <NA> <NA> 3    6‒10 <NA> 0‒1  <NA> <NA> 6‒10 6‒10 <NA> 6‒10 <NA> 6‒10 2   
#>  [61] 6‒10 6‒10 6‒10 4‒5  <NA> 0‒1  6‒10 6‒10 <NA> 6‒10 0‒1  6‒10 3    <NA> <NA>
#>  [76] 4‒5  <NA> 4‒5  <NA> 4‒5  3    0‒1  0‒1  6‒10 <NA> <NA> <NA> 4‒5  <NA> 6‒10
#>  [91] 0‒1  <NA> <NA> 2    <NA> <NA> 4‒5  <NA> 6‒10 6‒10
#> Levels: 0‒1 < 2 < 3 < 4‒5 < 6‒10
cut_integer(1:10, cut_points = c(1,3,9))
#>  [1] 1‒2 1‒2 3‒8 3‒8 3‒8 3‒8 3‒8 3‒8 ≥9  ≥9 
#> Levels: <1 < 1‒2 < 3‒8 < ≥9
```
