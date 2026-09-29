# Using Skimr

## Introduction

`skimr` is designed to provide summary statistics about variables in
data frames, tibbles, data tables and vectors. It is opinionated in its
defaults, but easy to modify.

In base R, the most similar functions are
[`summary()`](https://rdrr.io/r/base/summary.html) for vectors and data
frames and [`fivenum()`](https://rdrr.io/r/stats/fivenum.html) for
numeric vectors:

``` r

summary(iris)
```

    ##   Sepal.Length    Sepal.Width     Petal.Length    Petal.Width   
    ##  Min.   :4.300   Min.   :2.000   Min.   :1.000   Min.   :0.100  
    ##  1st Qu.:5.100   1st Qu.:2.800   1st Qu.:1.600   1st Qu.:0.300  
    ##  Median :5.800   Median :3.000   Median :4.350   Median :1.300  
    ##  Mean   :5.843   Mean   :3.057   Mean   :3.758   Mean   :1.199  
    ##  3rd Qu.:6.400   3rd Qu.:3.300   3rd Qu.:5.100   3rd Qu.:1.800  
    ##  Max.   :7.900   Max.   :4.400   Max.   :6.900   Max.   :2.500  
    ##        Species  
    ##  setosa    :50  
    ##  versicolor:50  
    ##  virginica :50  
    ##                 
    ##                 
    ## 

``` r

summary(iris$Sepal.Length)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   4.300   5.100   5.800   5.843   6.400   7.900

``` r

fivenum(iris$Sepal.Length)
```

    ## [1] 4.3 5.1 5.8 6.4 7.9

``` r

summary(iris$Species)
```

    ##     setosa versicolor  virginica 
    ##         50         50         50

## The `skim()` function

The core function of `skimr` is
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md), which is
designed to work with (grouped) data frames, and will try coerce other
objects to data frames if possible. Like
[`summary()`](https://rdrr.io/r/base/summary.html),
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md)’s method
for data frames presents results for every column; the statistics it
provides depend on the class of the variable.

### Skimming data frames

By design, the main focus of `skimr` is on data frames; it is intended
to fit well within a data [pipeline](https://r4ds.had.co.nz/pipes.html)
and relies extensively on [tidyverse](https://tidyverse.org/)
vocabulary, which focuses on data frames.

Results of [`skim()`](https://docs.ropensci.org/skimr/reference/skim.md)
are *printed* horizontally, with one section per variable type and one
row per variable.

``` r

library(skimr)
skim(iris)
```

    ## ── Data Summary ────────────────────────
    ##                            Values
    ## Name                       iris  
    ## Number of rows             150   
    ## Number of columns          5     
    ## _______________________          
    ## Column type frequency:           
    ##   factor                   1     
    ##   numeric                  4     
    ## ________________________         
    ## Group variables            None  
    ## 
    ## ── Variable type: factor ───────────────────────────────────────────────────────
    ##   skim_variable n_missing complete_rate ordered n_unique
    ## 1 Species               0             1 FALSE          3
    ##   top_counts               
    ## 1 set: 50, ver: 50, vir: 50
    ## 
    ## ── Variable type: numeric ──────────────────────────────────────────────────────
    ##   skim_variable n_missing complete_rate mean    sd  p0 p25  p50 p75 p100 hist 
    ## 1 Sepal.Length          0             1 5.84 0.828 4.3 5.1 5.8  6.4  7.9 ▆▇▇▅▂
    ## 2 Sepal.Width           0             1 3.06 0.436 2   2.8 3    3.3  4.4 ▁▆▇▂▁
    ## 3 Petal.Length          0             1 3.76 1.77  1   1.6 4.35 5.1  6.9 ▇▁▆▇▂
    ## 4 Petal.Width           0             1 1.20 0.762 0.1 0.3 1.3  1.8  2.5 ▇▁▇▅▃

The format of the results are a single wide data frame combining the
results, with some additional attributes and two metadata columns:

- `skim_variable`: name of the original variable
- `skim_type`: class of the variable

Unlike many other objects within `R`, these columns are intrinsic to the
`skim_df` class. Dropping these variables will result in a coercion to a
`tibble`. The
[`is_skim_df()`](https://docs.ropensci.org/skimr/reference/skim-obj.md)
function is used to assert that an object is a skim_df.

``` r

skim(iris) |> is_skim_df()
```

    ## [1] TRUE
    ## attr(,"message")
    ## character(0)

``` r

skim(iris) |>
  dplyr::select(-skim_type, -skim_variable) |>
  is_skim_df()
```

    ## [1] FALSE
    ## attr(,"message")
    ## [1] "Object is not a `skim_df`: missing column `skim_type`; missing column `skim_variable`"

``` r

skim(iris) |>
  dplyr::select(-n_missing) |>
  is_skim_df()
```

    ## [1] TRUE
    ## attr(,"message")
    ## character(0)

In order to avoid type coercion, columns for summary statistics for
different types are prefixed with the corresponding `skim_type`. This
means that the columns of the `skim_df` are somewhat sparse, with quite
a few missing values. This is because for some statistics the
representations for different types of variables is different. For
example, the mean of a Date variable and of a numeric variable are
represented differently when printing, but this cannot be supported in a
single vector. The exception to this are `n_missing` and `complete_rate`
(missing/number of observations) which are the same for all types of
variables.

``` r

skim(iris) |>
  tibble::as_tibble()
```

    ## # A tibble: 5 × 15
    ##   skim_type skim_variable n_missing complete_rate factor.ordered factor.n_unique
    ##   <chr>     <chr>             <int>         <dbl> <lgl>                    <int>
    ## 1 factor    Species               0             1 FALSE                        3
    ## 2 numeric   Sepal.Length          0             1 NA                          NA
    ## 3 numeric   Sepal.Width           0             1 NA                          NA
    ## 4 numeric   Petal.Length          0             1 NA                          NA
    ## 5 numeric   Petal.Width           0             1 NA                          NA
    ## # ℹ 9 more variables: factor.top_counts <chr>, numeric.mean <dbl>,
    ## #   numeric.sd <dbl>, numeric.p0 <dbl>, numeric.p25 <dbl>, numeric.p50 <dbl>,
    ## #   numeric.p75 <dbl>, numeric.p100 <dbl>, numeric.hist <chr>

This is in contrast to
[`summary.data.frame()`](https://rdrr.io/r/base/summary.html), which
stores statistics in a `table`. The distinction is important, because
the `skim_df` object is pipeable and easy to use for additional
manipulation: for example, the user could select all of the variable
means, or all summary statistics for a specific variable.

``` r

skim(iris) |>
  dplyr::filter(skim_variable == "Petal.Length")
```

    ## ── Data Summary ────────────────────────
    ##                            Values
    ## Name                       iris  
    ## Number of rows             150   
    ## Number of columns          5     
    ## _______________________          
    ## Column type frequency:           
    ##   numeric                  1     
    ## ________________________         
    ## Group variables            None  
    ## 
    ## ── Variable type: numeric ──────────────────────────────────────────────────────
    ##   skim_variable n_missing complete_rate mean   sd p0 p25  p50 p75 p100 hist 
    ## 1 Petal.Length          0             1 3.76 1.77  1 1.6 4.35 5.1  6.9 ▇▁▆▇▂

Most `dplyr` verbs should work as expected.

``` r

skim(iris) |>
  dplyr::select(skim_type, skim_variable, n_missing)
```

    ## ── Data Summary ────────────────────────
    ##                            Values
    ## Name                       iris  
    ## Number of rows             150   
    ## Number of columns          5     
    ## _______________________          
    ## Column type frequency:           
    ##   factor                   1     
    ##   numeric                  4     
    ## ________________________         
    ## Group variables            None  
    ## 
    ## ── Variable type: factor ───────────────────────────────────────────────────────
    ##   skim_variable n_missing
    ## 1 Species               0
    ## 
    ## ── Variable type: numeric ──────────────────────────────────────────────────────
    ##   skim_variable n_missing
    ## 1 Sepal.Length          0
    ## 2 Sepal.Width           0
    ## 3 Petal.Length          0
    ## 4 Petal.Width           0

The base skimmers `n_missing` and `complete_rate` are computed for all
of the columns in the data. But all other type-based skimmers have a
namespace. You need to use a `skim_type` prefix to refer to correct
column.

``` r

skim(iris) |>
  dplyr::select(skim_type, skim_variable, numeric.mean)
```

    ## ── Data Summary ────────────────────────
    ##                            Values
    ## Name                       iris  
    ## Number of rows             150   
    ## Number of columns          5     
    ## _______________________          
    ## Column type frequency:           
    ##   factor                   1     
    ##   numeric                  4     
    ## ________________________         
    ## Group variables            None  
    ## 
    ## ── Variable type: numeric ──────────────────────────────────────────────────────
    ##   skim_variable mean
    ## 1 Sepal.Length  5.84
    ## 2 Sepal.Width   3.06
    ## 3 Petal.Length  3.76
    ## 4 Petal.Width   1.20

[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md) also
supports grouped data created by
[`dplyr::group_by()`](https://dplyr.tidyverse.org/reference/group_by.html).
In this case, one additional column for each grouping variable is added
to the `skim_df` object.

``` r

iris |>
  dplyr::group_by(Species) |>
  skim()
```

    ## ── Data Summary ────────────────────────
    ##                            Values                      
    ## Name                       dplyr::group_by(iris, Spe...
    ## Number of rows             150                         
    ## Number of columns          5                           
    ## _______________________                                
    ## Column type frequency:                                 
    ##   numeric                  4                           
    ## ________________________                               
    ## Group variables            Species                     
    ## 
    ## ── Variable type: numeric ──────────────────────────────────────────────────────
    ##    skim_variable Species    n_missing complete_rate  mean    sd  p0  p25  p50
    ##  1 Sepal.Length  setosa             0             1 5.01  0.352 4.3 4.8  5   
    ##  2 Sepal.Length  versicolor         0             1 5.94  0.516 4.9 5.6  5.9 
    ##  3 Sepal.Length  virginica          0             1 6.59  0.636 4.9 6.22 6.5 
    ##  4 Sepal.Width   setosa             0             1 3.43  0.379 2.3 3.2  3.4 
    ##  5 Sepal.Width   versicolor         0             1 2.77  0.314 2   2.52 2.8 
    ##  6 Sepal.Width   virginica          0             1 2.97  0.322 2.2 2.8  3   
    ##  7 Petal.Length  setosa             0             1 1.46  0.174 1   1.4  1.5 
    ##  8 Petal.Length  versicolor         0             1 4.26  0.470 3   4    4.35
    ##  9 Petal.Length  virginica          0             1 5.55  0.552 4.5 5.1  5.55
    ## 10 Petal.Width   setosa             0             1 0.246 0.105 0.1 0.2  0.2 
    ## 11 Petal.Width   versicolor         0             1 1.33  0.198 1   1.2  1.3 
    ## 12 Petal.Width   virginica          0             1 2.03  0.275 1.4 1.8  2   
    ##     p75 p100 hist 
    ##  1 5.2   5.8 ▃▃▇▅▁
    ##  2 6.3   7   ▂▇▆▃▃
    ##  3 6.9   7.9 ▁▃▇▃▂
    ##  4 3.68  4.4 ▁▃▇▅▂
    ##  5 3     3.4 ▁▅▆▇▂
    ##  6 3.18  3.8 ▂▆▇▅▁
    ##  7 1.58  1.9 ▁▃▇▃▁
    ##  8 4.6   5.1 ▂▂▇▇▆
    ##  9 5.88  6.9 ▃▇▇▃▂
    ## 10 0.3   0.6 ▇▂▂▁▁
    ## 11 1.5   1.8 ▅▇▃▆▁
    ## 12 2.3   2.5 ▂▇▆▅▇

Individual columns from a data frame may be selected using
tidyverse-style selectors.

``` r

skim(iris, Sepal.Length, Species)
```

    ## ── Data Summary ────────────────────────
    ##                            Values
    ## Name                       iris  
    ## Number of rows             150   
    ## Number of columns          5     
    ## _______________________          
    ## Column type frequency:           
    ##   factor                   1     
    ##   numeric                  1     
    ## ________________________         
    ## Group variables            None  
    ## 
    ## ── Variable type: factor ───────────────────────────────────────────────────────
    ##   skim_variable n_missing complete_rate ordered n_unique
    ## 1 Species               0             1 FALSE          3
    ##   top_counts               
    ## 1 set: 50, ver: 50, vir: 50
    ## 
    ## ── Variable type: numeric ──────────────────────────────────────────────────────
    ##   skim_variable n_missing complete_rate mean    sd  p0 p25 p50 p75 p100 hist 
    ## 1 Sepal.Length          0             1 5.84 0.828 4.3 5.1 5.8 6.4  7.9 ▆▇▇▅▂

Or with common `select` helpers.

``` r

skim(iris, starts_with("Sepal"))
```

    ## ── Data Summary ────────────────────────
    ##                            Values
    ## Name                       iris  
    ## Number of rows             150   
    ## Number of columns          5     
    ## _______________________          
    ## Column type frequency:           
    ##   numeric                  2     
    ## ________________________         
    ## Group variables            None  
    ## 
    ## ── Variable type: numeric ──────────────────────────────────────────────────────
    ##   skim_variable n_missing complete_rate mean    sd  p0 p25 p50 p75 p100 hist 
    ## 1 Sepal.Length          0             1 5.84 0.828 4.3 5.1 5.8 6.4  7.9 ▆▇▇▅▂
    ## 2 Sepal.Width           0             1 3.06 0.436 2   2.8 3   3.3  4.4 ▁▆▇▂▁

If an individual column is of an unsupported class, it is treated as a
character variable with a warning.

## Skimming vectors

In `skimr` v2,
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md) will
attempt to coerce non-data frames (such as vectors and matrices) to data
frames. In most cases with vectors, the object being evaluated should be
equivalent to wrapping the object in
[`as.data.frame()`](https://rdrr.io/r/base/as.data.frame.html).

For example, the `lynx` data set is class `ts`.

``` r

skim(lynx)
```

    ## ── Data Summary ────────────────────────
    ##                            Values
    ## Name                       lynx  
    ## Number of rows             114   
    ## Number of columns          1     
    ## _______________________          
    ## Column type frequency:           
    ##   ts                       1     
    ## ________________________         
    ## Group variables            None  
    ## 
    ## ── Variable type: ts ───────────────────────────────────────────────────────────
    ##   skim_variable n_missing complete_rate start  end frequency deltat  mean    sd
    ## 1 x                     0             1  1821 1934         1      1 1538. 1586.
    ##   min  max median line_graph
    ## 1  39 6991    771 ⡈⢄⡠⢁⣀⠒⣀⠔

Which is the same as coercing to a data frame.

``` r

all.equal(skim(lynx), skim(as.data.frame(lynx)))
```

    ## [1] "Attributes: < Component \"df_name\": 1 string mismatch >"

## Skimming matrices

`skimr` does not support skimming matrices directly but coerces them to
data frames. Columns in the matrix become variables. This behavior is
similar to [`summary.matrix()`](https://rdrr.io/r/base/summary.html)).
Three possible ways to handle matrices with
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md) parallel
the three variations of the mean function for matrices.

``` r

m <- matrix(c(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12), nrow = 4, ncol = 3)
m
```

    ##      [,1] [,2] [,3]
    ## [1,]    1    5    9
    ## [2,]    2    6   10
    ## [3,]    3    7   11
    ## [4,]    4    8   12

Skimming the matrix produces similar results to
[`colMeans()`](https://rdrr.io/r/base/colSums.html).

``` r

colMeans(m)
```

    ## [1]  2.5  6.5 10.5

``` r

skim(m) # Similar to summary.matrix and colMeans()
```

    ## ── Data Summary ────────────────────────
    ##                            Values
    ## Name                       m     
    ## Number of rows             4     
    ## Number of columns          3     
    ## _______________________          
    ## Column type frequency:           
    ##   numeric                  3     
    ## ________________________         
    ## Group variables            None  
    ## 
    ## ── Variable type: numeric ──────────────────────────────────────────────────────
    ##   skim_variable n_missing complete_rate mean   sd p0  p25  p50   p75 p100 hist 
    ## 1 V1                    0             1  2.5 1.29  1 1.75  2.5  3.25    4 ▇▇▁▇▇
    ## 2 V2                    0             1  6.5 1.29  5 5.75  6.5  7.25    8 ▇▇▁▇▇
    ## 3 V3                    0             1 10.5 1.29  9 9.75 10.5 11.2    12 ▇▇▁▇▇

Skimming the transpose of the matrix will give row-wise results.

``` r

rowMeans(m)
```

    ## [1] 5 6 7 8

``` r

skim(t(m))
```

    ## ── Data Summary ────────────────────────
    ##                            Values
    ## Name                       t(m)  
    ## Number of rows             3     
    ## Number of columns          4     
    ## _______________________          
    ## Column type frequency:           
    ##   numeric                  4     
    ## ________________________         
    ## Group variables            None  
    ## 
    ## ── Variable type: numeric ──────────────────────────────────────────────────────
    ##   skim_variable n_missing complete_rate mean sd p0 p25 p50 p75 p100 hist 
    ## 1 V1                    0             1    5  4  1   3   5   7    9 ▇▁▇▁▇
    ## 2 V2                    0             1    6  4  2   4   6   8   10 ▇▁▇▁▇
    ## 3 V3                    0             1    7  4  3   5   7   9   11 ▇▁▇▁▇
    ## 4 V4                    0             1    8  4  4   6   8  10   12 ▇▁▇▁▇

And call [`c()`](https://rdrr.io/r/base/c.html) on the matrix to get
results across all columns.

``` r

skim(c(m))
```

    ## ── Data Summary ────────────────────────
    ##                            Values
    ## Name                       c(m)  
    ## Number of rows             12    
    ## Number of columns          1     
    ## _______________________          
    ## Column type frequency:           
    ##   numeric                  1     
    ## ________________________         
    ## Group variables            None  
    ## 
    ## ── Variable type: numeric ──────────────────────────────────────────────────────
    ##   skim_variable n_missing complete_rate mean   sd p0  p25 p50  p75 p100 hist 
    ## 1 data                  0             1  6.5 3.61  1 3.75 6.5 9.25   12 ▇▅▅▅▇

``` r

mean(m)
```

    ## [1] 6.5

### Skimming without modification

[`skim_tee()`](https://docs.ropensci.org/skimr/reference/skim.md)
produces the same printed version as
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md) but
returns the original, unmodified data frame. This allows for continued
piping of the original data.

``` r

iris_setosa <- iris |>
  skim_tee() |>
  dplyr::filter(Species == "setosa")
```

    ## ── Data Summary ────────────────────────
    ##                            Values
    ## Name                       data  
    ## Number of rows             150   
    ## Number of columns          5     
    ## _______________________          
    ## Column type frequency:           
    ##   factor                   1     
    ##   numeric                  4     
    ## ________________________         
    ## Group variables            None  
    ## 
    ## ── Variable type: factor ───────────────────────────────────────────────────────
    ##   skim_variable n_missing complete_rate ordered n_unique
    ## 1 Species               0             1 FALSE          3
    ##   top_counts               
    ## 1 set: 50, ver: 50, vir: 50
    ## 
    ## ── Variable type: numeric ──────────────────────────────────────────────────────
    ##   skim_variable n_missing complete_rate mean    sd  p0 p25  p50 p75 p100 hist 
    ## 1 Sepal.Length          0             1 5.84 0.828 4.3 5.1 5.8  6.4  7.9 ▆▇▇▅▂
    ## 2 Sepal.Width           0             1 3.06 0.436 2   2.8 3    3.3  4.4 ▁▆▇▂▁
    ## 3 Petal.Length          0             1 3.76 1.77  1   1.6 4.35 5.1  6.9 ▇▁▆▇▂
    ## 4 Petal.Width           0             1 1.20 0.762 0.1 0.3 1.3  1.8  2.5 ▇▁▇▅▃

``` r

head(iris_setosa)
```

    ##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    ## 1          5.1         3.5          1.4         0.2  setosa
    ## 2          4.9         3.0          1.4         0.2  setosa
    ## 3          4.7         3.2          1.3         0.2  setosa
    ## 4          4.6         3.1          1.5         0.2  setosa
    ## 5          5.0         3.6          1.4         0.2  setosa
    ## 6          5.4         3.9          1.7         0.4  setosa

Note, that
[`skim_tee()`](https://docs.ropensci.org/skimr/reference/skim.md) is
customized differently than `skim` itself. See below for more details.

## Reshaping the results from `skim()`

As noted above,
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md) returns a
wide data frame. This is usually the most sensible format for the
majority of operations when investigating data, but the package has some
other functions to help with edge cases.

First,
[`partition()`](https://docs.ropensci.org/skimr/reference/partition.md)
returns a named list of the wide data frames for each data type. Unlike
the original data the partitioned data only has columns corresponding to
the skimming functions used for this data type. These data frames are,
therefore, not `skim_df` objects.

``` r

iris |>
  skim() |>
  partition()
```

    ## $factor
    ## 
    ## ── Variable type: factor ───────────────────────────────────────────────────────
    ##   skim_variable n_missing complete_rate ordered n_unique top_counts             
    ## 1 Species               0             1 FALSE          3 set: 50, ver: 50, vir:…
    ## 
    ## $numeric
    ## 
    ## ── Variable type: numeric ──────────────────────────────────────────────────────
    ##   skim_variable n_missing complete_rate mean    sd  p0 p25  p50 p75 p100 hist 
    ## 1 Sepal.Length          0             1 5.84 0.828 4.3 5.1 5.8  6.4  7.9 ▆▇▇▅▂
    ## 2 Sepal.Width           0             1 3.06 0.436 2   2.8 3    3.3  4.4 ▁▆▇▂▁
    ## 3 Petal.Length          0             1 3.76 1.77  1   1.6 4.35 5.1  6.9 ▇▁▆▇▂
    ## 4 Petal.Width           0             1 1.20 0.762 0.1 0.3 1.3  1.8  2.5 ▇▁▇▅▃

Alternatively,
[`yank()`](https://docs.ropensci.org/skimr/reference/partition.md)
selects only the subtable for a specific type. Think of it like
[`dplyr::select`](https://dplyr.tidyverse.org/reference/select.html) on
column types in the original data. Again, unsuitable columns are
dropped.

``` r

iris |>
  skim() |>
  yank("numeric")
```

    ## 
    ## ── Variable type: numeric ──────────────────────────────────────────────────────
    ##   skim_variable n_missing complete_rate mean    sd  p0 p25  p50 p75 p100 hist 
    ## 1 Sepal.Length          0             1 5.84 0.828 4.3 5.1 5.8  6.4  7.9 ▆▇▇▅▂
    ## 2 Sepal.Width           0             1 3.06 0.436 2   2.8 3    3.3  4.4 ▁▆▇▂▁
    ## 3 Petal.Length          0             1 3.76 1.77  1   1.6 4.35 5.1  6.9 ▇▁▆▇▂
    ## 4 Petal.Width           0             1 1.20 0.762 0.1 0.3 1.3  1.8  2.5 ▇▁▇▅▃

[`to_long()`](https://docs.ropensci.org/skimr/reference/to_long.md)
returns a single long data frame with columns `variable`, `type`,
`statistic` and `formatted`. This is similar but not identical to the
`skim_df` object in `skimr` v1.

``` r

iris |>
  skim() |>
  to_long() |>
  head()
```

    ## # A tibble: 6 × 4
    ##   skim_type skim_variable stat              formatted                
    ##   <chr>     <chr>         <chr>             <chr>                    
    ## 1 factor    Species       n_missing         0                        
    ## 2 factor    Species       complete_rate     1                        
    ## 3 factor    Species       factor.ordered    FALSE                    
    ## 4 factor    Species       factor.n_unique   3                        
    ## 5 factor    Species       factor.top_counts set: 50, ver: 50, vir: 50
    ## 6 numeric   Sepal.Length  n_missing         0

Since the `skim_variable` and `skim_type` columns are a core component
of the `skim_df` class, it’s possible to get unwanted side effects when
using
[`dplyr::select()`](https://dplyr.tidyverse.org/reference/select.html).
Instead, use
[`focus()`](https://docs.ropensci.org/skimr/reference/focus.md) to
select columns of the skimmed results and keep them as a `skim_df`; it
always keeps the metadata column.

``` r

iris |>
  skim() |>
  focus(n_missing, numeric.mean)
```

    ## ── Data Summary ────────────────────────
    ##                            Values
    ## Name                       iris  
    ## Number of rows             150   
    ## Number of columns          5     
    ## _______________________          
    ## Column type frequency:           
    ##   factor                   1     
    ##   numeric                  4     
    ## ________________________         
    ## Group variables            None  
    ## 
    ## ── Variable type: factor ───────────────────────────────────────────────────────
    ##   skim_variable n_missing
    ## 1 Species               0
    ## 
    ## ── Variable type: numeric ──────────────────────────────────────────────────────
    ##   skim_variable n_missing mean
    ## 1 Sepal.Length          0 5.84
    ## 2 Sepal.Width           0 3.06
    ## 3 Petal.Length          0 3.76
    ## 4 Petal.Width           0 1.20

## Rendering the results of `skim()`

The `skim_df` object is a wide data frame. The display is created by
default using
[`print.skim_df()`](https://docs.ropensci.org/skimr/reference/print.md);
users can specify additional options by explicitly calling
`print([skim_df object], ...)`.

For documents rendered by `knitr`, the package provides a custom
`knit_print` method. To use it, the final line of your code chunk should
have a `skim_df` object.

``` r

skim(Orange)
```

|                                                  |        |
|:-------------------------------------------------|:-------|
| Name                                             | Orange |
| Number of rows                                   | 35     |
| Number of columns                                | 3      |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |        |
| Column type frequency:                           |        |
| factor                                           | 1      |
| numeric                                          | 2      |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |        |
| Group variables                                  | None   |

Data summary {.table}

**Variable type: factor**

| skim_variable | n_missing | complete_rate | ordered | n_unique | top_counts             |
|:--------------|----------:|--------------:|:--------|---------:|:-----------------------|
| Tree          |         0 |             1 | TRUE    |        5 | 3: 7, 1: 7, 5: 7, 2: 7 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate | mean | sd | p0 | p25 | p50 | p75 | p100 | hist |
|:---|---:|---:|---:|---:|---:|---:|---:|---:|---:|:---|
| age | 0 | 1 | 922.14 | 491.86 | 118 | 484.0 | 1004 | 1372.0 | 1582 | ▃▇▁▇▇ |
| circumference | 0 | 1 | 115.86 | 57.49 | 30 | 65.5 | 115 | 161.5 | 214 | ▇▃▇▇▅ |

The same type of rendering is available from reshaped `skim_df` objects,
those generated by
[`partition()`](https://docs.ropensci.org/skimr/reference/partition.md)
and [`yank()`](https://docs.ropensci.org/skimr/reference/partition.md)
in particular.

``` r

skim(Orange) |>
  yank("numeric")
```

**Variable type: numeric**

| skim_variable | n_missing | complete_rate | mean | sd | p0 | p25 | p50 | p75 | p100 | hist |
|:---|---:|---:|---:|---:|---:|---:|---:|---:|---:|:---|
| age | 0 | 1 | 922.14 | 491.86 | 118 | 484.0 | 1004 | 1372.0 | 1582 | ▃▇▁▇▇ |
| circumference | 0 | 1 | 115.86 | 57.49 | 30 | 65.5 | 115 | 161.5 | 214 | ▇▃▇▇▅ |

## Customizing print options

Although its not a common use case outside of writing vignettes about
`skimr`, you can fall back to default printing methods by adding the
chunk option `render = knitr::normal_print`.

You can also disable the `skimr` summary by setting the chunk option
`skimr_include_summary = FALSE`.

You can change the number of digits shown in the columns of generated
statistics by changing the `skimr_digits` chunk option.

## Modifying `skim()`

`skimr` is opinionated in its choice of defaults, but users can easily
add, replace, or remove the statistics for a class. For interactive use,
you can create your own skimming function with the
[`skim_with()`](https://docs.ropensci.org/skimr/reference/skim_with.md)
factory. `skimr` also has an API for extensions in other packages.
Working with that is covered later.

To add a statistic for a data type, create an
[`sfl()`](https://docs.ropensci.org/skimr/reference/sfl.md) (a `skimr`
function list) for each class that you want to change:

``` r

my_skim <- skim_with(numeric = sfl(new_mad = mad))
my_skim(faithful)
```

|                                                  |          |
|:-------------------------------------------------|:---------|
| Name                                             | faithful |
| Number of rows                                   | 272      |
| Number of columns                                | 2        |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |          |
| Column type frequency:                           |          |
| numeric                                          | 2        |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |          |
| Group variables                                  | None     |

Data summary {.table}

**Variable type: numeric**

| skim_variable | n_missing | complete_rate | mean | sd | p0 | p25 | p50 | p75 | p100 | hist | new_mad |
|:---|---:|---:|---:|---:|---:|---:|---:|---:|---:|:---|---:|
| eruptions | 0 | 1 | 3.49 | 1.14 | 1.6 | 2.16 | 4 | 4.45 | 5.1 | ▇▂▂▇▇ | 0.95 |
| waiting | 0 | 1 | 70.90 | 13.59 | 43.0 | 58.00 | 76 | 82.00 | 96.0 | ▃▃▂▇▂ | 11.86 |

As the previous example suggests, the default is to append *new* summary
statistics to the preexisting set. This behavior isn’t always desirable,
especially when you want lots of changes. To stop appending, set
`append = FALSE`.

``` r

my_skim <- skim_with(numeric = sfl(new_mad = mad), append = FALSE)
my_skim(faithful)
```

|                                                  |          |
|:-------------------------------------------------|:---------|
| Name                                             | faithful |
| Number of rows                                   | 272      |
| Number of columns                                | 2        |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |          |
| Column type frequency:                           |          |
| numeric                                          | 2        |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |          |
| Group variables                                  | None     |

Data summary {.table}

**Variable type: numeric**

| skim_variable | n_missing | complete_rate | new_mad |
|:--------------|----------:|--------------:|--------:|
| eruptions     |         0 |             1 |    0.95 |
| waiting       |         0 |             1 |   11.86 |

You can also use
[`skim_with()`](https://docs.ropensci.org/skimr/reference/skim_with.md)
to remove specific statistics by setting them to `NULL`. This is
commonly used to disable the inline histograms and spark graphs.

``` r

no_hist <- skim_with(ts = sfl(line_graph = NULL))
no_hist(Nile)
```

|                                                  |      |
|:-------------------------------------------------|:-----|
| Name                                             | Nile |
| Number of rows                                   | 100  |
| Number of columns                                | 1    |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |      |
| Column type frequency:                           |      |
| ts                                               | 1    |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |      |
| Group variables                                  | None |

Data summary {.table}

**Variable type: ts**

| skim_variable | n_missing | complete_rate | start | end | frequency | deltat | mean | sd | min | max | median |
|:---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| x | 0 | 1 | 1871 | 1970 | 1 | 1 | 919.35 | 169.23 | 456 | 1370 | 893.5 |

The same pattern applies to changing skimmers for multiple classes
simultaneously. If you want to partially-apply function arguments, use
the Tidyverse lambda syntax.

``` r

my_skim <- skim_with(
  numeric = sfl(total = ~ sum(., na.rm = TRUE)),
  factor = sfl(missing = ~ sum(is.na(.))),
  append = FALSE
)

my_skim(iris)
```

|                                                  |      |
|:-------------------------------------------------|:-----|
| Name                                             | iris |
| Number of rows                                   | 150  |
| Number of columns                                | 5    |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |      |
| Column type frequency:                           |      |
| factor                                           | 1    |
| numeric                                          | 4    |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |      |
| Group variables                                  | None |

Data summary {.table}

**Variable type: factor**

| skim_variable | n_missing | complete_rate | missing |
|:--------------|----------:|--------------:|--------:|
| Species       |         0 |             1 |       0 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate | total |
|:--------------|----------:|--------------:|------:|
| Sepal.Length  |         0 |             1 | 876.5 |
| Sepal.Width   |         0 |             1 | 458.6 |
| Petal.Length  |         0 |             1 | 563.7 |
| Petal.Width   |         0 |             1 | 179.9 |

To modify the “base” skimmers, refer to them in a similar manner. Since
base skimmers are usually a small group, they must return the same type
for all data types in R, `append` doesn’t apply here.

``` r

my_skim <- skim_with(base = sfl(length = length))
my_skim(faithful)
```

|                                                  |          |
|:-------------------------------------------------|:---------|
| Name                                             | faithful |
| Number of rows                                   | 272      |
| Number of columns                                | 2        |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |          |
| Column type frequency:                           |          |
| numeric                                          | 2        |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |          |
| Group variables                                  | None     |

Data summary {.table}

**Variable type: numeric**

| skim_variable | length |  mean |    sd |   p0 |   p25 | p50 |   p75 | p100 | hist  |
|:--------------|-------:|------:|------:|-----:|------:|----:|------:|-----:|:------|
| eruptions     |    272 |  3.49 |  1.14 |  1.6 |  2.16 |   4 |  4.45 |  5.1 | ▇▂▂▇▇ |
| waiting       |    272 | 70.90 | 13.59 | 43.0 | 58.00 |  76 | 82.00 | 96.0 | ▃▃▂▇▂ |

## Extending `skimr`

Packages may wish to export their own
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md) functions.
Use
[`skim_with()`](https://docs.ropensci.org/skimr/reference/skim_with.md)
for this. In fact, this is how `skimr` generates its version of
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md).

``` r

#' @export
my_package_skim <- skim_with()
```

Alternatively, defaults for another data types can be added to `skimr`
with the `get_skimmers` generic. The method for your data type should
return an [`sfl()`](https://docs.ropensci.org/skimr/reference/sfl.md).
Unlike the [`sfl()`](https://docs.ropensci.org/skimr/reference/sfl.md)
used interactively, you also need to set the `skim_type` argument. It
should match the method type in the function signature.

``` r

get_skimmers.my_type <- function(column) {
  sfl(
    skim_type = "my_type",
    total = sum
  )
}

my_data <- data.frame(
  my_type = structure(1:3, class = c("my_type", "integer"))
)
skim(my_data)
```

|                                                  |         |
|:-------------------------------------------------|:--------|
| Name                                             | my_data |
| Number of rows                                   | 3       |
| Number of columns                                | 1       |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |         |
| Column type frequency:                           |         |
| my_type                                          | 1       |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |         |
| Group variables                                  | None    |

Data summary {.table}

**Variable type: my_type**

| skim_variable | n_missing | complete_rate | total |
|:--------------|----------:|--------------:|------:|
| my_type       |         0 |             1 |     6 |

An extended example is available in the vignette *Supporting additional
objects*.

## Solutions to common rendering problems

The details of rendering are dependent on the operating system R is
running on, the locale of the installation, and the fonts installed.
Rendering may also differ based on whether it occurs in the console or
when knitting to specific types of documents such as HTML and PDF.

The most commonly reported problems involve rendering the spark graphs
(inline histogram and line chart) on Windows. One common fix is to
switch your locale. The function
[`fix_windows_histograms()`](https://docs.ropensci.org/skimr/reference/fix_windows_histograms.md)
does this for you.

In order to render the sparkgraphs in html or PDF histogram you may need
to change fonts to one that supports blocks or Braille (depending on
which you need). Please review the separate vignette and associated
template for details.
