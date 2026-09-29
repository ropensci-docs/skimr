# Test if an object is compatible with `skimr`

Objects within `skimr` are identified by a class, but they require
additional attributes and data columns for all operations to succeed.
These checks help ensure this. While they have some application
externally, they are mostly used internally.

## Usage

``` r
has_type_column(object)

has_variable_column(object)

has_skimr_attributes(object)

has_skim_type_attribute(object)

has_skimmers(object)

is_data_frame(object)

is_skim_df(object)

is_one_skim_df(object)

is_skim_list(object)

could_be_skim_df(object)

assert_is_skim_df(object)

assert_is_skim_list(object)

assert_is_one_skim_df(object)
```

## Arguments

- object:

  Any `R` object.

## Details

Most notably, a `skim_df` has columns `skim_type` and `skim_variable`.
And has the following special attributes

- `data_rows`: n rows in the original data

- `data_cols`: original number of columns

- `df_name`: name of the original data frame

- `dt_key`: name of the key if original is a data.table

- `groups`: if there were group variables

- `base_skimmers`: names of functions applied to all skim types

- `skimmers_used`: names of functions used to skim each type

The functions in these checks work like
[`all.equal()`](https://rdrr.io/r/base/all.equal.html). The return
`TRUE` if the check passes, or otherwise notifies why the check failed.
This makes them more useful when throwing errors.

## Functions

- `has_type_column()`: Does the object have the `skim_type` column?

- `has_variable_column()`: Does the object have the `skim_variable`
  column?

- `has_skimr_attributes()`: Does the object have the appropriate `skimr`
  attributes?

- `has_skim_type_attribute()`: Does the object have a `skim_type`
  attribute? This makes it a `one_skim_df`.

- `has_skimmers()`: Does the object have skimmers?

- `is_data_frame()`: Is the object a data frame?

- `is_skim_df()`: Is the object a `skim_df`?

- `is_one_skim_df()`: Is the object a `one_skim_df`? This is similar to
  a `skim_df`, but does not have the `type` column. That is stored as an
  attribute instead.

- `is_skim_list()`: Is the object a `skim_list`?

- `could_be_skim_df()`: Is this a data frame with `skim_variable` and
  `skim_type` columns?

- `assert_is_skim_df()`: Stop if the object is not a `skim_df`.

- `assert_is_skim_list()`: Stop if the object is not a `skim_list`.

- `assert_is_one_skim_df()`: Stop if the object is not a `one_skim_df`.
