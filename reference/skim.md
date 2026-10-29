# Skim a data frame, getting useful summary statistics

`skim()` is an alternative to
[`summary()`](https://rdrr.io/r/base/summary.html), quickly providing a
broad overview of a data frame. It handles data of all types,
dispatching a different set of summary functions based on the types of
columns in the data frame.

## Usage

``` r
skim(data, ..., .data_name = NULL)

skim_tee(data, ..., skim_fun = skim)

skim_without_charts(data, ..., .data_name = NULL)
```

## Arguments

- data:

  A tibble, or an object that can be coerced into a tibble.

- ...:

  Columns to select for skimming. When none are provided, the default is
  to skim all columns.

- .data_name:

  The name to use for the data. Defaults to the same as data.

- skim_fun:

  The skim function used.

- skim:

  The skimming function to use in `skim_tee()`.

## Value

A `skim_df` object, which also inherits the class(es) of the input data.
In many ways, the object behaves like a
[`tibble::tibble()`](https://tibble.tidyverse.org/reference/tibble.html).

## Details

Each call produces a `skim_df`, which is a fundamentally a tibble with a
special print method. One unusual feature of this data frame is pseudo-
namespace for columns. `skim()` computes statistics by data type, and it
stores them in the data frame as `<type>.<statistic>`. These types are
stripped when printing the results. The "base" skimmers (`n_missing` and
`complete_rate`) are the only columns that don't follow this behavior.
See
[`skim_with()`](https://docs.ropensci.org/skimr/reference/skim_with.md)
for more details on customizing `skim()` and
[`get_default_skimmers()`](https://docs.ropensci.org/skimr/reference/get_default_skimmers.md)
for a list of default functions.

If you just want to see the printed output, call `skim_tee()` instead.
This function returns the original data. `skim_tee()` uses the default
`skim()`, but you can replace it with the `skim` argument.

The data frame produced by `skim` is wide and sparse. To avoid type
coercion `skimr` uses a type namespace for all summary statistics.
Columns for numeric summary statistics all begin `numeric`; for factor
summary statistics begin `factor`; and so on.

See
[`partition()`](https://docs.ropensci.org/skimr/reference/partition.md)
and [`yank()`](https://docs.ropensci.org/skimr/reference/partition.md)
for methods for transforming this wide data frame. The first function
splits it into a list, with each entry corresponding to a data type. The
latter pulls a single subtable for a particular type from the `skim_df`.

`skim()` is designed to operate in pipes and to generally play nicely
with other `tidyverse` functions. This means that you can use
`tidyselect` helpers within `skim` to select or drop specific columns
for summary. You can also further work with a `skim_df` using `dplyr`
functions in a pipeline.

## Customizing skim

`skim()` is an intentionally simple function, with minimal arguments
like [`summary()`](https://rdrr.io/r/base/summary.html). Nonetheless,
this package provides two broad approaches to how you can customize
`skim()`'s behavior. You can customize the functions that are called to
produce summary statistics with
[`skim_with()`](https://docs.ropensci.org/skimr/reference/skim_with.md).

## Unicode rendering

If the rendered examples show unencoded values such as `<U+2587>` you
will need to change your locale to allow proper rendering. Please review
the *Using Skimr* vignette for more information
(`vignette("skimr", package = "skimr")`).

Otherwise, we export `skim_without_charts()` to produce summaries
without the spark graphs. These are the source of the unicode
dependency.

## Examples

``` r
skim(iris)
#> ── Data Summary ────────────────────────
#>                            Values
#> Name                       iris  
#> Number of rows             150   
#> Number of columns          5     
#> _______________________          
#> Column type frequency:           
#>   factor                   1     
#>   numeric                  4     
#> ________________________         
#> Group variables            None  
#> 
#> ── Variable type: factor ───────────────────────────────────────────────────────
#>   skim_variable n_missing complete_rate ordered n_unique
#> 1 Species               0             1 FALSE          3
#>   top_counts               
#> 1 set: 50, ver: 50, vir: 50
#> 
#> ── Variable type: numeric ──────────────────────────────────────────────────────
#>   skim_variable n_missing complete_rate mean    sd  p0 p25  p50 p75 p100 hist 
#> 1 Sepal.Length          0             1 5.84 0.828 4.3 5.1 5.8  6.4  7.9 ▆▇▇▅▂
#> 2 Sepal.Width           0             1 3.06 0.436 2   2.8 3    3.3  4.4 ▁▆▇▂▁
#> 3 Petal.Length          0             1 3.76 1.77  1   1.6 4.35 5.1  6.9 ▇▁▆▇▂
#> 4 Petal.Width           0             1 1.20 0.762 0.1 0.3 1.3  1.8  2.5 ▇▁▇▅▃

# Use tidyselect
skim(iris, Species)
#> ── Data Summary ────────────────────────
#>                            Values
#> Name                       iris  
#> Number of rows             150   
#> Number of columns          5     
#> _______________________          
#> Column type frequency:           
#>   factor                   1     
#> ________________________         
#> Group variables            None  
#> 
#> ── Variable type: factor ───────────────────────────────────────────────────────
#>   skim_variable n_missing complete_rate ordered n_unique
#> 1 Species               0             1 FALSE          3
#>   top_counts               
#> 1 set: 50, ver: 50, vir: 50
skim(iris, starts_with("Sepal"))
#> ── Data Summary ────────────────────────
#>                            Values
#> Name                       iris  
#> Number of rows             150   
#> Number of columns          5     
#> _______________________          
#> Column type frequency:           
#>   numeric                  2     
#> ________________________         
#> Group variables            None  
#> 
#> ── Variable type: numeric ──────────────────────────────────────────────────────
#>   skim_variable n_missing complete_rate mean    sd  p0 p25 p50 p75 p100 hist 
#> 1 Sepal.Length          0             1 5.84 0.828 4.3 5.1 5.8 6.4  7.9 ▆▇▇▅▂
#> 2 Sepal.Width           0             1 3.06 0.436 2   2.8 3   3.3  4.4 ▁▆▇▂▁
skim(iris, where(is.numeric))
#> ── Data Summary ────────────────────────
#>                            Values
#> Name                       iris  
#> Number of rows             150   
#> Number of columns          5     
#> _______________________          
#> Column type frequency:           
#>   numeric                  4     
#> ________________________         
#> Group variables            None  
#> 
#> ── Variable type: numeric ──────────────────────────────────────────────────────
#>   skim_variable n_missing complete_rate mean    sd  p0 p25  p50 p75 p100 hist 
#> 1 Sepal.Length          0             1 5.84 0.828 4.3 5.1 5.8  6.4  7.9 ▆▇▇▅▂
#> 2 Sepal.Width           0             1 3.06 0.436 2   2.8 3    3.3  4.4 ▁▆▇▂▁
#> 3 Petal.Length          0             1 3.76 1.77  1   1.6 4.35 5.1  6.9 ▇▁▆▇▂
#> 4 Petal.Width           0             1 1.20 0.762 0.1 0.3 1.3  1.8  2.5 ▇▁▇▅▃

# Skim also works groupwise
iris |>
  dplyr::group_by(Species) |>
  skim()
#> ── Data Summary ────────────────────────
#>                            Values                      
#> Name                       dplyr::group_by(iris, Spe...
#> Number of rows             150                         
#> Number of columns          5                           
#> _______________________                                
#> Column type frequency:                                 
#>   numeric                  4                           
#> ________________________                               
#> Group variables            Species                     
#> 
#> ── Variable type: numeric ──────────────────────────────────────────────────────
#>    skim_variable Species    n_missing complete_rate  mean    sd  p0  p25  p50
#>  1 Sepal.Length  setosa             0             1 5.01  0.352 4.3 4.8  5   
#>  2 Sepal.Length  versicolor         0             1 5.94  0.516 4.9 5.6  5.9 
#>  3 Sepal.Length  virginica          0             1 6.59  0.636 4.9 6.22 6.5 
#>  4 Sepal.Width   setosa             0             1 3.43  0.379 2.3 3.2  3.4 
#>  5 Sepal.Width   versicolor         0             1 2.77  0.314 2   2.52 2.8 
#>  6 Sepal.Width   virginica          0             1 2.97  0.322 2.2 2.8  3   
#>  7 Petal.Length  setosa             0             1 1.46  0.174 1   1.4  1.5 
#>  8 Petal.Length  versicolor         0             1 4.26  0.470 3   4    4.35
#>  9 Petal.Length  virginica          0             1 5.55  0.552 4.5 5.1  5.55
#> 10 Petal.Width   setosa             0             1 0.246 0.105 0.1 0.2  0.2 
#> 11 Petal.Width   versicolor         0             1 1.33  0.198 1   1.2  1.3 
#> 12 Petal.Width   virginica          0             1 2.03  0.275 1.4 1.8  2   
#>     p75 p100 hist 
#>  1 5.2   5.8 ▃▃▇▅▁
#>  2 6.3   7   ▂▇▆▃▃
#>  3 6.9   7.9 ▁▃▇▃▂
#>  4 3.68  4.4 ▁▃▇▅▂
#>  5 3     3.4 ▁▅▆▇▂
#>  6 3.18  3.8 ▂▆▇▅▁
#>  7 1.58  1.9 ▁▃▇▃▁
#>  8 4.6   5.1 ▂▂▇▇▆
#>  9 5.88  6.9 ▃▇▇▃▂
#> 10 0.3   0.6 ▇▂▂▁▁
#> 11 1.5   1.8 ▅▇▃▆▁
#> 12 2.3   2.5 ▂▇▆▅▇

# Which five numeric columns have the greatest mean value?
# Look in the `numeric.mean` column.
iris |>
  skim() |>
  dplyr::select(numeric.mean) |>
  dplyr::slice_head(n = 5)
#> # A tibble: 5 × 1
#>   numeric.mean
#>          <dbl>
#> 1        NA   
#> 2         5.84
#> 3         3.06
#> 4         3.76
#> 5         1.20

# Which of my columns have missing values? Use the base skimmer n_missing.
iris |>
  skim() |>
  dplyr::filter(n_missing > 0)
#> # A tibble: 0 × 15
#> # ℹ 15 variables: skim_type <chr>, skim_variable <chr>, n_missing <int>,
#> #   complete_rate <dbl>, factor.ordered <lgl>, factor.n_unique <int>,
#> #   factor.top_counts <chr>, numeric.mean <dbl>, numeric.sd <dbl>,
#> #   numeric.p0 <dbl>, numeric.p25 <dbl>, numeric.p50 <dbl>, numeric.p75 <dbl>,
#> #   numeric.p100 <dbl>, numeric.hist <chr>

# Use skim_tee to view the skim results and
# continue using the original data.
chickwts |>
  skim_tee() |>
  dplyr::filter(feed == "sunflower")
#> ── Data Summary ────────────────────────
#>                            Values
#> Name                       data  
#> Number of rows             71    
#> Number of columns          2     
#> _______________________          
#> Column type frequency:           
#>   factor                   1     
#>   numeric                  1     
#> ________________________         
#> Group variables            None  
#> 
#> ── Variable type: factor ───────────────────────────────────────────────────────
#>   skim_variable n_missing complete_rate ordered n_unique
#> 1 feed                  0             1 FALSE          6
#>   top_counts                        
#> 1 soy: 14, cas: 12, lin: 12, sun: 12
#> 
#> ── Variable type: numeric ──────────────────────────────────────────────────────
#>   skim_variable n_missing complete_rate mean   sd  p0  p25 p50  p75 p100 hist 
#> 1 weight                0             1 261. 78.1 108 204. 258 324.  423 ▆▆▇▇▃
#>    weight      feed
#> 1     423 sunflower
#> 2     340 sunflower
#> 3     392 sunflower
#> 4     339 sunflower
#> 5     341 sunflower
#> 6     226 sunflower
#> 7     320 sunflower
#> 8     295 sunflower
#> 9     334 sunflower
#> 10    322 sunflower
#> 11    297 sunflower
#> 12    318 sunflower

# Produce a summary without spark graphs
iris |>
  skim_without_charts()
#> ── Data Summary ────────────────────────
#>                            Values
#> Name                       iris  
#> Number of rows             150   
#> Number of columns          5     
#> _______________________          
#> Column type frequency:           
#>   factor                   1     
#>   numeric                  4     
#> ________________________         
#> Group variables            None  
#> 
#> ── Variable type: factor ───────────────────────────────────────────────────────
#>   skim_variable n_missing complete_rate ordered n_unique
#> 1 Species               0             1 FALSE          3
#>   top_counts               
#> 1 set: 50, ver: 50, vir: 50
#> 
#> ── Variable type: numeric ──────────────────────────────────────────────────────
#>   skim_variable n_missing complete_rate mean    sd  p0 p25  p50 p75 p100
#> 1 Sepal.Length          0             1 5.84 0.828 4.3 5.1 5.8  6.4  7.9
#> 2 Sepal.Width           0             1 3.06 0.436 2   2.8 3    3.3  4.4
#> 3 Petal.Length          0             1 3.76 1.77  1   1.6 4.35 5.1  6.9
#> 4 Petal.Width           0             1 1.20 0.762 0.1 0.3 1.3  1.8  2.5
```
