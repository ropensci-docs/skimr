# View default skimmer names and functions

These utility functions look up the currently-available defaults for one
or more `skim_type`'s. They work with all defaults in the `skimr`
package, as well as the defaults in any package that extends `skimr`.
See
[`get_skimmers()`](https://docs.ropensci.org/skimr/reference/get_skimmers.md)
for writing lookup methods for different.

## Usage

``` r
get_default_skimmers(skim_type = NULL)

get_one_default_skimmer(skim_type)

get_default_skimmer_names(skim_type = NULL)

get_one_default_skimmer_names(skim_type)

get_sfl(skim_type)
```

## Arguments

- skim_type:

  The class of the column being skimmed.

## Details

The functions differ in return type and whether or not the result is in
a list. `get_default_skimmers()` and `get_one_default_skimmer()` return
functions. The former returns functions within a typed list, i.e.
`list(numeric = list(...functions...))`.

The functions differ in return type and whether or not the result is in
a list. `get_default_skimmer_names()` and
`get_one_default_skimmer_names()` return a list of character vectors or
a single character vector.

`get_sfl()` returns the skimmer function list for a particular
`skim_type`. It differs from `get_default_skimmers()` in that the
returned `sfl` contains a list of functions and a `skim_type`.

## Functions

- `get_one_default_skimmer()`: Get the functions associated with one
  `skim_type`.

- `get_default_skimmer_names()`: Get the names of the functions used in
  one or more `skim_type`'s.

- `get_one_default_skimmer_names()`: Get the names of the functions used
  in one `skim_type`.

- `get_sfl()`: Get the `sfl` for a `skim_type`.
