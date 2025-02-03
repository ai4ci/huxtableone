
<!-- README.md is generated from README.Rmd. Please edit that file -->

# huxtableone: Descriptive Tables for Observational or Interventional Studies <a href='https://ai4ci.github.io/huxtableone/index.html'><img src='man/figures/logo.png' align="right" height="139" /></a>

<!-- badges: start -->

[![R-CMD-check](https://github.com/ai4ci/huxtableone/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/ai4ci/huxtableone/actions/workflows/R-CMD-check.yaml)
[![DOI](https://zenodo.org/badge/551386697.svg)](https://zenodo.org/badge/latestdoi/551386697)
[![huxtableone status
badge](https://ai4ci.r-universe.dev/badges/huxtableone)](https://ai4ci.r-universe.dev)
<!-- badges: end -->

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

<table class="huxtable" data-quarto-disable-processing="true" style="border-collapse: collapse; border: 0px; margin-bottom: 2em; margin-top: 2em; ; margin-left: auto; margin-right: auto;  ">
<col><col><col><col><tr>
<th style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 1pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">Variable</th><th style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 1pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">Characteristic</th><th style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 1pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">Value</th><th style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 1pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">Count (N=150)</th></tr>
<tr>
<td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">Sepal.Length</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">Mean ± SD</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">5.84 ± 0.828</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">150</td></tr>
<tr>
<td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">Sepal.Width</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">Mean ± SD</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">3.06 ± 0.436</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">150</td></tr>
<tr>
<td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">Petal.Length</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">Median [IQR]</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">4.35 [1.6—5.1]</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">150</td></tr>
<tr>
<td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">Petal.Width</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">Median [IQR]</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">1.3 [0.3—1.8]</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0.5pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">150</td></tr>
<tr>
<td rowspan="3" style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 1pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">Species</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">setosa % [95% CI]</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">33.3% [26.3%—41.2%]</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0.5pt 0pt 0pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">50/150</td></tr>
<tr>
<td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 0pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">versicolor % [95% CI]</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 0pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">33.3% [26.3%—41.2%]</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 0pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">50/150</td></tr>
<tr>
<td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 1pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">virginica % [95% CI]</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 1pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">33.3% [26.3%—41.2%]</td><td style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 1pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">50/150</td></tr>
<tr>
<td colspan="4" style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 1pt 0pt 0pt 0pt;    padding: 0pt 2pt 0pt 2pt; font-weight: normal; font-family: Arial; font-size: 8pt;">Normal distributions determined by the Anderson-Darling test (P&gt;0.005)</td></tr>
</table>

As a `huxtable` output the table can be saved as a wide range of formats
from spreadsheets or documents to latex and html (even as a github
README.md with limited success). This default output of `huxtableone`
can be very substantially customised to fit into a specific journal’s
requirements.
