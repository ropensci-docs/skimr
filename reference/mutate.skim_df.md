# Mutate a skim_df

[`dplyr::mutate()`](https://dplyr.tidyverse.org/reference/mutate.html)
currently drops attributes, but we need to keep them around for other
skim behaviors. Otherwise the behavior is exactly the same. For more
information, see <https://github.com/tidyverse/dplyr/issues/3429>.

## Usage

``` r
# S3 method for class 'skim_df'
mutate(.data, ...)
```

## Arguments

- .data:

  A `skim_df`, which behaves like a `tbl.`

- ...:

  Name-value pairs of expressions, each with length 1 or the same length
  as the number of rows in the group, if using
  [`dplyr::group_by()`](https://dplyr.tidyverse.org/reference/group_by.html),
  or in the entire input (if not using groups). The name of each
  argument will be the name of a new variable, and the value will be its
  corresponding value. Use `NULL` value in
  [`dplyr::mutate()`](https://dplyr.tidyverse.org/reference/mutate.html)
  to drop a variable. New variables overwrite existing variables of the
  same name.

  The arguments in `...` are automatically quoted with
  [`rlang::quo()`](https://rlang.r-lib.org/reference/defusing-advanced.html)
  and evaluated with
  [`rlang::eval_tidy()`](https://rlang.r-lib.org/reference/eval_tidy.html)
  in the context of the data frame. They support unquoting
  [rlang::quasiquotation](https://rlang.r-lib.org/reference/topic-inject.html)
  and splicing. See
  [`vignette("programming", package = "dplyr")`](https://dplyr.tidyverse.org/articles/programming.html)
  for an introduction to these concepts.

## Value

A `skim_df` object, which also inherits the class(es) of the input data.
In many ways, the object behaves like a
[`tibble::tibble()`](https://tibble.tidyverse.org/reference/tibble.html).

## See also

[`dplyr::mutate()`](https://dplyr.tidyverse.org/reference/mutate.html)
for the function's expected behavior.
