# Create a skimr function list

This constructor is used to create a named list of functions. It also
you also pass `NULL` to identify a skimming function that you wish to
remove. Only functions that return a single value, working with
[`dplyr::summarize()`](https://dplyr.tidyverse.org/reference/summarise.html),
can be used within `sfl`.

## Usage

``` r
sfl(..., skim_type = "")
```

## Arguments

- ...:

  Inherited from dplyr::data_masking() for dplyr version 1 or later or
  dplyr::funs() for older versions of dplyr. A list of functions
  specified by:

  - Their name, `"mean"`

  - The function itself, `mean`

  - A call to the function with `.` as a dummy argument,
    `mean(., na.rm = TRUE)`

  - An anonymous function in purrr notation, `~mean(., na.rm = TRUE)`

- skim_type:

  A character scalar. This is used to match locally-provided skimmers
  with defaults. See
  [`get_skimmers()`](https://docs.ropensci.org/skimr/reference/get_skimmers.md)
  for more detail.

## Value

A `skimr_function_list`, which contains a list of `fun_calls`, returned
by [`dplyr::funs()`](https://dplyr.tidyverse.org/reference/funs.html)
and a list of skimming functions to drop.

## Details

`sfl()` will automatically generate callables and names for a variety of
inputs, including functions, formulas and strings. Nonetheless, we
recommend providing names when reasonable to get better
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md) output.

## See also

[`dplyr::funs()`](https://dplyr.tidyverse.org/reference/funs.html),
[`skim_with()`](https://docs.ropensci.org/skimr/reference/skim_with.md)
and
[`get_skimmers()`](https://docs.ropensci.org/skimr/reference/get_skimmers.md).

## Examples

``` r
# sfl's can take a variety of input formats and will generate names
# if not provided.
sfl(mad, "var", ~ length(.)^2)
#> $funs
#> $funs$mad
#> function (x, center = median(x), constant = 1.4826, na.rm = FALSE, 
#>     low = FALSE, high = FALSE) 
#> {
#>     if (na.rm) 
#>         x <- x[!is.na(x)]
#>     n <- length(x)
#>     constant * if ((low || high) && n%%2 == 0) {
#>         if (low && high) 
#>             stop("'low' and 'high' cannot be both TRUE")
#>         n2 <- n%/%2 + as.integer(high)
#>         sort(abs(x - center), partial = n2)[n2]
#>     }
#>     else median(abs(x - center))
#> }
#> <bytecode: 0x55cd10377470>
#> <environment: namespace:stats>
#> 
#> $funs$`"var"`
#> [1] "var"
#> 
#> $funs$`~length(.)^2`
#> ~length(.)^2
#> <environment: 0x55cd0f9efb48>
#> 
#> 
#> $skim_type
#> [1] ""
#> 
#> attr(,"class")
#> [1] "skimr_function_list"

# But these can generate unpredictable names in your output.
# Better to set your own names.
sfl(mad = mad, variance = "var", length_sq = ~ length(.)^2)
#> $funs
#> $funs$mad
#> function (x, center = median(x), constant = 1.4826, na.rm = FALSE, 
#>     low = FALSE, high = FALSE) 
#> {
#>     if (na.rm) 
#>         x <- x[!is.na(x)]
#>     n <- length(x)
#>     constant * if ((low || high) && n%%2 == 0) {
#>         if (low && high) 
#>             stop("'low' and 'high' cannot be both TRUE")
#>         n2 <- n%/%2 + as.integer(high)
#>         sort(abs(x - center), partial = n2)[n2]
#>     }
#>     else median(abs(x - center))
#> }
#> <bytecode: 0x55cd10377470>
#> <environment: namespace:stats>
#> 
#> $funs$variance
#> [1] "var"
#> 
#> $funs$length_sq
#> ~length(.)^2
#> <environment: 0x55cd0f9efb48>
#> 
#> 
#> $skim_type
#> [1] ""
#> 
#> attr(,"class")
#> [1] "skimr_function_list"

# sfl's can remove individual skimmers from defaults by passing NULL.
sfl(hist = NULL)
#> $funs
#> $funs$hist
#> NULL
#> 
#> 
#> $skim_type
#> [1] ""
#> 
#> attr(,"class")
#> [1] "skimr_function_list"

# When working interactively, you don't need to set a type.
# But you should when defining new defaults with `get_skimmers()`.
get_skimmers.my_new_class <- function(column) {
  sfl(n_missing, skim_type = "my_new_class")
}
```
