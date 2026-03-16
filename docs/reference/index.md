# Package index

## Table creators

Create tabular summaries of data in a simple manner.

- [`describe_population()`](https://ai4ci.github.io/huxtableone/reference/describe_population.md)
  : Describe the population in a summary table
- [`describe_data()`](https://ai4ci.github.io/huxtableone/reference/describe_data.md)
  : Describe the data types and consistence
- [`compare_population()`](https://ai4ci.github.io/huxtableone/reference/compare_population.md)
  : Compares the population against an intervention in a summary table
- [`compare_outcomes()`](https://ai4ci.github.io/huxtableone/reference/compare_outcomes.md)
  : Compares multiple outcomes against an intervention in a summary
  table
- [`group_comparison()`](https://ai4ci.github.io/huxtableone/reference/group_comparison.md)
  : Extract one or more comparisons for inserting into text.
- [`compare_missing()`](https://ai4ci.github.io/huxtableone/reference/compare_missing.md)
  : Compares missing data against an intervention in a summary table
- [`remove_missing()`](https://ai4ci.github.io/huxtableone/reference/remove_missing.md)
  : Remove variables that fail a missing data test from models
- [`count_table()`](https://ai4ci.github.io/huxtableone/reference/count_table.md)
  : Group data count and calculate proportions by column.
- [`extract_comparison()`](https://ai4ci.github.io/huxtableone/reference/extract_comparison.md)
  : Get summary comparisons and statistics between variables as raw
  data.

## S3 functions

Create tabular summaries of data in a simple manner.

- [`as_t1_shape()`](https://ai4ci.github.io/huxtableone/reference/as_t1_shape.md)
  : Summarise a data set

- [`as_t1_signif()`](https://ai4ci.github.io/huxtableone/reference/as_t1_signif.md)
  : Compares the population against an intervention

- [`as_t1_summary()`](https://ai4ci.github.io/huxtableone/reference/as_t1_summary.md)
  : Summarise a population

- [`as_huxtable(`*`<t1_shape>`*`)`](https://ai4ci.github.io/huxtableone/reference/as_huxtable.t1_shape.md)
  :

  Convert a `t1_summary` object to a `huxtable`

- [`as_huxtable(`*`<t1_signif>`*`)`](https://ai4ci.github.io/huxtableone/reference/as_huxtable.t1_signif.md)
  :

  Convert a `t1_signif` S3 class to a huxtable

- [`as_huxtable(`*`<t1_summary>`*`)`](https://ai4ci.github.io/huxtableone/reference/as_huxtable.t1_summary.md)
  :

  Convert a `t1_summary` object to a `huxtable`

## Supporting functions

Modify data for making tabular summaries, making missing data more
explicit or by converting discrete data types to explicit factors.

- [`make_factors()`](https://ai4ci.github.io/huxtableone/reference/make_factors.md)
  : Convert discrete data to factors
- [`explicit_na()`](https://ai4ci.github.io/huxtableone/reference/explicit_na.md)
  : Make NA values in factor columns explicit
- [`get_footer_text()`](https://ai4ci.github.io/huxtableone/reference/get_footer_text.md)
  : Get footer text if available
- [`format_pvalue()`](https://ai4ci.github.io/huxtableone/reference/format_pvalue.md)
  : Format a p-value
- [`cut_integer()`](https://ai4ci.github.io/huxtableone/reference/cut_integer.md)
  : Cut and label an integer valued quantity
- [`label_extractor()`](https://ai4ci.github.io/huxtableone/reference/label_extractor.md)
  : Extract labels from a dataframe column attributes
- [`set_labels()`](https://ai4ci.github.io/huxtableone/reference/set_labels.md)
  : Set a label attribute
- [`extract_units()`](https://ai4ci.github.io/huxtableone/reference/extract_units.md)
  : Extracts units set as dataframe column attributes
- [`set_units()`](https://ai4ci.github.io/huxtableone/reference/set_units.md)
  : Title
- [`as_vars()`](https://ai4ci.github.io/huxtableone/reference/as_vars.md)
  : Reuse tidy-select syntax outside of a tidy-select function

## Configuration

- [`default.format`](https://ai4ci.github.io/huxtableone/reference/default.format.md)
  : Default table layout functions

## Test data

- [`test_cols`](https://ai4ci.github.io/huxtableone/reference/test_cols.md)
  : A list of columns for a test case
- [`bad_test_cols`](https://ai4ci.github.io/huxtableone/reference/bad_test_cols.md)
  : A list of columns for a test case
- [`diamonds`](https://ai4ci.github.io/huxtableone/reference/diamonds.md)
  : A copy of the diamonds dataset
- [`missing_diamonds`](https://ai4ci.github.io/huxtableone/reference/missing_diamonds.md)
  : A copy of the diamonds dataset
- [`mnar_two_class_1000`](https://ai4ci.github.io/huxtableone/reference/mnar_two_class_1000.md)
  : Missing not at random 2 class 1000 items
- [`multi_class_negative`](https://ai4ci.github.io/huxtableone/reference/multi_class_negative.md)
  : A multi-class dataset with equal random samples in each class
- [`one_class_test_100`](https://ai4ci.github.io/huxtableone/reference/one_class_test_100.md)
  : A single-class dataset with 100 items of random data
- [`one_class_test_1000`](https://ai4ci.github.io/huxtableone/reference/one_class_test_1000.md)
  : A single-class dataset with 1000 items of random data
- [`two_class_test`](https://ai4ci.github.io/huxtableone/reference/two_class_test.md)
  : A two-class dataset with random data
