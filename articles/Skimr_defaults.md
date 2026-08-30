# Skimr defaults

## Introduction

This vignette simply displays the default data types and summary
functions for `skimr`. Customizing `skimr` is explained in the **Using
Skimr** vignette.

## The base skimmers

`skimr` has a group of functions that it applies to all data types. We
call these the “base”” skimmers:

- `n_missing`: The number of missing values in the column.
- `complete_rate`: The ratio of non-missing values to the total values
  in the column.

## Default skimmers

To learn more about the functions used in this package, use the function
[`get_default_skimmer_names()`](https://docs.ropensci.org/skimr/reference/get_default_skimmers.md).

``` r

library(skimr)
get_default_skimmer_names()
```

    ## $AsIs
    ## [1] "n_unique"   "min_length" "max_length"
    ## 
    ## $character
    ## [1] "min"        "max"        "empty"      "n_unique"   "whitespace"
    ## 
    ## $complex
    ## [1] "mean"
    ## 
    ## $Date
    ## [1] "min"      "max"      "median"   "n_unique"
    ## 
    ## $difftime
    ## [1] "min"      "max"      "median"   "n_unique"
    ## 
    ## $factor
    ## [1] "ordered"    "n_unique"   "top_counts"
    ## 
    ## $haven_labelled
    ## [1] "mean" "sd"   "p0"   "p25"  "p50"  "p75"  "p100" "hist"
    ## 
    ## $list
    ## [1] "n_unique"   "min_length" "max_length"
    ## 
    ## $logical
    ## [1] "mean"  "count"
    ## 
    ## $numeric
    ## [1] "mean" "sd"   "p0"   "p25"  "p50"  "p75"  "p100" "hist"
    ## 
    ## $POSIXct
    ## [1] "min"      "max"      "median"   "n_unique"
    ## 
    ## $Timespan
    ## [1] "min"      "max"      "median"   "n_unique"
    ## 
    ## $ts
    ##  [1] "start"      "end"        "frequency"  "deltat"     "mean"      
    ##  [6] "sd"         "min"        "max"        "median"     "line_graph"

The counterpart to this function is
[`get_default_skimmers()`](https://docs.ropensci.org/skimr/reference/get_default_skimmers.md),
which returns the functions themselves. If you are interested in a
particular class within `skimr`, pass it as a string to either function.

``` r

get_default_skimmer_names("numeric")
```

    ## $numeric
    ## [1] "mean" "sd"   "p0"   "p25"  "p50"  "p75"  "p100" "hist"

The same information is stored in the `skimmers_used` attribute of the
object returned by
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md).
