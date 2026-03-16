# Compares the population against an intervention in a summary table

The population comparison is a summary of the co-variates in a data set
with no reference to outcome, but comparing intervention groups. It will
report summary statistics for continuous and counts for categorical
data, for each of the intervention groups, and reports on the
significance of the association in relation to the intervention groups.
It gives a clear summary of whether data is correlated to intervention.

## Usage

``` r
compare_population(
  df,
  ...,
  label_fn = NULL,
  units = extract_units(df),
  override_type = list(),
  override_method = list(),
  layout = "compact",
  override_percent_dp = list(),
  override_real_dp = list(),
  p_format = names(.pvalue.defaults),
  font_size = getOption("huxtableone.font_size", 8),
  font = .default_font(),
  footer_text = NULL,
  show_binary_value = NULL,
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

- override_method:

  if you want to override the comparison method for a particular
  variable the options are "chi-sq trend","fisher","t-test","2-sided
  wilcoxon","2-sided ks","anova","kruskal-wallis","no comparison" and
  you specify this on a column by column bases with a named list (e.g
  `c("Petal.Width"="t-test")`)

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

- p_format:

  the format of the p-values: one of "sampl", "nejm", "jama", "lancet",
  "aim" but any value here is overridden by the
  `option("huxtableone.pvalue_formatter"=function(...))`

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
iris %>% dplyr::group_by(Species) %>% compare_population(tidyselect::everything())
#> mean_sd summary for Sepal.Length
#> mean_sd summary for Sepal.Width
#> median_iqr summary for Petal.Length
#> median_iqr summary for Petal.Width
#> anova test on Sepal.Length
#> anova test on Sepal.Width
#> kruskal-wallis test on Petal.Length
#> kruskal-wallis test on Petal.Width
#> ───────────────────────────────────────────────────────────────────────────────
#>                             setosa       versicolor   virginica                
#>   Variable     Characteri   Value        Value        Value        P value     
#>                stic         (N=50)       (N=50)       (N=50)                   
#> ───────────────────────────────────────────────────────────────────────────────
#>   Sepal.Leng   Mean ± SD    5.01 ±       5.94 ±       6.59 ±       <0.001 †    
#>   th                        0.352        0.516        0.636                    
#>   Sepal.Widt   Mean ± SD    3.43 ±       2.77 ±       2.97 ±       <0.001 †    
#>   h                         0.379        0.314        0.322                    
#>   Petal.Leng   Median       1.5          4.35         5.55         <0.001 ††   
#>   th           [IQR]        [1.4—1.58]   [4—4.6]      [5.1—5.88]               
#>   Petal.Widt   Median       0.2          1.3          2            <0.001 ††   
#>   h            [IQR]        [0.2—0.3]    [1.2—1.5]    [1.8—2.3]                
#> ───────────────────────────────────────────────────────────────────────────────
#>   †, Analysis of variance (linear model) (continuous); ††,                     
#>   Kruskal-Wallis rank sum test (continuous)                                    
#>   Normal distributions determined by the Anderson-Darling test                 
#>   (P>0.005)                                                                    
#>   An adjusted P value of 0.0125 may be considered significant.                 
#> 
#> Column names: Variable, Characteristic, 1, 2, 3, P value

# Missing data
old = options("huxtableone.show_pvalue_method"=FALSE)
missing_diamonds %>%
  dplyr::group_by(is_colored) %>%
  compare_population(-color, layout="relaxed")
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
#> Significance testing skipped due to missing values: carat
#> fisher test on cut
#> Significance testing skipped due to missing values: cut
#> fisher test on clarity
#> Significance testing skipped due to missing values: clarity
#> 2-sided wilcoxon test on depth
#> Significance testing skipped due to missing values: depth
#> 2-sided wilcoxon test on table
#> Significance testing skipped due to missing values: table
#> 2-sided wilcoxon test on price
#> Significance testing skipped due to missing values: price
#> 2-sided wilcoxon test on x
#> Significance testing skipped due to missing values: x
#> 2-sided wilcoxon test on y
#> Significance testing skipped due to missing values: y
#> 2-sided wilcoxon test on z
#> Significance testing skipped due to missing values: z
#>  ──────────────────────────────────────────────────────────────────────────────
#>                          clear                 colored                         
#>    Variable   Characte   Value      Count      Value      Count      P value   
#>               ristic                (N=16572              (N=37368             
#>                                     )                     )                    
#>  ──────────────────────────────────────────────────────────────────────────────
#>    carat      Median     0.53       14921      0.74       33761      —         
#>               [IQR]      [0.36—0.              [0.41—1.                        
#>                          9]                    13]                             
#>  ──────────────────────────────────────────────────────────────────────────────
#>    cut        Fair %     2.3%       349/1498   3.3%       1105/335   —         
#>               [95% CI]   [2.1%—2.   0          [3.1%—3.   73                   
#>                          6%]                   5%]                             
#>               Good %     9.7%       1449/149   9.0%       3013/335             
#>               [95% CI]   [9.2%—10   80         [8.7%—9.   73                   
#>                          .2%]                  3%]                             
#>               Very       23.7%      3550/149   21.6%      7266/335             
#>               Good %     [23.0%—2   80         [21.2%—2   73                   
#>               [95% CI]   4.4%]                 2.1%]                           
#>               Premium    23.7%      3556/149   26.5%      8904/335             
#>               % [95%     [23.1%—2   80         [26.1%—2   73                   
#>               CI]        4.4%]                 7.0%]                           
#>               Ideal %    40.6%      6076/149   39.6%      13285/33             
#>               [95% CI]   [39.8%—4   80         [39.0%—4   573                  
#>                          1.3%]                 0.1%]                           
#>  ──────────────────────────────────────────────────────────────────────────────
#>    clarity    I1 %       0.9%       129/1488   1.6%       535/3364   —         
#>               [95% CI]   [0.7%—1.   7          [1.5%—1.   0                    
#>                          0%]                   7%]                             
#>               SI2 %      18.5%      2755/148   16.4%      5510/336             
#>               [95% CI]   [17.9%—1   87         [16.0%—1   40                   
#>                          9.1%]                 6.8%]                           
#>               SI1 %      27.3%      4064/148   22.9%      7692/336             
#>               [95% CI]   [26.6%—2   87         [22.4%—2   40                   
#>                          8.0%]                 3.3%]                           
#>               VS2 %      25.1%      3733/148   21.7%      7287/336             
#>               [95% CI]   [24.4%—2   87         [21.2%—2   40                   
#>                          5.8%]                 2.1%]                           
#>               VS1 %      12.1%      1798/148   16.5%      5557/336             
#>               [95% CI]   [11.6%—1   87         [16.1%—1   40                   
#>                          2.6%]                 6.9%]                           
#>               VVS2 %     9.3%       1390/148   9.5%       3180/336             
#>               [95% CI]   [8.9%—9.   87         [9.1%—9.   40                   
#>                          8%]                   8%]                             
#>               VVS1 %     5.4%       809/1488   7.4%       2489/336             
#>               [95% CI]   [5.1%—5.   7          [7.1%—7.   40                   
#>                          8%]                   7%]                             
#>               IF %       1.4%       209/1488   4.1%       1390/336             
#>               [95% CI]   [1.2%—1.   7          [3.9%—4.   40                   
#>                          6%]                   3%]                             
#>  ──────────────────────────────────────────────────────────────────────────────
#>    depth      Median     61.8       14920      61.9       33664      —         
#>               [IQR]      [61—62.5              [61.1—62                        
#>                          ]                     .5]                             
#>  ──────────────────────────────────────────────────────────────────────────────
#>    table      Median     57         14925      57         33782      —         
#>               [IQR]      [56—59]               [56—59]                         
#>  ──────────────────────────────────────────────────────────────────────────────
#>    price      Median     1.78e+03   14985      2.79e+03   33690      —         
#>               [IQR]      [893—4.1              [992—6.0                        
#>                          2e+03]                1e+03]                          
#>  ──────────────────────────────────────────────────────────────────────────────
#>    x          Median     5.24       14939      5.83       33638      —         
#>               [IQR]      [4.58—6.              [4.77—6.                        
#>                          18]                   7]                              
#>  ──────────────────────────────────────────────────────────────────────────────
#>    y          Median     5.24       14897      5.85       33681      —         
#>               [IQR]      [4.58—6.              [4.78—6.                        
#>                          18]                   69]                             
#>  ──────────────────────────────────────────────────────────────────────────────
#>    z          Median     3.22       14985      3.6        33574      —         
#>               [IQR]      [2.81—3.              [2.95—4.                        
#>                          83]                   13]                             
#>  ──────────────────────────────────────────────────────────────────────────────
#>    Significance determined using Not calculated due to                         
#>    missing values (continuous variables) or Not calculated                     
#>    due to missing values (ordered variables)                                   
#>    Normal distributions determined by the Anderson-Darling                     
#>    test (P>0.005)                                                              
#>    An adjusted P value of 0.00556 may be considered                            
#>    significant.                                                                
#> 
#> Column names: Variable, Characteristic, 1, 2, 3, 4, P value

tmp = missing_diamonds %>% explicit_na() %>% dplyr::group_by(is_colored)
tmp %>% compare_population(-color,
    footer_text = c(
      "IQR: Interquartile range; CI: Confidence interval",
      "Line two")
    )
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
#> Significance testing skipped due to missing values: carat
#> fisher test on cut
#> fisher test on clarity
#> 2-sided wilcoxon test on depth
#> Significance testing skipped due to missing values: depth
#> 2-sided wilcoxon test on table
#> Significance testing skipped due to missing values: table
#> 2-sided wilcoxon test on price
#> Significance testing skipped due to missing values: price
#> 2-sided wilcoxon test on x
#> Significance testing skipped due to missing values: x
#> 2-sided wilcoxon test on y
#> Significance testing skipped due to missing values: y
#> 2-sided wilcoxon test on z
#> Significance testing skipped due to missing values: z
#>   ────────────────────────────────────────────────────────────────────────────
#>                                   clear          colored                      
#>     Variable       Characterist   Value          Value          P value       
#>                    ic             (N=16572)      (N=37368)                    
#>   ────────────────────────────────────────────────────────────────────────────
#>     carat          Median [IQR]   0.53           0.74           —             
#>                                   [0.36—0.9]     [0.41—1.13]                  
#>   ────────────────────────────────────────────────────────────────────────────
#>     cut            Fair % [95%    2.1%           3.0%           <0.001        
#>                    CI] (n)        [1.9%—2.3%]    [2.8%—3.1%]                  
#>                                   (349)          (1105)                       
#>                    Good % [95%    8.7%           8.1%                         
#>                    CI] (n)        [8.3%—9.2%]    [7.8%—8.3%]                  
#>                                   (1449)         (3013)                       
#>                    Very Good %    21.4%          19.4%                        
#>                    [95% CI] (n)   [20.8%—22.1%   [19.0%—19.8%                 
#>                                   ] (3550)       ] (7266)                     
#>                    Premium %      21.5%          23.8%                        
#>                    [95% CI] (n)   [20.8%—22.1%   [23.4%—24.3%                 
#>                                   ] (3556)       ] (8904)                     
#>                    Ideal % [95%   36.7%          35.6%                        
#>                    CI] (n)        [35.9%—37.4%   [35.1%—36.0%                 
#>                                   ] (6076)       ] (13285)                    
#>                    <missing> %    9.6%           10.2%                        
#>                    [95% CI] (n)   [9.2%—10.1%]   [9.9%—10.5%]                 
#>                                   (1592)         (3795)                       
#>   ────────────────────────────────────────────────────────────────────────────
#>     clarity        I1 % [95%      0.8%           1.4%           <0.001        
#>                    CI] (n)        [0.7%—0.9%]    [1.3%—1.6%]                  
#>                                   (129)          (535)                        
#>                    SI2 % [95%     16.6%          14.7%                        
#>                    CI] (n)        [16.1%—17.2%   [14.4%—15.1%                 
#>                                   ] (2755)       ] (5510)                     
#>                    SI1 % [95%     24.5%          20.6%                        
#>                    CI] (n)        [23.9%—25.2%   [20.2%—21.0%                 
#>                                   ] (4064)       ] (7692)                     
#>                    VS2 % [95%     22.5%          19.5%                        
#>                    CI] (n)        [21.9%—23.2%   [19.1%—19.9%                 
#>                                   ] (3733)       ] (7287)                     
#>                    VS1 % [95%     10.8%          14.9%                        
#>                    CI] (n)        [10.4%—11.3%   [14.5%—15.2%                 
#>                                   ] (1798)       ] (5557)                     
#>                    VVS2 % [95%    8.4%           8.5%                         
#>                    CI] (n)        [8.0%—8.8%]    [8.2%—8.8%]                  
#>                                   (1390)         (3180)                       
#>                    VVS1 % [95%    4.9%           6.7%                         
#>                    CI] (n)        [4.6%—5.2%]    [6.4%—6.9%]                  
#>                                   (809)          (2489)                       
#>                    IF % [95%      1.3%           3.7%                         
#>                    CI] (n)        [1.1%—1.4%]    [3.5%—3.9%]                  
#>                                   (209)          (1390)                       
#>                    <missing> %    10.2%          10.0%                        
#>                    [95% CI] (n)   [9.7%—10.6%]   [9.7%—10.3%]                 
#>                                   (1685)         (3728)                       
#>   ────────────────────────────────────────────────────────────────────────────
#>     depth          Median [IQR]   61.8           61.9           —             
#>                                   [61—62.5]      [61.1—62.5]                  
#>   ────────────────────────────────────────────────────────────────────────────
#>     table          Median [IQR]   57 [56—59]     57 [56—59]     —             
#>   ────────────────────────────────────────────────────────────────────────────
#>     price          Median [IQR]   1.78e+03       2.79e+03       —             
#>                                   [893—4.12e+0   [992—6.01e+0                 
#>                                   3]             3]                           
#>   ────────────────────────────────────────────────────────────────────────────
#>     x              Median [IQR]   5.24           5.83           —             
#>                                   [4.58—6.18]    [4.77—6.7]                   
#>   ────────────────────────────────────────────────────────────────────────────
#>     y              Median [IQR]   5.24           5.85           —             
#>                                   [4.58—6.18]    [4.78—6.69]                  
#>   ────────────────────────────────────────────────────────────────────────────
#>     z              Median [IQR]   3.22           3.6            —             
#>                                   [2.81—3.83]    [2.95—4.13]                  
#>   ────────────────────────────────────────────────────────────────────────────
#>     Significance determined using Fisher's exact test                         
#>     (categorical variables) or Not calculated due to missing                  
#>     values (continuous variables)                                             
#>     Normal distributions determined by the Anderson-Darling test              
#>     (P>0.005)                                                                 
#>     IQR: Interquartile range; CI: Confidence interval                         
#>     Line two                                                                  
#>     An adjusted P value of 0.00556 may be considered                          
#>     significant.                                                              
#> 
#> Column names: Variable, Characteristic, 1, 2, P value

options(old)
```
