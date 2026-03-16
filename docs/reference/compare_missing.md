# Compares missing data against an intervention in a summary table

The missing data summary is a simple summary of the missingness of
co-variates in a data set with no reference to outcome, but comparing
intervention groups. It reports summary counts for missingness in data
and reports on the significance of that missingness in relation to the
intervention groups, allowing a clear summary of whether data is missing
at random compared to the intervention.

## Usage

``` r
compare_missing(
  df,
  ...,
  label_fn = NULL,
  p_format = names(.pvalue.defaults),
  font_size = getOption("huxtableone.font_size", 8),
  font = .default_font(),
  significance_limit = 0.05,
  missingness_limit = 0.1,
  footer_text = NULL,
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

- p_format:

  the format of the p-values: one of "sampl", "nejm", "jama", "lancet",
  "aim" but any value here is overridden by the
  `option("huxtableone.pvalue_formatter"=function(...))`

- font_size:

  (optional) the font size for the table in points

- font:

  (optional) the font family for the table (which will be matched to
  closest on your system)

- significance_limit:

  the limit at which we reject the hypothesis that the data is missing
  at random.

- missingness_limit:

  the limit at which too much data is missing to include the predictor.

- footer_text:

  any text that needs to be added at the end of the table, setting this
  to FALSE dsables the whole footer (as does
  `options("huxtableone.hide_footer"=TRUE)`).

- raw_output:

  return comparison as tidy dataframe rather than formatted table

## Value

a `huxtable` formatted table.

## Examples

``` r
# this option lets us change the column name for p value from its default
# "P value"
old = options("huxtableone.pvalue_column_name"="p-value")

# missing at random
missing_diamonds %>% dplyr::group_by(is_colored) %>% compare_missing(tidyselect::everything())
#> subtype_count summary for carat
#> subtype_count summary for cut
#> subtype_count summary for color
#> subtype_count summary for clarity
#> subtype_count summary for depth
#> subtype_count summary for table
#> subtype_count summary for price
#> subtype_count summary for x
#> subtype_count summary for y
#> subtype_count summary for z
#> fisher test on carat
#> fisher test on cut
#> fisher test on color
#> fisher test on clarity
#> fisher test on depth
#> fisher test on table
#> fisher test on price
#> fisher test on x
#> fisher test on y
#> fisher test on z
#>           ────────────────────────────────────────────────────────────
#>                        clear              colored                     
#>             variable   missing % (N)      missing % (N)      p-value  
#>           ────────────────────────────────────────────────────────────
#>             carat      10.0%              9.7%               0.26     
#>                        (1651/16572)       (3607/37368)                
#>             z          9.6%               10.2%              0.04     
#>                        (1587/16572)       (3794/37368)                
#>             cut        9.6%               10.2%              0.05     
#>                        (1592/16572)       (3795/37368)                
#>             color      9.7%               10.1%              0.18     
#>                        (1607/16572)       (3764/37368)                
#>             clarity    10.2%              10.0%              0.49     
#>                        (1685/16572)       (3728/37368)                
#>             depth      10.0%              9.9%               0.84     
#>                        (1652/16572)       (3704/37368)                
#>             table      9.9%               9.6%               0.22     
#>                        (1647/16572)       (3586/37368)                
#>             price      9.6%               9.8%               0.35     
#>                        (1587/16572)       (3678/37368)                
#>             x          9.9%               10.0%              0.65     
#>                        (1633/16572)       (3730/37368)                
#>             y          10.1%              9.9%               0.39     
#>                        (1675/16572)       (3687/37368)                
#>           ────────────────────────────────────────────────────────────
#>             More than 10% of data is missing for variables            
#>             clarity.                                                  
#> 
#> Column names: variable, 1, 2, p-value

# nothing missing
iris %>% dplyr::group_by(Species) %>% compare_missing(tidyselect::everything())
#> subtype_count summary for Sepal.Length
#> subtype_count summary for Sepal.Width
#> subtype_count summary for Petal.Length
#> subtype_count summary for Petal.Width
#> fisher test on Sepal.Length
#> fisher test on Sepal.Width
#> fisher test on Petal.Length
#> fisher test on Petal.Width
#>     ───────────────────────────────────────────────────────────────────────
#>                      setosa         versicolor     virginica               
#>       variable       missing %      missing %      missing %      p-value  
#>                      (N)            (N)            (N)                     
#>     ───────────────────────────────────────────────────────────────────────
#>       Sepal.Length   0.0% (0/50)    0.0% (0/50)    0.0% (0/50)    —        
#>       Sepal.Width    0.0% (0/50)    0.0% (0/50)    0.0% (0/50)    —        
#>       Petal.Length   0.0% (0/50)    0.0% (0/50)    0.0% (0/50)    —        
#>       Petal.Width    0.0% (0/50)    0.0% (0/50)    0.0% (0/50)    —        
#>     ───────────────────────────────────────────────────────────────────────
#>                                                                            
#> 
#> Column names: variable, 1, 2, 3, p-value

# MNAR: by design missingness is correlated with grouping
mnar_two_class_1000 %>% dplyr::group_by(grouping) %>% compare_missing(tidyselect::everything())
#> subtype_count summary for uniform_variable
#> subtype_count summary for normal_variable
#> subtype_count summary for binomial_class
#> subtype_count summary for multinom_class
#> subtype_count summary for ordered_class
#> subtype_count summary for discrete
#> subtype_count summary for ignore_me
#> fisher test on uniform_variable
#> fisher test on normal_variable
#> fisher test on binomial_class
#> fisher test on multinom_class
#> fisher test on ordered_class
#> fisher test on discrete
#> fisher test on ignore_me
#>  ─────────────────────────────────────────────────────────────────────────────
#>                       10% missing        20% missing                          
#>    variable           missing % (N)      missing % (N)      p-value           
#>  ─────────────────────────────────────────────────────────────────────────────
#>    uniform_variable   7.3% (73/1000)     18.9% (189/1000)   <0.001            
#>    normal_variable    9.9% (99/1000)     19.3% (193/1000)   <0.001            
#>    binomial_class     9.9% (99/1000)     18.8% (188/1000)   <0.001            
#>    multinom_class     10.1% (101/1000)   17.9% (179/1000)   <0.001            
#>    ordered_class      9.9% (99/1000)     19.1% (191/1000)   <0.001            
#>    discrete           10.6% (106/1000)   19.6% (196/1000)   <0.001            
#>    ignore_me          100.0%             100.0%             1                 
#>                       (1000/1000)        (1000/1000)                          
#>  ─────────────────────────────────────────────────────────────────────────────
#>    More than 10% of data is missing for variables uniform_variable,           
#>    normal_variable, binomial_class, multinom_class, ordered_class,            
#>    discrete, ignore_me.                                                       
#>    Data is missing not at random (compared to grouping) at a                  
#>    p-value<0.007 (0.05 over 7 comparisons) for variables                      
#>    uniform_variable, normal_variable, binomial_class,                         
#>    multinom_class, ordered_class, discrete.                                   
#> 
#> Column names: variable, 1, 2, p-value

options(old)
```
