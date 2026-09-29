# Provide a default printing method for knitr.

Instead of standard R output, `knitr` and `RMarkdown` documents will
have formatted
[`knitr::kable()`](https://rdrr.io/pkg/knitr/man/kable.html) output on
return. You can disable this by setting the chunk option
`render = normal_print`.

## Usage

``` r
# S3 method for class 'skim_df'
knit_print(x, options = NULL, ...)

# S3 method for class 'skim_list'
knit_print(x, options = NULL, ...)

# S3 method for class 'one_skim_df'
knit_print(x, options = NULL, ...)

# S3 method for class 'summary_skim_df'
knit_print(x, options = NULL, ...)
```

## Arguments

- x:

  An R object to be printed

- options:

  Options passed into the print function.

- ...:

  Additional arguments passed to the S3 method. Currently ignored,
  except two optional arguments `options` and `inline`; see the
  references below.

## Value

A `knit_asis` object. Which is used by `knitr` when rendered.

## Details

The summary statistics for the original data frame can be disabled by
setting the `knitr` chunk option `skimr_include_summary = FALSE`. See
[knitr::opts_chunk](https://rdrr.io/pkg/knitr/man/opts_chunk.html) for
more information. You can change the number of digits shown in the
printed table with the `skimr_digits` chunk option.

Alternatively, you can call
[`yank()`](https://docs.ropensci.org/skimr/reference/partition.md) to
get the particular `skim_df` objects and format them however you like.
One warning though. Because histograms contain unicode characters, they
can have unexpected print results, as R as varying levels of unicode
support. This affects Windows users most commonly. Call
[`vignette("Using_fonts")`](https://docs.ropensci.org/skimr/articles/Using_fonts.md)
for more details.

## Methods (by class)

- `knit_print(skim_df)`: Default `knitr` print for `skim_df` objects.

- `knit_print(skim_list)`: Default `knitr` print for a `skim_list`.

- `knit_print(one_skim_df)`: Default `knitr` print within a partitioned
  `skim_df`.

- `knit_print(summary_skim_df)`: Default `knitr` print for `skim_df`
  summaries.

## See also

[`knitr::kable()`](https://rdrr.io/pkg/knitr/man/kable.html)
