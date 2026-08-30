# Print `skim` objects

`skimr` has custom print methods for all supported objects. Default
printing methods for `knitr`/ `rmarkdown` documents is also provided.

## Usage

``` r
# S3 method for class 'skim_df'
print(
  x,
  include_summary = TRUE,
  n = Inf,
  width = Inf,
  summary_rule_width = getOption("skimr_summary_rule_width", default = 40),
  ...
)

# S3 method for class 'skim_list'
print(x, n = Inf, width = Inf, ...)

# S3 method for class 'summary_skim_df'
print(x, .summary_rule_width = 40, ...)
```

## Arguments

- x:

  Object to format or print.

- include_summary:

  Whether a summary of the data frame should be printed

- n:

  Number of rows to show. If `NULL`, the default, will print all rows if
  less than the `print_max`
  [option](https://pillar.r-lib.org/reference/pillar_options.html).
  Otherwise, will print as many rows as specified by the `print_min`
  [option](https://pillar.r-lib.org/reference/pillar_options.html).

- width:

  Width of text output to generate. This defaults to `NULL`, which means
  use the `width`
  [option](https://pillar.r-lib.org/reference/pillar_options.html).

- summary_rule_width:

  Width of Data Summary cli rule, defaults to 40.

- ...:

  Passed on to
  [pillar::tbl_format_setup()](https://pillar.r-lib.org/reference/tbl_format_setup.html)

- .summary_rule_width:

  the width for the main rule above the summary.

## Methods (by class)

- `print(skim_df)`: Print a skimmed data frame (`skim_df` from
  [`skim()`](https://docs.ropensci.org/skimr/reference/skim.md)).

- `print(skim_list)`: Print a `skim_list`, a list of `skim_df` objects.

- `print(summary_skim_df)`: Print method for a `summary_skim_df` object.

## Printing options

For better or for worse, `skimr` often produces more output than can fit
in the standard R console. Fortunately, most modern environments like
RStudio and Jupyter support more than 80 character outputs. Call
`options(width = 90)` to get a better experience with `skimr`.

The print methods in `skimr` wrap those in the
[tibble](https://tibble.tidyverse.org/reference/formatting.html)
package. You can control printing behavior using the same global
options.

## Behavior in `dplyr` pipelines

Printing a `skim_df` requires specific columns that might be dropped
when using
[`dplyr::select()`](https://dplyr.tidyverse.org/reference/select.html)
or
[`dplyr::summarize()`](https://dplyr.tidyverse.org/reference/summarise.html)
on a `skim_df`. In those cases, this method falls back to
[`tibble::print.tbl()`](https://tibble.tidyverse.org/reference/formatting.html).

## Options for controlling print behavior

You can control the width rule line for the printed subtables with an
option: `skimr_table_header_width`.

## See also

[`tibble::trunc_mat()`](https://tibble.tidyverse.org/reference/trunc_mat.html)
For a list of global options for customizing print formatting.
[`cli::num_ansi_colors()`](https://cli.r-lib.org/reference/num_ansi_colors.html)
for the variety of issues that affect tibble's color support.
