# Create "long" skim output

Skim results returned as a tidy long data frame with four columns:
variable, type, stat and formatted.

## Usage

``` r
to_long(.data, ..., skim_fun = skim)

# Default S3 method
to_long(.data, ..., skim_fun = skim)

# S3 method for class 'skim_df'
to_long(.data, ..., skim_fun = skim)
```

## Arguments

- .data:

  A data frame or an object that can be coerced into a data frame.

- ...:

  Columns to select for skimming. When none are provided, the default is
  to skim all columns.

- skim_fun:

  The skim function used.

## Value

A tibble

## Methods (by class)

- `to_long(default)`: Skim a data frame and convert the results to a
  long data frame.

- `to_long(skim_df)`: Transform a skim_df to a long data frame.

## Examples

``` r
to_long(iris)
#> # A tibble: 45 × 4
#>    skim_type skim_variable stat              formatted                
#>    <chr>     <chr>         <chr>             <chr>                    
#>  1 factor    Species       n_missing         0                        
#>  2 factor    Species       complete_rate     1                        
#>  3 factor    Species       factor.ordered    FALSE                    
#>  4 factor    Species       factor.n_unique   3                        
#>  5 factor    Species       factor.top_counts set: 50, ver: 50, vir: 50
#>  6 numeric   Sepal.Length  n_missing         0                        
#>  7 numeric   Sepal.Length  complete_rate     1                        
#>  8 numeric   Sepal.Length  numeric.mean      5.84333333333333         
#>  9 numeric   Sepal.Length  numeric.sd        0.828066127977863        
#> 10 numeric   Sepal.Length  numeric.p0        4.3                      
#> # ℹ 35 more rows
to_long(skim(iris))
#> # A tibble: 45 × 4
#>    skim_type skim_variable stat              formatted                
#>    <chr>     <chr>         <chr>             <chr>                    
#>  1 factor    Species       n_missing         0                        
#>  2 factor    Species       complete_rate     1                        
#>  3 factor    Species       factor.ordered    FALSE                    
#>  4 factor    Species       factor.n_unique   3                        
#>  5 factor    Species       factor.top_counts set: 50, ver: 50, vir: 50
#>  6 numeric   Sepal.Length  n_missing         0                        
#>  7 numeric   Sepal.Length  complete_rate     1                        
#>  8 numeric   Sepal.Length  numeric.mean      5.84333333333333         
#>  9 numeric   Sepal.Length  numeric.sd        0.828066127977863        
#> 10 numeric   Sepal.Length  numeric.p0        4.3                      
#> # ℹ 35 more rows
```
