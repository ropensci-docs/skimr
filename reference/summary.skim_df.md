# Summary function for skim_df

This is a method of the generic function
[`summary()`](https://rdrr.io/r/base/summary.html).

## Usage

``` r
# S3 method for class 'skim_df'
summary(object, ...)
```

## Arguments

- object:

  a skim dataframe.

- ...:

  Additional arguments affecting the summary produced. Not used.

## Value

A summary of the skim data frame.

## Examples

``` r
a <- skim(mtcars)
summary(a)
#> ── Data Summary ────────────────────────
#>                            Values
#> Name                       mtcars
#> Number of rows             32    
#> Number of columns          11    
#> _______________________          
#> Column type frequency:           
#>   numeric                  11    
#> ________________________         
#> Group variables            None  
```
