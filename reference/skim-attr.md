# Functions for accessing skim_df attributes

These functions simplify access to attributes contained within a
`skim_df`. While all attributes are read-only, being able to extract
this information is useful for different analyses. These functions
should always be preferred over calling base R's attribute functions.

## Usage

``` r
data_rows(object)

data_cols(object)

df_name(object)

dt_key(object)

group_names(object)

base_skimmers(object)

skimmers_used(object)
```

## Arguments

- object:

  A `skim_df` or `skim_list`.

## Value

Data contained within the requested `skimr` attribute.

## Functions

- `data_rows()`: Get the number of rows in the skimmed data frame.

- `data_cols()`: Get the number of columns in the skimmed data frame.

- `df_name()`: Get the name of the skimmed data frame. This is only
  available in contexts where the name can be looked up. This is often
  not the case within a pipeline.

- `dt_key()`: Get the key of the skimmed data.table. This is only
  available in contexts where `data` is of class `data.table`.

- `group_names()`: Get the names of the groups in the original data
  frame. Only available if the data was grouped. Otherwise, `NULL`.

- `base_skimmers()`: Get the names of the base skimming functions used.

- `skimmers_used()`: Get the names of the skimming functions used,
  separated by data type.
