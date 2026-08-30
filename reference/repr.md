# Skimr printing within Jupyter notebooks

This reproduces printed results in the console. By default Jupyter
kernels render the final object in the cell. We want the version printed
by `skimr` instead of the data that it contains.

## Usage

``` r
# S3 method for class 'skim_df'
repr_text(obj, ...)

# S3 method for class 'skim_list'
repr_text(obj, ...)

# S3 method for class 'one_skim_df'
repr_text(obj, ...)
```

## Arguments

- obj:

  The object to
  [print](https://docs.ropensci.org/skimr/reference/print.md) and then
  return the output.

- ...:

  ignored.

## Value

None. `invisible(NULL)`.
