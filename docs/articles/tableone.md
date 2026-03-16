# huxtableone: Getting started

## Formula versus `tidyselect` interface

`huxtableone` is implemented to allow a subset of columns in a large
dataset to be pulled into a table without any fuss. It is also designed
with a workflow in mind that involves building statistical models from
the data later. We assume the data follows a general pattern in that
there are one observation per row, individual columns are specific data
points in those observations and may be one of:

- `outcome`: something that we will be assessing in a statistical model,
  maybe a continuous outcome, or a time measure, or a logical measure.
- `intervention`: the thing that is varied between the different
  observations
- `covariates`: the other factors that may influence the outcome that we
  want to control for.

In the end we will want to construct a model that takes the following
high level structure:

`outcome ~ intervention + covariate_1 + covariate_2 + ... + covariate_n`

### Simple population description example

Before we build a model we need to firstly compare the distribution of
the covariates in the population and secondly compare them in the
intervention and non-intervention groups, usually done without reference
to outcome. To demonstrate this we are using the
[`survival::cgd`](https://rdrr.io/pkg/survival/man/cgd.html) data set.

``` r
cgd = survival::cgd %>% 
  # filter to include only the first visit
  dplyr::filter(enum==1) %>% 
  # make the steroids and propylac columns into a logical value
  # see later for a better way of doing this.
  dplyr::mutate(
    steroids = as.logical(steroids),
    propylac = as.logical(propylac)
  )
  

# A basic unstratified population description table is as follows:
cgd %>% describe_population(tidyselect::everything())
#> Warning: Unknown or uninitialised column: `level`.
```

| Variable | Characteristic | Value | Count (N=128) |
|:---|:---|:---|:---|
| id | Median \[IQR\] | 64.5 \[32.8—96.2\] | 128 |
| center | Harvard Medical Sch % \[95% CI\] | 3.1% \[1.2%—7.8%\] | 4/128 |
|  | Scripps Institute % \[95% CI\] | 12.5% \[7.8%—19.3%\] | 16/128 |
|  | Copenhagen % \[95% CI\] | 3.1% \[1.2%—7.8%\] | 4/128 |
|  | NIH % \[95% CI\] | 20.3% \[14.3%—28.1%\] | 26/128 |
|  | L.A. Children's Hosp % \[95% CI\] | 6.2% \[3.2%—11.8%\] | 8/128 |
|  | Mott Children's Hosp % \[95% CI\] | 7.0% \[3.7%—12.8%\] | 9/128 |
|  | Univ. of Utah % \[95% CI\] | 3.1% \[1.2%—7.8%\] | 4/128 |
|  | Univ. of Washington % \[95% CI\] | 3.1% \[1.2%—7.8%\] | 4/128 |
|  | Univ. of Minnesota % \[95% CI\] | 4.7% \[2.2%—9.8%\] | 6/128 |
|  | Univ. of Zurich % \[95% CI\] | 12.5% \[7.8%—19.3%\] | 16/128 |
|  | Texas Children's Hosp % \[95% CI\] | 6.2% \[3.2%—11.8%\] | 8/128 |
|  | Amsterdam % \[95% CI\] | 14.8% \[9.7%—22.0%\] | 19/128 |
|  | Mt. Sinai Medical Ctr % \[95% CI\] | 3.1% \[1.2%—7.8%\] | 4/128 |
| random |  | — | 128 |
| treat | placebo % \[95% CI\] | 50.8% \[42.2%—59.3%\] | 65/128 |
|  | rIFN-g % \[95% CI\] | 49.2% \[40.7%—57.8%\] | 63/128 |
| sex | male % \[95% CI\] | 81.2% \[73.6%—87.1%\] | 104/128 |
|  | female % \[95% CI\] | 18.8% \[12.9%—26.4%\] | 24/128 |
| age | Median \[IQR\] | 12 \[7—22\] | 128 |
| height | Median \[IQR\] | 141 \[116—170\] | 128 |
| weight | Median \[IQR\] | 34.8 \[20.7—59.2\] | 128 |
| inherit | X-linked % \[95% CI\] | 67.2% \[58.7%—74.7%\] | 86/128 |
|  | autosomal % \[95% CI\] | 32.8% \[25.3%—41.3%\] | 42/128 |
| steroids | false % \[95% CI\] | 97.7% \[93.3%—99.2%\] | 125/128 |
|  | true % \[95% CI\] | 2.3% \[0.8%—6.7%\] | 3/128 |
| propylac | false % \[95% CI\] | 13.3% \[8.5%—20.2%\] | 17/128 |
|  | true % \[95% CI\] | 86.7% \[79.8%—91.5%\] | 111/128 |
| hos.cat | US:NIH % \[95% CI\] | 20.3% \[14.3%—28.1%\] | 26/128 |
|  | US:other % \[95% CI\] | 49.2% \[40.7%—57.8%\] | 63/128 |
|  | Europe:Amsterdam % \[95% CI\] | 14.8% \[9.7%—22.0%\] | 19/128 |
|  | Europe:other % \[95% CI\] | 15.6% \[10.3%—22.9%\] | 20/128 |
| tstart | Median \[IQR\] | 0 \[0—0\] | 128 |
| enum | Median \[IQR\] | 1 \[1—1\] | 128 |
| tstop | Median \[IQR\] | 269 \[197—304\] | 128 |
| status | Median \[IQR\] | 0 \[0—1\] | 128 |
| Normal distributions determined by the Anderson-Darling test (P\>0.005) |  |  |  |

This could have been specified using the formula interface. In this
example we have taken an example of the formula we might wish to use for
a survival model and we reuse it to give us a more targetted descriptive
table. It is also possible to supply `huxtableone` with a relabelling
function that maps column names to printable labels, as demonstrated
here:

``` r
# define a formula - this might be reused in model building later
formula = Surv(tstart, tstop, status) ~ treat + 
  sex + age + height + weight + inherit + steroids + hos.cat

# set a table relabelling function
rename_cols = function(col) {
  dplyr::case_when(
    col == "hos.cat" ~ "Location",
    col == "steroids" ~ "Steroid treatment",
    TRUE ~ stringr::str_to_sentence(col)
  )
}
options("huxtableone.labeller"=rename_cols)

# create a simple description
cgd %>% describe_population(formula)
```

| Variable | Characteristic | Value | Count (N=128) |
|:---|:---|:---|:---|
| Treat | placebo % \[95% CI\] | 50.8% \[42.2%—59.3%\] | 65/128 |
|  | rIFN-g % \[95% CI\] | 49.2% \[40.7%—57.8%\] | 63/128 |
| Sex | male % \[95% CI\] | 81.2% \[73.6%—87.1%\] | 104/128 |
|  | female % \[95% CI\] | 18.8% \[12.9%—26.4%\] | 24/128 |
| Age | Median \[IQR\] | 12 \[7—22\] | 128 |
| Height | Median \[IQR\] | 141 \[116—170\] | 128 |
| Weight | Median \[IQR\] | 34.8 \[20.7—59.2\] | 128 |
| Inherit | X-linked % \[95% CI\] | 67.2% \[58.7%—74.7%\] | 86/128 |
|  | autosomal % \[95% CI\] | 32.8% \[25.3%—41.3%\] | 42/128 |
| Steroid treatment | false % \[95% CI\] | 97.7% \[93.3%—99.2%\] | 125/128 |
|  | true % \[95% CI\] | 2.3% \[0.8%—6.7%\] | 3/128 |
| Location | US:NIH % \[95% CI\] | 20.3% \[14.3%—28.1%\] | 26/128 |
|  | US:other % \[95% CI\] | 49.2% \[40.7%—57.8%\] | 63/128 |
|  | Europe:Amsterdam % \[95% CI\] | 14.8% \[9.7%—22.0%\] | 19/128 |
|  | Europe:other % \[95% CI\] | 15.6% \[10.3%—22.9%\] | 20/128 |
| Normal distributions determined by the Anderson-Darling test (P\>0.005) |  |  |  |

The relabelling function can either be passed to each invocation of
`huxtableone` functions or as an option as shown here, which makes the
labeller available to all subsequent calls. This is useful if you are
generating many tables from a single dataset.

We will generally use the formula interface from here on but for
exploration of larger datasets with more covariates the `tidyselect`
interface may be more useful.

### Comparing the population by intervention

In this example a more useful table compares the treatment groups. We
can use the same formula syntax for this, but in this case the first
predictor is assumed to be the intervention and the data set is compared
by intervention (in this case the `treat` column). From this we can
conclude that the population is well distributed between placebo and
treatment groups and there is no major bias in the randomisation
process:

``` r

# same as above
formula = Surv(tstart, tstop, status) ~ treat + 
  sex + age + height + weight + inherit + steroids + hos.cat

# labelling function is still active
cgd %>% compare_population(formula)
```

[TABLE]

Alternatively if we were using the `tidyselect` interface this alternate
syntax would have given us the same table. Note that we must group the
data by intervention, for the `tidyselect` to work as intended:

``` r
cgd %>% dplyr::group_by(treat) %>% 
  compare_population(sex,age,height,weight,inherit,steroids,hos.cat)
```

## Analysis of missing data

We need to make sure that not only is the data equivalent between the
intervention groups but also that missing data is not unevenly
distributed or excessive. Reporting on the frequency of missing data
stratified by intervention is also easy, and to demonstrate this we make
a data set with 10% of the placebo arm having missing values, but 25% of
the treatment arm:

``` r

# generate a dataset with values missing not at random compared to the intervention:
cgd_treat = cgd %>% dplyr::mutate(treat = as.character(treat)) %>% dplyr::filter(treat != "placebo")
cgd_placebo = cgd %>% dplyr::mutate(treat = as.character(treat)) %>% dplyr::filter(treat == "placebo")

set.seed(100)
mnar_cgd = dplyr::bind_rows(
  cgd_placebo %>% .make_missing(p_missing = 0.1),
  cgd_treat %>% .make_missing(p_missing = 0.25)
)
```

Comparing this new data set we see that there is significant differences
in some of the data (but not the `steroids` variable). As this is quite
a small dataset it is not sufficiently powered to reliably detect the
difference in missingness at this level (15% difference).

``` r
# compare the MNAR dataset against the intervention:
formula = Surv(tstart, tstop, status) ~ treat + 
  sex + age + height + weight + inherit + steroids + hos.cat

mnar_cgd %>% compare_missing(formula)
```

[TABLE]

with this analysis it is useful to be able to update the analysis
formula removing the variables with missing data so that we are
confident the models are based on reasonable data.

``` r

# formula can also be a list of formulae
new_formula = mnar_cgd %>% huxtableone::remove_missing(formula)
#> More than 10% of data is missing for variables Sex, Age, Height, Weight, Inherit, Steroid treatment, Location.
#> Data is missing not at random (compared to Treat) at a p-value<0.007 (0.05 over 7 comparisons) for variables Age.

print(new_formula)
#> [[1]]
#> Surv(tstart, tstop, status) ~ treat
```

## Conversion of discrete data

Using this new data set with missing data it may be necessary to
discretise some or all of the data, or convert logical values into
properly named factors.

``` r

decade = function(x) sprintf("%d-%d",x-(x%%10),x-(x%%10)+9)

discrete_cgd = mnar_cgd %>% 
  # pick out the first episode
  dplyr::filter(enum == 1) %>%
  # convert data
  make_factors(
    steroids,propylac,age,weight,height,
    .logical = c("received","not received"),
    .numeric = list(
      age="{decade(value)}",
      weight="{ifelse(value<20,'<20','20+')}",
      height="{ifelse(value<mean(value, na.rm=TRUE),'below average','above average')}"
    )
  )

formula = Surv(tstart, tstop, status) ~ treat + 
  sex + age + height + weight + inherit + steroids + hos.cat


old = options("huxtableone.show_pvalue_method"=TRUE)
# This comparison implicitly ignores missing values.
t = discrete_cgd %>% compare_population(formula)
options(old)

t
```

[TABLE]

``` r
# N.B. The following option is involved when converting integer data
# which decides how many levels of integer data are considered discrete
# and when to decide integer data can be treated as continuous:
options("huxtableone.max_discrete_levels"=0)
# and is described in the documentation for make_factors().
```

## Making missing factors explicit:

In the comparison above missing values were not included, and we should
be cautious of the findings. Because of the missingness `huxtableone`
will not calculate p-values. If factor values are missing (as in this
case) then we can include them as a new group and get a more robust
comparison which includes the distribution of missingness, and for which
we can calculate a p-value. However previously ordered variables, are
now regarded as unordered as we cannot determine the value of a missing
level.

``` r
discrete_cgd %>% explicit_na() %>% compare_population(formula)
```

[TABLE]

## Non biomedical data

Beyond the bio-medical example `huxtableone` can make any more general
comparison between data that has a structure like:

`~ group + observation_1 + observation_2 + ... + observation_n`

We will use the `iris` and the `diamonds` datasets to demonstrate this
more general use case for `huxtableone`.

``` r

# revert the labeller setting to the default
# and additionally hide the footer.
old = options(
  "huxtableone.labeller"=NULL,
  "huxtableone.show_pvalue_method"=FALSE,
  "huxtableone.hide_footer"=TRUE)

# the heuristics detect that Petals in the iris data set are not normally
# distributed and hence report median and IQR:
iris %>% dplyr::group_by(Species) %>% compare_population(tidyselect::everything())
```

|  |  | setosa | versicolor | virginica |  |
|:---|:---|:---|:---|:---|:---|
| Variable | Characteristic | Value (N=50) | Value (N=50) | Value (N=50) | P value |
| Sepal.Length | Mean ± SD | 5.01 ± 0.352 | 5.94 ± 0.516 | 6.59 ± 0.636 | \<0.001 |
| Sepal.Width | Mean ± SD | 3.43 ± 0.379 | 2.77 ± 0.314 | 2.97 ± 0.322 | \<0.001 |
| Petal.Length | Median \[IQR\] | 1.5 \[1.4—1.58\] | 4.35 \[4—4.6\] | 5.55 \[5.1—5.88\] | \<0.001 |
| Petal.Width | Median \[IQR\] | 0.2 \[0.2—0.3\] | 1.3 \[1.2—1.5\] | 2 \[1.8—2.3\] | \<0.001 |

``` r

options(old)
```

The `missing_diamonds` data set which is included in this package has
10% of the values removed. This demonstrates the need for reporting the
denominator.

``` r
# The counts sometimes seem redundant if there is no missing information:
# however in a data set with missing values the denominators are important:
missing_diamonds %>% describe_population(tidyselect::everything())
```

| Variable | Characteristic | Value | Count (N=53940) |
|:---|:---|:---|:---|
| Carat | Median \[IQR\] | 0.7 \[0.4—1.04\] | 48682 |
| Cut | Fair % \[95% CI\] | 3.0% \[2.8%—3.2%\] | 1454/48553 |
|  | Good % \[95% CI\] | 9.2% \[8.9%—9.5%\] | 4462/48553 |
|  | Very Good % \[95% CI\] | 22.3% \[21.9%—22.6%\] | 10816/48553 |
|  | Premium % \[95% CI\] | 25.7% \[25.3%—26.1%\] | 12460/48553 |
|  | Ideal % \[95% CI\] | 39.9% \[39.4%—40.3%\] | 19361/48553 |
| Color | D % \[95% CI\] | 12.5% \[12.2%—12.8%\] | 6079/48569 |
|  | E % \[95% CI\] | 18.3% \[18.0%—18.6%\] | 8886/48569 |
|  | F % \[95% CI\] | 17.7% \[17.4%—18.1%\] | 8613/48569 |
|  | G % \[95% CI\] | 20.9% \[20.5%—21.2%\] | 10137/48569 |
|  | H % \[95% CI\] | 15.4% \[15.1%—15.7%\] | 7466/48569 |
|  | I % \[95% CI\] | 10.0% \[9.8%—10.3%\] | 4876/48569 |
|  | J % \[95% CI\] | 5.2% \[5.0%—5.4%\] | 2512/48569 |
| Clarity | I1 % \[95% CI\] | 1.4% \[1.3%—1.5%\] | 664/48527 |
|  | SI2 % \[95% CI\] | 17.0% \[16.7%—17.4%\] | 8265/48527 |
|  | SI1 % \[95% CI\] | 24.2% \[23.8%—24.6%\] | 11756/48527 |
|  | VS2 % \[95% CI\] | 22.7% \[22.3%—23.1%\] | 11020/48527 |
|  | VS1 % \[95% CI\] | 15.2% \[14.8%—15.5%\] | 7355/48527 |
|  | VVS2 % \[95% CI\] | 9.4% \[9.2%—9.7%\] | 4570/48527 |
|  | VVS1 % \[95% CI\] | 6.8% \[6.6%—7.0%\] | 3298/48527 |
|  | IF % \[95% CI\] | 3.3% \[3.1%—3.5%\] | 1599/48527 |
| Depth | Median \[IQR\] | 61.8 \[61—62.5\] | 48584 |
| Table | Median \[IQR\] | 57 \[56—59\] | 48707 |
| Price | Median \[IQR\] | 2.41e+03 \[952—5.33e+03\] | 48675 |
| X | Median \[IQR\] | 5.69 \[4.72—6.54\] | 48577 |
| Y | Median \[IQR\] | 5.71 \[4.72—6.54\] | 48578 |
| Z | Median \[IQR\] | 3.52 \[2.91—4.03\] | 48559 |
| Is_colored | clear % \[95% CI\] | 30.7% \[30.3%—31.1%\] | 16572/53940 |
|  | colored % \[95% CI\] | 69.3% \[68.9%—69.7%\] | 37368/53940 |
| Normal distributions determined by the Anderson-Darling test (P\>0.005) |  |  |  |
