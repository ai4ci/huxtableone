# Extract labels from a dataframe column attributes

Retrieve column labels are embedded as an attribute of each column.

## Usage

``` r
label_extractor(df, ..., attribute = "label")
```

## Arguments

- df:

  a dataframe containing some labels

- ...:

  additional string manipulation functions to apply e.g. `tolower`

- attribute:

  the name of the label containing attribute (defaults to `"label"`)

## Value

a labelling function. This is specific to the dataframe provided in `df`

## Examples

``` r
iris = set_labels(iris, c(
    "Sepal Length", "Sepal Width",
    "Petal Length", "Petal Width",  "Species"
 ))
fn = label_extractor(iris,tolower)
fn(colnames(iris))
#>   Sepal.Length    Sepal.Width   Petal.Length    Petal.Width        Species 
#> "sepal length"  "sepal width" "petal length"  "petal width"      "species" 
```
