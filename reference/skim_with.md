# Set or add the summary functions for a particular type of data

While skim is designed around having an opinionated set of defaults, you
can use this function to change the summary statistics that it returns.

## Usage

``` r
skim_with(
  ...,
  base = sfl(n_missing = n_missing, complete_rate = complete_rate),
  append = TRUE
)
```

## Arguments

- ...:

  One or more (`sfl`) `skimmer_function_list` objects, with an argument
  name that matches a particular data type.

- base:

  An `sfl` that sets skimmers for all column types.

- append:

  Whether the provided options should be in addition to the defaults
  already in `skim`. Default is `TRUE`.

## Value

A new [`skim()`](https://docs.ropensci.org/skimr/reference/skim.md)
function. This is callable. See
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md) for more
details.

## Details

`skim_with()` is a closure: a function that returns a new function. This
lets you have several skimming functions in a single R session, but it
also means that you need to assign the return of `skim_with()` before
you can use it.

You assign values within `skim_with` by using the
[`sfl()`](https://docs.ropensci.org/skimr/reference/sfl.md) helper
(`skimr` function list). This helper behaves mostly like
[`dplyr::funs()`](https://dplyr.tidyverse.org/reference/funs.html), but
lets you also identify which skimming functions you want to remove, by
setting them to `NULL`. Assign an `sfl` to each column type that you
wish to modify.

Functions that summarize all data types, and always return the same type
of value, can be assigned to the `base` argument. The default base
skimmers compute the number of missing values
[`n_missing()`](https://docs.ropensci.org/skimr/reference/stats.md) and
the rate of values being complete, i.e. not missing,
[`complete_rate()`](https://docs.ropensci.org/skimr/reference/stats.md).

When `append = TRUE` and local skimmers have names matching the names of
entries in the default `skim_function_list`, the values in the default
list are overwritten. Similarly, if `NULL` values are passed within
[`sfl()`](https://docs.ropensci.org/skimr/reference/sfl.md), these
default skimmers are dropped. Otherwise, if `append = FALSE`, only the
locally-provided skimming functions are used.

Note that `append` only applies to the `typed` skimmers (i.e. non-base).
See
[`get_default_skimmer_names()`](https://docs.ropensci.org/skimr/reference/get_default_skimmers.md)
for a list of defaults.

## Examples

``` r
# Use new functions for numeric functions. If you don't provide a name,
# one will be automatically generated.
my_skim <- skim_with(numeric = sfl(median, mad), append = FALSE)
my_skim(faithful)
#> ── Data Summary ────────────────────────
#>                            Values  
#> Name                       faithful
#> Number of rows             272     
#> Number of columns          2       
#> _______________________            
#> Column type frequency:             
#>   numeric                  2       
#> ________________________           
#> Group variables            None    
#> 
#> ── Variable type: numeric ──────────────────────────────────────────────────────
#>   skim_variable n_missing complete_rate median    mad
#> 1 eruptions             0             1      4  0.951
#> 2 waiting               0             1     76 11.9  

# If you want to remove a particular skimmer, set it to NULL
# This removes the inline histogram
my_skim <- skim_with(numeric = sfl(hist = NULL))
my_skim(faithful)
#> ── Data Summary ────────────────────────
#>                            Values  
#> Name                       faithful
#> Number of rows             272     
#> Number of columns          2       
#> _______________________            
#> Column type frequency:             
#>   numeric                  2       
#> ________________________           
#> Group variables            None    
#> 
#> ── Variable type: numeric ──────────────────────────────────────────────────────
#>   skim_variable n_missing complete_rate  mean    sd   p0   p25 p50   p75 p100
#> 1 eruptions             0             1  3.49  1.14  1.6  2.16   4  4.45  5.1
#> 2 waiting               0             1 70.9  13.6  43   58     76 82    96  

# This works with multiple skimmers. Just match names to overwrite
my_skim <- skim_with(numeric = sfl(iqr = IQR, p25 = NULL, p75 = NULL))
my_skim(faithful)
#> ── Data Summary ────────────────────────
#>                            Values  
#> Name                       faithful
#> Number of rows             272     
#> Number of columns          2       
#> _______________________            
#> Column type frequency:             
#>   numeric                  2       
#> ________________________           
#> Group variables            None    
#> 
#> ── Variable type: numeric ──────────────────────────────────────────────────────
#>   skim_variable n_missing complete_rate  mean    sd   p0 p50 p100 hist    iqr
#> 1 eruptions             0             1  3.49  1.14  1.6   4  5.1 ▇▂▂▇▇  2.29
#> 2 waiting               0             1 70.9  13.6  43    76 96   ▃▃▂▇▂ 24   

# Alternatively, set `append = FALSE` to replace the skimmers of a type.
my_skim <- skim_with(numeric = sfl(mean = mean, sd = sd), append = FALSE)

# Skimmers are unary functions. Partially apply arguments during assigment.
# For example, you might want to remove NA values.
my_skim <- skim_with(numeric = sfl(iqr = ~ IQR(., na.rm = TRUE)))

# Set multiple types of skimmers simultaneously.
my_skim <- skim_with(numeric = sfl(mean), character = sfl(length))

# Or pass the same as a list, unquoting the input.
my_skimmers <- list(numeric = sfl(mean), character = sfl(length))
my_skim <- skim_with(!!!my_skimmers)

# Use the v1 base skimmers instead.
my_skim <- skim_with(base = sfl(
  missing = n_missing,
  complete = n_complete,
  n = length
))

# Remove the base skimmers entirely
my_skim <- skim_with(base = NULL)
```
