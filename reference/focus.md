# Only show a subset of summary statistics after skimming

This function is a variant of
[`dplyr::select()`](https://dplyr.tidyverse.org/reference/select.html)
designed to work with `skim_df` objects. When using `focus()`, `skimr`
metadata columns are kept, and `skimr` print methods are still utilized.
Otherwise, the signature and behavior is identical to
[`dplyr::select()`](https://dplyr.tidyverse.org/reference/select.html).

## Usage

``` r
focus(.data, ...)
```

## Arguments

- .data:

  A `skim_df` object.

- ...:

  One or more unquoted expressions separated by commas. Variable names
  can be used as if they were positions in the data frame, so
  expressions like x:y can be used to select a range of variables.

## Examples

``` r
# Compare
iris |>
  skim() |>
  dplyr::select(n_missing)
#> # A tibble: 5 × 1
#>   n_missing
#>       <int>
#> 1         0
#> 2         0
#> 3         0
#> 4         0
#> 5         0

iris |>
  skim() |>
  focus(n_missing)
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
#>   skim_variable n_missing
#> 1 Species               0
#> 
#> ── Variable type: numeric ──────────────────────────────────────────────────────
#>   skim_variable n_missing
#> 1 Sepal.Length          0
#> 2 Sepal.Width           0
#> 3 Petal.Length          0
#> 4 Petal.Width           0

# This is equivalent to
iris |>
  skim() |>
  dplyr::select(skim_variable, skim_type, n_missing)
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
#>   skim_variable n_missing
#> 1 Species               0
#> 
#> ── Variable type: numeric ──────────────────────────────────────────────────────
#>   skim_variable n_missing
#> 1 Sepal.Length          0
#> 2 Sepal.Width           0
#> 3 Petal.Length          0
#> 4 Petal.Width           0
```
