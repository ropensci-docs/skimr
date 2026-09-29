# Deprecated functions from skimr v1

Skimr used to offer functions that combined skimming with a secondary
effect, like reshaping the data, building a list or printing the
results. Some of these behaviors are no longer necessary.
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md) always
returns a wide data frame. Others have been replaced by functions that
do a single thing.
[`partition()`](https://docs.ropensci.org/skimr/reference/partition.md)
creates a list-like object from a skimmed data frame.

## Usage

``` r
skim_to_wide(.data, ...)

skim_to_list(.data, ...)

skim_format(...)
```

## Arguments

- .data:

  A tibble, or an object that can be coerced into a tibble.

- ...:

  Columns to select for skimming. When none are provided, the default is
  to skim all columns.

## Value

Either A `skim_df` or a `skim_list` object.

## Functions

- `skim_to_wide()`:
  [`skim()`](https://docs.ropensci.org/skimr/reference/skim.md) always
  produces a wide data frame.

- `skim_to_list()`:
  [`partition()`](https://docs.ropensci.org/skimr/reference/partition.md)
  creates a list.

- `skim_format()`:
  [`print()`](https://docs.ropensci.org/skimr/reference/print.md) and
  [`skim_with()`](https://docs.ropensci.org/skimr/reference/skim_with.md)
  set options.
