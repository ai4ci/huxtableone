# Describe the data types and consistence

The population description is a simple summary of the co-variates in a
data set with no reference to outcome, and not comparing intervention
(although it might contain intervention rates.) It will report summary
statistics for continuous and counts for categorical data,

## Usage

``` r
describe_data(
  df,
  ...,
  label_fn = NULL,
  units = extract_units(df),
  layout = "single",
  font_size = getOption("huxtableone.font_size", 8),
  font = .default_font(),
  footer_text = NULL,
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

- layout:

  (optional) various layouts are defined as default. As of this version
  of `huxtableone` they are
  "relaxed","compact","micro","simple","single","missing". The layouts
  can be customised using the options
  `options("huxtableone.format_list"=list(...)")`, and this is described
  in more detail in the vignettes.

- font_size:

  (optional) the font size for the table in points

- font:

  (optional) the font family for the table (which will be matched to
  closest on your system)

- footer_text:

  any text that needs to be added at the end of the table, setting this
  to FALSE dsables the whole footer (as does
  `options("huxtableone.hide_footer"=TRUE)`).

- raw_output:

  return comparison as `t1_signif` dataframe rather than formatted table

## Value

a `huxtable` formatted table.

## Examples

``` r
# Overriding the heuristics is possible:
iris %>% describe_data(tidyselect::everything())
#>  ──────────────────────────────────────────────────────────────────────────────
#>    Variable   Values /   Type       Normally   Unique     Missing    Default   
#>               Units                 distribu   (%)        (%)        summary   
#>                                     ted                                        
#>  ──────────────────────────────────────────────────────────────────────────────
#>    Sepal.Le              continuo   TRUE       23.3%      0.0%       mean_sd   
#>    ngth                  us                                                    
#>    Sepal.Wi              continuo   TRUE       15.3%      0.0%       mean_sd   
#>    dth                   us                                                    
#>    Petal.Le              continuo   FALSE      28.7%      0.0%       median_i  
#>    ngth                  us                                          qr        
#>    Petal.Wi              continuo   FALSE      14.7%      0.0%       median_i  
#>    dth                   us                                          qr        
#>    Species    setosa,    categori   —          0.0%       0.0%       subtype_  
#>               versicol   cal                                         count     
#>               or or                                                            
#>               virginic                                                         
#>               a                                                                
#>  ──────────────────────────────────────────────────────────────────────────────
#>    Normal distributions determined by the Anderson-Darling                     
#>    test (P>0.005)                                                              
#> 
#> Column names: Variable, Values / Units, 1, 2, 3, 4, 5, 6, 7
#> 
#> 7/9 columns shown.

diamonds %>% dplyr::group_by(is_colored) %>% describe_data(tidyselect::everything())
#>  ──────────────────────────────────────────────────────────────────────────────
#>    Variable   Values /   Type       Normally   Unique     Missing    Default   
#>               Units                 distribu   (%)        (%)        summary   
#>                                     ted                                        
#>  ──────────────────────────────────────────────────────────────────────────────
#>    carat                 continuo   FALSE      0.5%       0.0%       median_i  
#>                          us                                          qr        
#>    z                     continuo   FALSE      0.7%       0.0%       median_i  
#>                          us                                          qr        
#>    cut        Fair,      ordered    —          0.0%       0.0%       subtype_  
#>               Good,                                                  count     
#>               Very                                                             
#>               Good,                                                            
#>               Premium                                                          
#>               or Ideal                                                         
#>    color      D, E, F,   ordered    —          0.0%       0.0%       subtype_  
#>               G, H, I                                                count     
#>               or J                                                             
#>    clarity    I1, SI2,   ordered    —          0.0%       0.0%       subtype_  
#>               SI1,                                                   count     
#>               VS2,                                                             
#>               VS1,                                                             
#>               VVS2,                                                            
#>               VVS1 or                                                          
#>               IF                                                               
#>    depth                 continuo   FALSE      0.3%       0.0%       median_i  
#>                          us                                          qr        
#>    table                 continuo   FALSE      0.2%       0.0%       median_i  
#>                          us                                          qr        
#>    price                 continuo   FALSE      21.5%      0.0%       median_i  
#>                          us                                          qr        
#>    x                     continuo   FALSE      1.0%       0.0%       median_i  
#>                          us                                          qr        
#>    y                     continuo   FALSE      1.0%       0.0%       median_i  
#>                          us                                          qr        
#>  ──────────────────────────────────────────────────────────────────────────────
#>    Normal distributions determined by the Anderson-Darling                     
#>    test (P>0.005)                                                              
#> 
#> Column names: Variable, Values / Units, 1, 2, 3, 4, 5, 6, 7
#> 
#> 7/9 columns shown.
```
