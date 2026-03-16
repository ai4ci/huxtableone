# huxtableone: Descriptive Tables for Observational or Interventional Studies

The goal of `huxtableone` is to make it easy to generate comparison
tables for journal publication. It converts a line list of experimental
or observational data into a summary table which can be grouped by an
intervention. Reporting summaries of this kind of data has to be aware
of missing items, and provide summary statistics and statistical
comparisons that are appropriate for the data. This is amenable to some
automated decision making but frequently such automation must be
overridden. `huxtableone` provides an automated one command statistical
summary table the output of which is highly configurable. The resulting
tables are in `huxtable` format ready for export into a wide range of
output file types.

## Installation

This package is hosted in the [AI4CI
r-universe](https://https://ai4ci.r-universe.dev/). Installation from
there is as follows:

``` r
options(repos = c(
  "ai4ci" = 'https://ai4ci.r-universe.dev/',
  CRAN = 'https://cloud.r-project.org'))

# Download and install huxtableone in R
install.packages("huxtableone")
```

You can install the development version of `huxtableone` from
[GitHub](https://github.com/ai4ci/huxtableone) with:

``` r
# install.packages("devtools")
devtools::install_github("ai4ci/huxtableone")
```

## Example

`huxtableone` is there to make descriptive statistics consistent and
easy. Summarising the dataset in a a nicely formatted summary table is
as simple as the following code. For the proper formatted output head to
the [main documentation website](https://ai4ci.github.io/huxtableone/).

``` r
# hide messages 
old = options(huxtableone.quiet = TRUE)

# generate table 
iris %>% 
  describe_population(everything()) 
```

| Variable | Characteristic | Value | Count (N=150) |
|:---|:---|:---|:---|
| Sepal.Length | Mean ± SD | 5.84 ± 0.828 | 150 |
| Sepal.Width | Mean ± SD | 3.06 ± 0.436 | 150 |
| Petal.Length | Median \[IQR\] | 4.35 \[1.6—5.1\] | 150 |
| Petal.Width | Median \[IQR\] | 1.3 \[0.3—1.8\] | 150 |
| Species | setosa % \[95% CI\] | 33.3% \[26.3%—41.2%\] | 50/150 |
|  | versicolor % \[95% CI\] | 33.3% \[26.3%—41.2%\] | 50/150 |
|  | virginica % \[95% CI\] | 33.3% \[26.3%—41.2%\] | 50/150 |
| Normal distributions determined by the Anderson-Darling test (P\>0.005) |  |  |  |

As a `huxtable` output the table can be saved as a wide range of formats
from spreadsheets or documents to latex and html (even as a github
README.md with limited success). This default output of `huxtableone`
can be very substantially customised to fit into a specific journal’s
requirements.
