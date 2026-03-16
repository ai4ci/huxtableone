# Set a label attribute

Set a label attribute

## Usage

``` r
set_labels(df, labels, attribute = "label")
```

## Arguments

- df:

  a dataframe

- labels:

  a vector of labels, one for each column

- attribute:

  the name of the label attribute (defaults to `"label"`)

## Value

the same dataframe with each column labelled

## Examples

``` r
iris = set_labels(iris,
  c("Sepal Length", "Sepal Width",
    "Petal Length", "Petal Width",  "Species"
   ))
fn = label_extractor(iris,tolower)
fn(colnames(iris))
#>   Sepal.Length    Sepal.Width   Petal.Length    Petal.Width        Species 
#> "sepal length"  "sepal width" "petal length"  "petal width"      "species" 
```
