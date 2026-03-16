# Describe the population in a summary table

The population description is a simple summary of the co-variates in a
data set with no reference to outcome, and not comparing intervention
(although it might contain intervention rates.) It will report summary
statistics for continuous and counts for categorical data,

## Usage

``` r
describe_population(
  df,
  ...,
  label_fn = NULL,
  units = extract_units(df),
  override_type = list(),
  layout = "single",
  override_percent_dp = list(),
  override_real_dp = list(),
  font_size = getOption("huxtableone.font_size", 8),
  font = .default_font(),
  footer_text = NULL,
  show_binary_value = NULL,
  raw_output = FALSE
)
```

## Arguments

- df:

  a dataframe of individual observations. Grouping, if present, is
  ignored. (n.b. if you wanted to construct multiple summary tables a
  [`dplyr::group_map()`](https://dplyr.tidyverse.org/reference/group_map.html)
  call could be used)

- ...:

  the columns of variables we wish to summarise. This can be given as a
  `tidyselect` specification (see
  [`utils::vignette("syntax", package = "tidyselect")`](https://tidyselect.r-lib.org/articles/syntax.html)),
  identifying the columns. Alternatively it can be given as a formula of
  the nature

  `outcome ~ intervention + covariate_1 + covariate_2 + ...` .

  which may be more convenient if you are going on to do a model fit. If
  the latter format the left hand side is ignored (outcomes are not
  usual in this kind of table).

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

- raw_output:

  return comparison as `t1_signif` dataframe rather than formatted table

## Value

a `huxtable` formatted table.

## Examples

``` r
# the heuristics detect that Petals in the iris data set are not normally
# distributed and hence report median and IQR:
iris %>% describe_population(tidyselect::everything())
#> mean_sd summary for Sepal.Length
#> mean_sd summary for Sepal.Width
#> median_iqr summary for Petal.Length
#> median_iqr summary for Petal.Width
#> subtype_count summary for Species
#>      ──────────────────────────────────────────────────────────────────────
#>        Variable       Characteristic     Value              Count (N=150)  
#>      ──────────────────────────────────────────────────────────────────────
#>        Sepal.Length   Mean ± SD          5.84 ± 0.828       150            
#>      ──────────────────────────────────────────────────────────────────────
#>        Sepal.Width    Mean ± SD          3.06 ± 0.436       150            
#>      ──────────────────────────────────────────────────────────────────────
#>        Petal.Length   Median [IQR]       4.35 [1.6—5.1]     150            
#>      ──────────────────────────────────────────────────────────────────────
#>        Petal.Width    Median [IQR]       1.3 [0.3—1.8]      150            
#>      ──────────────────────────────────────────────────────────────────────
#>        Species        setosa % [95%      33.3%              50/150         
#>                       CI]                [26.3%—41.2%]                     
#>                       versicolor %       33.3%              50/150         
#>                       [95% CI]           [26.3%—41.2%]                     
#>                       virginica % [95%   33.3%              50/150         
#>                       CI]                [26.3%—41.2%]                     
#>      ──────────────────────────────────────────────────────────────────────
#>        Normal distributions determined by the Anderson-Darling             
#>        test (P>0.005)                                                      
#> 
#> Column names: Variable, Characteristic, 1, 2

# Overriding the heuristics is possible:
iris %>% describe_population(
  tidyselect::everything(),
  override_type = c(Petal.Length = "mean_sd", Petal.Width = "mean_sd")
)
#> mean_sd summary for Sepal.Length
#> mean_sd summary for Sepal.Width
#> mean_sd summary for Petal.Length
#> mean_sd summary for Petal.Width
#> subtype_count summary for Species
#>      ──────────────────────────────────────────────────────────────────────
#>        Variable       Characteristic     Value              Count (N=150)  
#>      ──────────────────────────────────────────────────────────────────────
#>        Sepal.Length   Mean ± SD          5.84 ± 0.828       150            
#>      ──────────────────────────────────────────────────────────────────────
#>        Sepal.Width    Mean ± SD          3.06 ± 0.436       150            
#>      ──────────────────────────────────────────────────────────────────────
#>        Petal.Length   Mean ± SD          3.76 ± 1.77        150            
#>      ──────────────────────────────────────────────────────────────────────
#>        Petal.Width    Mean ± SD          1.2 ± 0.762        150            
#>      ──────────────────────────────────────────────────────────────────────
#>        Species        setosa % [95%      33.3%              50/150         
#>                       CI]                [26.3%—41.2%]                     
#>                       versicolor %       33.3%              50/150         
#>                       [95% CI]           [26.3%—41.2%]                     
#>                       virginica % [95%   33.3%              50/150         
#>                       CI]                [26.3%—41.2%]                     
#>      ──────────────────────────────────────────────────────────────────────
#>        Normal distributions determined by the Anderson-Darling             
#>        test (P>0.005)                                                      
#> 
#> Column names: Variable, Characteristic, 1, 2

# The counts sometimes seem redundant if there is no missing information:
diamonds %>% describe_population(tidyselect::everything())
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
#> subtype_count summary for is_colored
#>      ──────────────────────────────────────────────────────────────────────
#>        Variable     Characteristic     Value              Count (N=53940)  
#>      ──────────────────────────────────────────────────────────────────────
#>        carat        Median [IQR]       0.7 [0.4—1.04]     53940            
#>      ──────────────────────────────────────────────────────────────────────
#>        cut          Fair % [95% CI]    3.0% [2.8%—3.1%]   1610/53940       
#>                     Good % [95% CI]    9.1% [8.9%—9.3%]   4906/53940       
#>                     Very Good % [95%   22.4%              12082/53940      
#>                     CI]                [22.0%—22.8%]                       
#>                     Premium % [95%     25.6%              13791/53940      
#>                     CI]                [25.2%—25.9%]                       
#>                     Ideal % [95% CI]   40.0%              21551/53940      
#>                                        [39.5%—40.4%]                       
#>      ──────────────────────────────────────────────────────────────────────
#>        color        D % [95% CI]       12.6%              6775/53940       
#>                                        [12.3%—12.8%]                       
#>                     E % [95% CI]       18.2%              9797/53940       
#>                                        [17.8%—18.5%]                       
#>                     F % [95% CI]       17.7%              9542/53940       
#>                                        [17.4%—18.0%]                       
#>                     G % [95% CI]       20.9%              11292/53940      
#>                                        [20.6%—21.3%]                       
#>                     H % [95% CI]       15.4%              8304/53940       
#>                                        [15.1%—15.7%]                       
#>                     I % [95% CI]       10.1%              5422/53940       
#>                                        [9.8%—10.3%]                        
#>                     J % [95% CI]       5.2% [5.0%—5.4%]   2808/53940       
#>      ──────────────────────────────────────────────────────────────────────
#>        clarity      I1 % [95% CI]      1.4% [1.3%—1.5%]   741/53940        
#>                     SI2 % [95% CI]     17.0%              9194/53940       
#>                                        [16.7%—17.4%]                       
#>                     SI1 % [95% CI]     24.2%              13065/53940      
#>                                        [23.9%—24.6%]                       
#>                     VS2 % [95% CI]     22.7%              12258/53940      
#>                                        [22.4%—23.1%]                       
#>                     VS1 % [95% CI]     15.1%              8171/53940       
#>                                        [14.8%—15.5%]                       
#>                     VVS2 % [95% CI]    9.4% [9.1%—9.6%]   5066/53940       
#>                     VVS1 % [95% CI]    6.8% [6.6%—7.0%]   3655/53940       
#>                     IF % [95% CI]      3.3% [3.2%—3.5%]   1790/53940       
#>      ──────────────────────────────────────────────────────────────────────
#>        depth        Median [IQR]       61.8 [61—62.5]     53940            
#>      ──────────────────────────────────────────────────────────────────────
#>        table        Median [IQR]       57 [56—59]         53940            
#>      ──────────────────────────────────────────────────────────────────────
#>        price        Median [IQR]       2.4e+03            53940            
#>                                        [950—5.32e+03]                      
#>      ──────────────────────────────────────────────────────────────────────
#>        x            Median [IQR]       5.7 [4.71—6.54]    53940            
#>      ──────────────────────────────────────────────────────────────────────
#>        y            Median [IQR]       5.71 [4.72—6.54]   53940            
#>      ──────────────────────────────────────────────────────────────────────
#>        z            Median [IQR]       3.53 [2.91—4.04]   53940            
#>      ──────────────────────────────────────────────────────────────────────
#>        is_colored   clear % [95% CI]   30.7%              16572/53940      
#>                                        [30.3%—31.1%]                       
#>                     colored % [95%     69.3%              37368/53940      
#>                     CI]                [68.9%—69.7%]                       
#>      ──────────────────────────────────────────────────────────────────────
#>        Normal distributions determined by the Anderson-Darling             
#>        test (P>0.005)                                                      
#> 
#> Column names: Variable, Characteristic, 1, 2

# however in a data set with missing values the denominators are important:
missing_diamonds %>% describe_population(tidyselect::everything())
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
#> subtype_count summary for is_colored
#>      ──────────────────────────────────────────────────────────────────────
#>        Variable     Characteristic     Value              Count (N=53940)  
#>      ──────────────────────────────────────────────────────────────────────
#>        carat        Median [IQR]       0.7 [0.4—1.04]     48682            
#>      ──────────────────────────────────────────────────────────────────────
#>        cut          Fair % [95% CI]    3.0% [2.8%—3.2%]   1454/48553       
#>                     Good % [95% CI]    9.2% [8.9%—9.5%]   4462/48553       
#>                     Very Good % [95%   22.3%              10816/48553      
#>                     CI]                [21.9%—22.6%]                       
#>                     Premium % [95%     25.7%              12460/48553      
#>                     CI]                [25.3%—26.1%]                       
#>                     Ideal % [95% CI]   39.9%              19361/48553      
#>                                        [39.4%—40.3%]                       
#>      ──────────────────────────────────────────────────────────────────────
#>        color        D % [95% CI]       12.5%              6079/48569       
#>                                        [12.2%—12.8%]                       
#>                     E % [95% CI]       18.3%              8886/48569       
#>                                        [18.0%—18.6%]                       
#>                     F % [95% CI]       17.7%              8613/48569       
#>                                        [17.4%—18.1%]                       
#>                     G % [95% CI]       20.9%              10137/48569      
#>                                        [20.5%—21.2%]                       
#>                     H % [95% CI]       15.4%              7466/48569       
#>                                        [15.1%—15.7%]                       
#>                     I % [95% CI]       10.0%              4876/48569       
#>                                        [9.8%—10.3%]                        
#>                     J % [95% CI]       5.2% [5.0%—5.4%]   2512/48569       
#>      ──────────────────────────────────────────────────────────────────────
#>        clarity      I1 % [95% CI]      1.4% [1.3%—1.5%]   664/48527        
#>                     SI2 % [95% CI]     17.0%              8265/48527       
#>                                        [16.7%—17.4%]                       
#>                     SI1 % [95% CI]     24.2%              11756/48527      
#>                                        [23.8%—24.6%]                       
#>                     VS2 % [95% CI]     22.7%              11020/48527      
#>                                        [22.3%—23.1%]                       
#>                     VS1 % [95% CI]     15.2%              7355/48527       
#>                                        [14.8%—15.5%]                       
#>                     VVS2 % [95% CI]    9.4% [9.2%—9.7%]   4570/48527       
#>                     VVS1 % [95% CI]    6.8% [6.6%—7.0%]   3298/48527       
#>                     IF % [95% CI]      3.3% [3.1%—3.5%]   1599/48527       
#>      ──────────────────────────────────────────────────────────────────────
#>        depth        Median [IQR]       61.8 [61—62.5]     48584            
#>      ──────────────────────────────────────────────────────────────────────
#>        table        Median [IQR]       57 [56—59]         48707            
#>      ──────────────────────────────────────────────────────────────────────
#>        price        Median [IQR]       2.41e+03           48675            
#>                                        [952—5.33e+03]                      
#>      ──────────────────────────────────────────────────────────────────────
#>        x            Median [IQR]       5.69 [4.72—6.54]   48577            
#>      ──────────────────────────────────────────────────────────────────────
#>        y            Median [IQR]       5.71 [4.72—6.54]   48578            
#>      ──────────────────────────────────────────────────────────────────────
#>        z            Median [IQR]       3.52 [2.91—4.03]   48559            
#>      ──────────────────────────────────────────────────────────────────────
#>        is_colored   clear % [95% CI]   30.7%              16572/53940      
#>                                        [30.3%—31.1%]                       
#>                     colored % [95%     69.3%              37368/53940      
#>                     CI]                [68.9%—69.7%]                       
#>      ──────────────────────────────────────────────────────────────────────
#>        Normal distributions determined by the Anderson-Darling             
#>        test (P>0.005)                                                      
#> 
#> Column names: Variable, Characteristic, 1, 2

# for factor levels we can make the missing values more explicit
missing_diamonds %>% explicit_na() %>%
  describe_population(tidyselect::everything())
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
#> subtype_count summary for is_colored
#>      ──────────────────────────────────────────────────────────────────────
#>        Variable     Characteristic     Value              Count (N=53940)  
#>      ──────────────────────────────────────────────────────────────────────
#>        carat        Median [IQR]       0.7 [0.4—1.04]     48682            
#>      ──────────────────────────────────────────────────────────────────────
#>        cut          Fair % [95% CI]    2.7% [2.6%—2.8%]   1454/53940       
#>                     Good % [95% CI]    8.3% [8.0%—8.5%]   4462/53940       
#>                     Very Good % [95%   20.1%              10816/53940      
#>                     CI]                [19.7%—20.4%]                       
#>                     Premium % [95%     23.1%              12460/53940      
#>                     CI]                [22.7%—23.5%]                       
#>                     Ideal % [95% CI]   35.9%              19361/53940      
#>                                        [35.5%—36.3%]                       
#>                     <missing> % [95%   10.0%              5387/53940       
#>                     CI]                [9.7%—10.2%]                        
#>      ──────────────────────────────────────────────────────────────────────
#>        color        D % [95% CI]       11.3%              6079/53940       
#>                                        [11.0%—11.5%]                       
#>                     E % [95% CI]       16.5%              8886/53940       
#>                                        [16.2%—16.8%]                       
#>                     F % [95% CI]       16.0%              8613/53940       
#>                                        [15.7%—16.3%]                       
#>                     G % [95% CI]       18.8%              10137/53940      
#>                                        [18.5%—19.1%]                       
#>                     H % [95% CI]       13.8%              7466/53940       
#>                                        [13.6%—14.1%]                       
#>                     I % [95% CI]       9.0% [8.8%—9.3%]   4876/53940       
#>                     J % [95% CI]       4.7% [4.5%—4.8%]   2512/53940       
#>                     <missing> % [95%   10.0%              5371/53940       
#>                     CI]                [9.7%—10.2%]                        
#>      ──────────────────────────────────────────────────────────────────────
#>        clarity      I1 % [95% CI]      1.2% [1.1%—1.3%]   664/53940        
#>                     SI2 % [95% CI]     15.3%              8265/53940       
#>                                        [15.0%—15.6%]                       
#>                     SI1 % [95% CI]     21.8%              11756/53940      
#>                                        [21.4%—22.1%]                       
#>                     VS2 % [95% CI]     20.4%              11020/53940      
#>                                        [20.1%—20.8%]                       
#>                     VS1 % [95% CI]     13.6%              7355/53940       
#>                                        [13.3%—13.9%]                       
#>                     VVS2 % [95% CI]    8.5% [8.2%—8.7%]   4570/53940       
#>                     VVS1 % [95% CI]    6.1% [5.9%—6.3%]   3298/53940       
#>                     IF % [95% CI]      3.0% [2.8%—3.1%]   1599/53940       
#>                     <missing> % [95%   10.0%              5413/53940       
#>                     CI]                [9.8%—10.3%]                        
#>      ──────────────────────────────────────────────────────────────────────
#>        depth        Median [IQR]       61.8 [61—62.5]     48584            
#>      ──────────────────────────────────────────────────────────────────────
#>        table        Median [IQR]       57 [56—59]         48707            
#>      ──────────────────────────────────────────────────────────────────────
#>        price        Median [IQR]       2.41e+03           48675            
#>                                        [952—5.33e+03]                      
#>      ──────────────────────────────────────────────────────────────────────
#>        x            Median [IQR]       5.69 [4.72—6.54]   48577            
#>      ──────────────────────────────────────────────────────────────────────
#>        y            Median [IQR]       5.71 [4.72—6.54]   48578            
#>      ──────────────────────────────────────────────────────────────────────
#>        z            Median [IQR]       3.52 [2.91—4.03]   48559            
#>      ──────────────────────────────────────────────────────────────────────
#>        is_colored   clear % [95% CI]   30.7%              16572/53940      
#>                                        [30.3%—31.1%]                       
#>                     colored % [95%     69.3%              37368/53940      
#>                     CI]                [68.9%—69.7%]                       
#>      ──────────────────────────────────────────────────────────────────────
#>        Normal distributions determined by the Anderson-Darling             
#>        test (P>0.005)                                                      
#> 
#> Column names: Variable, Characteristic, 1, 2

# in the output above the price variable is not # presented the way we would
# like so here we override the number of decimal places shown for the price
# variable while we are at it we will use a mid point for the decimal point,
# and make the variable labels sentence case.

old = options("huxtableone.dp"="\u00B7")
missing_diamonds %>%
  explicit_na() %>%
  describe_population(
    tidyselect::everything(),
    label_fn=stringr::str_to_sentence,
    override_real_dp=list(price=6)
  )
#> median_iqr summary for Carat
#> subtype_count summary for Cut
#> subtype_count summary for Color
#> subtype_count summary for Clarity
#> median_iqr summary for Depth
#> median_iqr summary for Table
#> median_iqr summary for Price
#> median_iqr summary for X
#> median_iqr summary for Y
#> median_iqr summary for Z
#> subtype_count summary for Is_colored
#>      ──────────────────────────────────────────────────────────────────────
#>        Variable     Characteristic     Value              Count (N=53940)  
#>      ──────────────────────────────────────────────────────────────────────
#>        Carat        Median [IQR]       0·7 [0·4—1·04]     48682            
#>      ──────────────────────────────────────────────────────────────────────
#>        Cut          Fair % [95% CI]    2·7% [2·6%—2·8%]   1454/53940       
#>                     Good % [95% CI]    8·3% [8·0%—8·5%]   4462/53940       
#>                     Very Good % [95%   20·1%              10816/53940      
#>                     CI]                [19·7%—20·4%]                       
#>                     Premium % [95%     23·1%              12460/53940      
#>                     CI]                [22·7%—23·5%]                       
#>                     Ideal % [95% CI]   35·9%              19361/53940      
#>                                        [35·5%—36·3%]                       
#>                     <missing> % [95%   10·0%              5387/53940       
#>                     CI]                [9·7%—10·2%]                        
#>      ──────────────────────────────────────────────────────────────────────
#>        Color        D % [95% CI]       11·3%              6079/53940       
#>                                        [11·0%—11·5%]                       
#>                     E % [95% CI]       16·5%              8886/53940       
#>                                        [16·2%—16·8%]                       
#>                     F % [95% CI]       16·0%              8613/53940       
#>                                        [15·7%—16·3%]                       
#>                     G % [95% CI]       18·8%              10137/53940      
#>                                        [18·5%—19·1%]                       
#>                     H % [95% CI]       13·8%              7466/53940       
#>                                        [13·6%—14·1%]                       
#>                     I % [95% CI]       9·0% [8·8%—9·3%]   4876/53940       
#>                     J % [95% CI]       4·7% [4·5%—4·8%]   2512/53940       
#>                     <missing> % [95%   10·0%              5371/53940       
#>                     CI]                [9·7%—10·2%]                        
#>      ──────────────────────────────────────────────────────────────────────
#>        Clarity      I1 % [95% CI]      1·2% [1·1%—1·3%]   664/53940        
#>                     SI2 % [95% CI]     15·3%              8265/53940       
#>                                        [15·0%—15·6%]                       
#>                     SI1 % [95% CI]     21·8%              11756/53940      
#>                                        [21·4%—22·1%]                       
#>                     VS2 % [95% CI]     20·4%              11020/53940      
#>                                        [20·1%—20·8%]                       
#>                     VS1 % [95% CI]     13·6%              7355/53940       
#>                                        [13·3%—13·9%]                       
#>                     VVS2 % [95% CI]    8·5% [8·2%—8·7%]   4570/53940       
#>                     VVS1 % [95% CI]    6·1% [5·9%—6·3%]   3298/53940       
#>                     IF % [95% CI]      3·0% [2·8%—3·1%]   1599/53940       
#>                     <missing> % [95%   10·0%              5413/53940       
#>                     CI]                [9·8%—10·3%]                        
#>      ──────────────────────────────────────────────────────────────────────
#>        Depth        Median [IQR]       61·8 [61—62·5]     48584            
#>      ──────────────────────────────────────────────────────────────────────
#>        Table        Median [IQR]       57 [56—59]         48707            
#>      ──────────────────────────────────────────────────────────────────────
#>        Price        Median [IQR]       2407 [952—5330]    48675            
#>      ──────────────────────────────────────────────────────────────────────
#>        X            Median [IQR]       5·69 [4·72—6·54]   48577            
#>      ──────────────────────────────────────────────────────────────────────
#>        Y            Median [IQR]       5·71 [4·72—6·54]   48578            
#>      ──────────────────────────────────────────────────────────────────────
#>        Z            Median [IQR]       3·52 [2·91—4·03]   48559            
#>      ──────────────────────────────────────────────────────────────────────
#>        Is_colored   clear % [95% CI]   30·7%              16572/53940      
#>                                        [30·3%—31·1%]                       
#>                     colored % [95%     69·3%              37368/53940      
#>                     CI]                [68·9%—69·7%]                       
#>      ──────────────────────────────────────────────────────────────────────
#>        Normal distributions determined by the Anderson-Darling             
#>        test (P>0.005)                                                      
#> 
#> Column names: Variable, Characteristic, 1, 2
options(old)
```
