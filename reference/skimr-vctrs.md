# Functions for working with the vctrs package

These make it clear that we need to use the tibble behavior when
joining, concatenating or casting `skim_df` objects. For a better
discussion, on why this is important and how these functions work, see:
<https://vctrs.r-lib.org/reference/howto-faq-coercion-data-frame.html>.

## Usage

``` r
# S3 method for class 'skim_df.skim_df'
vec_ptype2(x, y, ...)

# S3 method for class 'skim_df.tbl_df'
vec_ptype2(x, y, ...)

# S3 method for class 'tbl_df.skim_df'
vec_ptype2(x, y, ...)

# S3 method for class 'skim_df.skim_df'
vec_cast(x, to, ...)

# S3 method for class 'skim_df.tbl_df'
vec_cast(x, to, ...)

# S3 method for class 'tbl_df.skim_df'
vec_cast(x, to, ...)
```

## Details

`vec_ptype2.*` handles finding common prototypes between `skim_df` and
similar objects. `vec_cast.*` handles casting between objects. Note that
as of `dplyr 1.0.2`,
[`dplyr::bind_rows()`](https://dplyr.tidyverse.org/reference/bind_rows.html)
does not full support combining attributes and
[`vctrs::vec_rbind()`](https://vctrs.r-lib.org/reference/vec_bind.html)
is preferred instead.
