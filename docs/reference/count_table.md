# Group data count and calculate proportions by column.

Group data count and calculate proportions by column.

## Usage

``` r
count_table(
  df,
  rowGroupVars,
  colGroupVars,
  numExpr = dplyr::n(),
  denomExpr = dplyr::n(),
  totalExpr = dplyr::n(),
  subgroupLevel = length(rowGroupVars),
  glue = list(`Count [%] (N={sprintf("%d",N)})` =
    "{sprintf(\"%d/%d [%1.1f%%]\", x, n, mean*100)}"),
  label_fn = NULL,
  font_size = getOption("huxtableone.font_size", 8),
  font = .default_font()
)
```

## Arguments

- df:

  a dataframe of linelist items

- rowGroupVars:

  the rows of the table. The last one of these is the denominator
  grouping

- colGroupVars:

  the column groupings of the table.

- numExpr:

  defines how the numerator is defined in the context of the column and
  row groups (e.g. dplyr::n())

- denomExpr:

  defines how the numerator is defined in the context of the column and
  row (ungrouped one level)

- totalExpr:

  defines how the column level total is defined

- subgroupLevel:

  defines how the numerator grouping is defined in terms of the row
  groupings

- glue:

  a named list of column value specifications.

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

- font_size:

  (optional) the font size for the table in points

- font:

  (optional) the font family for the table (which will be matched to
  closest on your system)

## Value

a huxtable with the count and proportions of the rows groups

## Examples

