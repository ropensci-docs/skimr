# Separate a big `skim_df` into smaller data frames, by type.

The data frames produced by
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md) are wide
and sparse, filled with columns that are mostly `NA`. For that reason,
it can be convenient to work with "by type" subsets of the original data
frame. These smaller subsets have their `NA` columns removed.

## Usage

``` r
partition(data)

bind(data)

yank(data, skim_type)
```

## Arguments

- data:

  A `skim_df`.

- skim_type:

  A character scalar. The subtable to extract from a `skim_df`.

## Value

A `skim_list` of `skim_df`'s, by type.

## Details

`partition()` creates a list of smaller `skim_df` data frames. Each
entry in the list is a data type from the original `skim_df`. The
inverse of `partition()` is `bind()`, which takes the list and produces
the original `skim_df`. While `partition()` keeps all of the subtables
as list entries, `yank()` gives you a single subtable for a data type.

## Functions

- `bind()`: The inverse of a `partition()`. Rebuild the original
  `skim_df`.

- `yank()`: Extract a subtable from a `skim_df` with a particular type.

## Examples

``` r
# Create a wide skimmed data frame (a skim_df)
skimmed <- skim(iris)

# Separate into a list of subtables by type
separate <- partition(skimmed)

# Put back together
identical(bind(separate), skimmed)
#> [1] TRUE
# > TRUE

# Alternatively, get the subtable of a particular type
yank(skimmed, "factor")
#> 
#> ── Variable type: factor ───────────────────────────────────────────────────────
#>   skim_variable n_missing complete_rate ordered n_unique top_counts             
#> 1 Species               0             1 FALSE          3 set: 50, ver: 50, vir:…
```
