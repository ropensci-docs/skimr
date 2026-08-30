# Summary statistic functions

`skimr` provides extensions to a variety of functions with R's stats
package to simplify creating summaries of data. All functions are
vectorized over the first argument. Additional arguments should be set
in the [`sfl()`](https://docs.ropensci.org/skimr/reference/sfl.md) that
sets the appropriate skimmers for a data type. You can use these, along
with other vectorized R functions, for creating custom sets of summary
functions for a given data type.

## Usage

``` r
n_missing(x)

n_complete(x)

complete_rate(x)

n_whitespace(x)

sorted_count(x)

top_counts(x, max_char = 3, max_levels = 4)

inline_hist(x, n_bins = 8)

n_empty(x)

min_char(x)

max_char(x)

n_unique(x)

ts_start(x)

ts_end(x)

inline_linegraph(x, length.out = 16)

list_lengths_min(x)

list_lengths_median(x)

list_lengths_max(x)

list_min_length(x)

list_max_length(x)

min_date(x)

max_date(x)
```

## Arguments

- x:

  A vector

- max_char:

  In `top` = 3, max_levels = 4

- max_levels:

  The maximum number of levels to be displayed.

- n_bins:

  In `inline_hist`, the number of histogram bars.

- length.out:

  In `inline_linegraph`, the length of the character time series.

## Functions

- `n_missing()`: Calculate the sum of `NA` and `NULL` (i.e. missing)
  values.

- `n_complete()`: Calculate the sum of not `NA` and `NULL` (i.e.
  missing) values.

- `complete_rate()`: Calculate complete values; complete values are not
  missing.

- `n_whitespace()`: Calculate the number of rows containing only
  whitespace values using s+ regex.

- `sorted_count()`: Create a contingency table and arrange its levels in
  descending order. In case of ties, the ordering of results is
  alphabetical and depends upon the locale. `NA` is treated as a
  ordinary value for sorting.

- `top_counts()`: Compute and collapse a contingency table into a single
  character scalar. Wraps `sorted_count()`.

- `inline_hist()`: Generate inline histogram for numeric variables. The
  character length of the histogram is controlled by the formatting
  options for character vectors.

- `n_empty()`: Calculate the number of blank values in a character
  vector. A "blank" is equal to "".

- `min_char()`: Calculate the minimum number of characters within a
  character vector.

- `max_char()`: Calculate the maximum number of characters within a
  character vector.

- `n_unique()`: Calculate the number of unique elements but remove `NA`.

- `ts_start()`: Get the start for a time series without the frequency.

- `ts_end()`: Get the finish for a time series without the frequency.

- `inline_linegraph()`: Generate inline line graph for time series
  variables. The character length of the line graph is controlled by the
  formatting options for character vectors. Based on the function in the
  pillar package.

- `list_lengths_min()`: Get the length of the shortest list in a vector
  of lists.

- `list_lengths_median()`: Get the median length of the lists.

- `list_lengths_max()`: Get the maximum length of the lists.

- `list_min_length()`: Get the length of the shortest list in a vector
  of lists.

- `list_max_length()`: Get the length of the longest list in a vector of
  lists.

- `min_date()`: Calculate the minimum within a Date vector.

- `max_date()`: Calculate the maximum of Date vector.

## See also

[`get_skimmers()`](https://docs.ropensci.org/skimr/reference/get_skimmers.md)
for customizing the functions called by
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md).