``` r
diamonds %>% count_table(dplyr::vars(cut,clarity), dplyr::vars(color), subgroupLevel = 1)
#>  ─────────────────────────────────────────────────────────────────────────────
#>                         D          E          F          G          H         
#>    cut        clarity   Count      Count      Count      Count      Count     
#>                         [%]        [%]        [%]        [%]        [%]       
#>                         (N=6775)   (N=9797)   (N=9542)   (N=11292   (N=8304)  
#>                                                          )                    
#>  ─────────────────────────────────────────────────────────────────────────────
#>    Fair       I1        4/163      9/224      35/312     53/314     52/303    
#>                         [2.5%]     [4.0%]     [11.2%]    [16.9%]    [17.2%]   
#>               SI2       56/163     78/224     89/312     80/314     91/303    
#>                         [34.4%]    [34.8%]    [28.5%]    [25.5%]    [30.0%]   
#>               SI1       58/163     65/224     83/312     69/314     75/303    
#>                         [35.6%]    [29.0%]    [26.6%]    [22.0%]    [24.8%]   
#>               VS2       25/163     42/224     53/312     45/314     41/303    
#>                         [15.3%]    [18.8%]    [17.0%]    [14.3%]    [13.5%]   
#>               VS1       5/163      14/224     33/312     45/314     32/303    
#>                         [3.1%]     [6.2%]     [10.6%]    [14.3%]    [10.6%]   
#>               VVS2      9/163      13/224     10/312     17/314     11/303    
#>                         [5.5%]     [5.8%]     [3.2%]     [5.4%]     [3.6%]    
#>               VVS1      3/163      3/224      5/312      3/314      1/303     
#>                         [1.8%]     [1.3%]     [1.6%]     [1.0%]     [0.3%]    
#>               IF        3/163      —          4/312      2/314      —         
#>                         [1.8%]                [1.3%]     [0.6%]               
#>  ─────────────────────────────────────────────────────────────────────────────
#>    Good       I1        8/662      23/933     19/909     19/871     14/702    
#>                         [1.2%]     [2.5%]     [2.1%]     [2.2%]     [2.0%]    
#>               SI2       223/662    202/933    201/909    163/871    158/702   
#>                         [33.7%]    [21.7%]    [22.1%]    [18.7%]    [22.5%]   
#>               SI1       237/662    355/933    273/909    207/871    235/702   
#>                         [35.8%]    [38.0%]    [30.0%]    [23.8%]    [33.5%]   
#>               VS2       104/662    160/933    184/909    192/871    138/702   
#>                         [15.7%]    [17.1%]    [20.2%]    [22.0%]    [19.7%]   
#>               VS1       43/662     89/933     132/909    152/871    77/702    
#>                         [6.5%]     [9.5%]     [14.5%]    [17.5%]    [11.0%]   
#>               VVS2      25/662     52/933     50/909     75/871     45/702    
#>                         [3.8%]     [5.6%]     [5.5%]     [8.6%]     [6.4%]    
#>               VVS1      13/662     43/933     35/909     41/871     31/702    
#>                         [2.0%]     [4.6%]     [3.9%]     [4.7%]     [4.4%]    
#>               IF        9/662      9/933      15/909     22/871     4/702     
#>                         [1.4%]     [1.0%]     [1.7%]     [2.5%]     [0.6%]    
#>  ─────────────────────────────────────────────────────────────────────────────
#>    Very       I1        5/1513     22/2400    13/2164    16/2299    12/1824   
#>    Good                 [0.3%]     [0.9%]     [0.6%]     [0.7%]     [0.7%]    
#>               SI2       314/1513   445/2400   343/2164   327/2299   343/1824  
#>                         [20.8%]    [18.5%]    [15.9%]    [14.2%]    [18.8%]   
#>               SI1       494/1513   626/2400   559/2164   474/2299   547/1824  
#>                         [32.7%]    [26.1%]    [25.8%]    [20.6%]    [30.0%]   
#>               VS2       309/1513   503/2400   466/2164   479/2299   376/1824  
#>                         [20.4%]    [21.0%]    [21.5%]    [20.8%]    [20.6%]   
#>               VS1       175/1513   293/2400   293/2164   432/2299   257/1824  
#>                         [11.6%]    [12.2%]    [13.5%]    [18.8%]    [14.1%]   
#>               VVS2      141/1513   298/2400   249/2164   302/2299   145/1824  
#>                         [9.3%]     [12.4%]    [11.5%]    [13.1%]    [7.9%]    
#>               VVS1      52/1513    170/2400   174/2164   190/2299   115/1824  
#>                         [3.4%]     [7.1%]     [8.0%]     [8.3%]     [6.3%]    
#>               IF        23/1513    43/2400    67/2164    79/2299    29/1824   
#>                         [1.5%]     [1.8%]     [3.1%]     [3.4%]     [1.6%]    
#>  ─────────────────────────────────────────────────────────────────────────────
#>    Premium    I1        12/1603    30/2337    34/2331    46/2924    46/2360   
#>                         [0.7%]     [1.3%]     [1.5%]     [1.6%]     [1.9%]    
#>               SI2       421/1603   519/2337   523/2331   492/2924   521/2360  
#>                         [26.3%]    [22.2%]    [22.4%]    [16.8%]    [22.1%]   
#>               SI1       556/1603   614/2337   608/2331   566/2924   655/2360  
#>                         [34.7%]    [26.3%]    [26.1%]    [19.4%]    [27.8%]   
#>               VS2       339/1603   629/2337   619/2331   721/2924   532/2360  
#>                         [21.1%]    [26.9%]    [26.6%]    [24.7%]    [22.5%]   
#>               VS1       131/1603   292/2337   290/2331   566/2924   336/2360  
#>                         [8.2%]     [12.5%]    [12.4%]    [19.4%]    [14.2%]   
#>               VVS2      94/1603    121/2337   146/2331   275/2924   118/2360  
#>                         [5.9%]     [5.2%]     [6.3%]     [9.4%]     [5.0%]    
#>               VVS1      40/1603    105/2337   80/2331    171/2924   112/2360  
#>                         [2.5%]     [4.5%]     [3.4%]     [5.8%]     [4.7%]    
#>               IF        10/1603    27/2337    31/2331    87/2924    40/2360   
#>                         [0.6%]     [1.2%]     [1.3%]     [3.0%]     [1.7%]    
#>  ─────────────────────────────────────────────────────────────────────────────
#>    Ideal      I1        13/2834    18/3903    42/3826    16/4884    38/3115   
#>                         [0.5%]     [0.5%]     [1.1%]     [0.3%]     [1.2%]    
#>               SI2       356/2834   469/3903   453/3826   486/4884   450/3115  
#>                         [12.6%]    [12.0%]    [11.8%]    [10.0%]    [14.4%]   
#>               SI1       738/2834   766/3903   608/3826   660/4884   763/3115  
#>                         [26.0%]    [19.6%]    [15.9%]    [13.5%]    [24.5%]   
#>               VS2       920/2834   1136/390   879/3826   910/4884   556/3115  
#>                         [32.5%]    3          [23.0%]    [18.6%]    [17.8%]   
#>                                    [29.1%]                                    
#>               VS1       351/2834   593/3903   616/3826   953/4884   467/3115  
#>                         [12.4%]    [15.2%]    [16.1%]    [19.5%]    [15.0%]   
#>               VVS2      284/2834   507/3903   520/3826   774/4884   289/3115  
#>                         [10.0%]    [13.0%]    [13.6%]    [15.8%]    [9.3%]    
#>               VVS1      144/2834   335/3903   440/3826   594/4884   326/3115  
#>                         [5.1%]     [8.6%]     [11.5%]    [12.2%]    [10.5%]   
#>               IF        28/2834    79/3903    268/3826   491/4884   226/3115  
#>                         [1.0%]     [2.0%]     [7.0%]     [10.1%]    [7.3%]    
#>  ─────────────────────────────────────────────────────────────────────────────
#> 
#> Column names: cut, clarity, 1, 2, 3, 4, 5, 6, 7
#> 
#> 7/9 columns shown.
```
