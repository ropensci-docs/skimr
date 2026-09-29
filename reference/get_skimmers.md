# Retrieve the summary functions for a specific data type

These functions are used to set the default skimming functions for a
data type. They are combined with the base skim function list (`sfl`) in
[`skim_with()`](https://docs.ropensci.org/skimr/reference/skim_with.md),
to create the summary tibble for each type.

## Usage

``` r
get_skimmers(column)

# Default S3 method
get_skimmers(column)

# S3 method for class 'numeric'
get_skimmers(column)

# S3 method for class 'factor'
get_skimmers(column)

# S3 method for class 'character'
get_skimmers(column)

# S3 method for class 'logical'
get_skimmers(column)

# S3 method for class 'complex'
get_skimmers(column)

# S3 method for class 'Date'
get_skimmers(column)

# S3 method for class 'POSIXct'
get_skimmers(column)

# S3 method for class 'difftime'
get_skimmers(column)

# S3 method for class 'Timespan'
get_skimmers(column)

# S3 method for class 'ts'
get_skimmers(column)

# S3 method for class 'list'
get_skimmers(column)

# S3 method for class 'AsIs'
get_skimmers(column)

# S3 method for class 'haven_labelled'
get_skimmers(column)

modify_default_skimmers(skim_type, new_skim_type = NULL, new_funs = list())
```

## Arguments

- column:

  An atomic vector or list. A column from a data frame.

- skim_type:

  A character scalar. The class of the object with default skimmers.

- new_skim_type:

  The type to assign to the looked up set of skimmers.

- new_funs:

  Replacement functions for those in

## Value

A `skim_function_list` object.

## Details

When creating your own set of skimming functions, call
[`sfl()`](https://docs.ropensci.org/skimr/reference/sfl.md) within a
`get_skimmers()` method for your particular type. Your call to
[`sfl()`](https://docs.ropensci.org/skimr/reference/sfl.md) should also
provide a matching class in the `skim_type` argument. Otherwise, it will
not be possible to dynamically reassign your default functions when
working interactively.

Call
[`get_default_skimmers()`](https://docs.ropensci.org/skimr/reference/get_default_skimmers.md)
to see the functions for each type of summary function currently
supported. Call
[`get_default_skimmer_names()`](https://docs.ropensci.org/skimr/reference/get_default_skimmers.md)
to just see the names of these functions. Use
`modify_default_skimmers()` for a method for changing the `skim_type` or
functions for a default `sfl`. This is useful for creating new default
`sfl`'s.

## Methods (by class)

- `get_skimmers(default)`: The default method for skimming data. Only
  used when a column's data type doesn't match currently installed
  types. Call
  [get_default_skimmer_names](https://docs.ropensci.org/skimr/reference/get_default_skimmers.md)
  to see these defaults.

- `get_skimmers(numeric)`: Summary functions for numeric columns,
  covering both [`double()`](https://rdrr.io/r/base/double.html) and
  [`integer()`](https://rdrr.io/r/base/integer.html) classes:
  [`mean()`](https://rdrr.io/r/base/mean.html),
  [`sd()`](https://rdrr.io/r/stats/sd.html),
  [`quantile()`](https://rdrr.io/r/stats/quantile.html) and
  [`inline_hist()`](https://docs.ropensci.org/skimr/reference/stats.md).

- `get_skimmers(factor)`: Summary functions for factor columns:
  [`is.ordered()`](https://rdrr.io/r/base/factor.html),
  [`n_unique()`](https://docs.ropensci.org/skimr/reference/stats.md) and
  [`top_counts()`](https://docs.ropensci.org/skimr/reference/stats.md).

- `get_skimmers(character)`: Summary functions for character columns.
  Also, the default for unknown columns:
  [`min_char()`](https://docs.ropensci.org/skimr/reference/stats.md),
  [`max_char()`](https://docs.ropensci.org/skimr/reference/stats.md),
  [`n_empty()`](https://docs.ropensci.org/skimr/reference/stats.md),
  [`n_unique()`](https://docs.ropensci.org/skimr/reference/stats.md) and
  [`n_whitespace()`](https://docs.ropensci.org/skimr/reference/stats.md).

- `get_skimmers(logical)`: Summary functions for logical/ boolean
  columns: [`mean()`](https://rdrr.io/r/base/mean.html), which produces
  rates for each value, and
  [`top_counts()`](https://docs.ropensci.org/skimr/reference/stats.md).

- `get_skimmers(complex)`: Summary functions for complex columns:
  [`mean()`](https://rdrr.io/r/base/mean.html).

- `get_skimmers(Date)`: Summary functions for `Date` columns:
  [`min()`](https://rdrr.io/r/base/Extremes.html),
  [`max()`](https://rdrr.io/r/base/Extremes.html),
  [`median()`](https://rdrr.io/r/stats/median.html) and
  [`n_unique()`](https://docs.ropensci.org/skimr/reference/stats.md).

- `get_skimmers(POSIXct)`: Summary functions for `POSIXct` columns:
  [`min()`](https://rdrr.io/r/base/Extremes.html),
  [`max()`](https://rdrr.io/r/base/Extremes.html),
  [`median()`](https://rdrr.io/r/stats/median.html) and
  [`n_unique()`](https://docs.ropensci.org/skimr/reference/stats.md).

- `get_skimmers(difftime)`: Summary functions for `difftime` columns:
  [`min()`](https://rdrr.io/r/base/Extremes.html),
  [`max()`](https://rdrr.io/r/base/Extremes.html),
  [`median()`](https://rdrr.io/r/stats/median.html) and
  [`n_unique()`](https://docs.ropensci.org/skimr/reference/stats.md).

- `get_skimmers(Timespan)`: Summary functions for `Timespan` columns:
  [`min()`](https://rdrr.io/r/base/Extremes.html),
  [`max()`](https://rdrr.io/r/base/Extremes.html),
  [`median()`](https://rdrr.io/r/stats/median.html) and
  [`n_unique()`](https://docs.ropensci.org/skimr/reference/stats.md).

- `get_skimmers(ts)`: Summary functions for `ts` columns:
  [`min()`](https://rdrr.io/r/base/Extremes.html),
  [`max()`](https://rdrr.io/r/base/Extremes.html),
  [`median()`](https://rdrr.io/r/stats/median.html) and
  [`n_unique()`](https://docs.ropensci.org/skimr/reference/stats.md).

- `get_skimmers(list)`: Summary functions for `list` columns:
  [`n_unique()`](https://docs.ropensci.org/skimr/reference/stats.md),
  [`list_min_length()`](https://docs.ropensci.org/skimr/reference/stats.md)
  and
  [`list_max_length()`](https://docs.ropensci.org/skimr/reference/stats.md).

- `get_skimmers(AsIs)`: Summary functions for `AsIs` columns:
  [`n_unique()`](https://docs.ropensci.org/skimr/reference/stats.md),
  [`list_min_length()`](https://docs.ropensci.org/skimr/reference/stats.md)
  and
  [`list_max_length()`](https://docs.ropensci.org/skimr/reference/stats.md).

- `get_skimmers(haven_labelled)`: Summary functions for `haven_labelled`
  columns. Finds the appropriate skimmers for the underlying data in the
  vector.

## See also

[`sfl()`](https://docs.ropensci.org/skimr/reference/sfl.md)

## Examples

``` r
# Defining default skimming functions for a new class, `my_class`.
# Note that the class argument is required for dynamic reassignment.
get_skimmers.my_class <- function(column) {
  sfl(
    skim_type = "my_class",
    mean,
    sd
  )
}

# Integer and double columns are both "numeric" and are treated the same
# by default. To switch this behavior in another package, add a method.
get_skimmers.integer <- function(column) {
  sfl(
    skim_type = "integer",
    p50 = ~ stats::quantile(
      .,
      probs = .50, na.rm = TRUE, names = FALSE, type = 1
    )
  )
}
x <- mtcars[c("gear", "carb")]
class(x$carb) <- "integer"
skim(x)
#> ── Data Summary ────────────────────────
#>                            Values
#> Name                       x     
#> Number of rows             32    
#> Number of columns          2     
#> _______________________          
#> Column type frequency:           
#>   numeric                  2     
#> ________________________         
#> Group variables            None  
#> 
#> ── Variable type: numeric ──────────────────────────────────────────────────────
#>   skim_variable n_missing complete_rate mean    sd p0 p25 p50 p75 p100 hist 
#> 1 gear                  0             1 3.69 0.738  3   3   4   4    5 ▇▁▆▁▂
#> 2 carb                  0             1 2.81 1.62   1   2   2   4    8 ▇▂▅▁▁
if (FALSE) { # \dontrun{
# In a package, to revert to the V1 behavior of skimming separately with the
# same functions, assign the numeric `get_skimmers`.
get_skimmers.integer <- skimr::get_skimmers.numeric

# Or, in a local session, use `skim_with` to create a different `skim`.
new_skim <- skim_with(integer = skimr::get_skimmers.numeric())

# To apply a set of skimmers from an old type to a new type
get_skimmers.new_type <- function(column) {
  modify_default_skimmers("old_type", new_skim_type = "new_type")
}
} # }
```
