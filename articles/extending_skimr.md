# Extending skimr

### Introduction

The [`skim()`](https://docs.ropensci.org/skimr/reference/skim.md)
function summarizes data types contained within data frames and objects
that have [`as.data.frame()`](https://rdrr.io/r/base/as.data.frame.html)
methods to coerce them into data frames. It comes with a set of default
summary functions for a wide variety of data types, but this is not
comprehensive.

Package authors (and advanced users) can add support for skimming their
specific non-data-frame objects in their packages, and they can provide
different defaults in their own summary functions. This will require
including skimr as a dependency.

## Skimming objects that are not coercible to data frames

This example will illustrate this by creating support for the `lm`
object produced by [`lm()`](https://rdrr.io/r/stats/lm.html). For any
object this involves two required elements and one optional element.
This is a simple example, but for other types of objects there may be
much more complexity

If you are adding skim support to a package you will also need to add
`skimr` to the list of imports.

``` r

library(skimr)
```

The [`lm()`](https://rdrr.io/r/stats/lm.html) function produces a
complex object with class “lm”.

``` r

results <- lm(weight ~ feed, data = chickwts)
class(results)
```

    ## [1] "lm"

``` r

attributes(results)
```

    ## $names
    ##  [1] "coefficients"  "residuals"     "effects"       "rank"         
    ##  [5] "fitted.values" "assign"        "qr"            "df.residual"  
    ##  [9] "contrasts"     "xlevels"       "call"          "terms"        
    ## [13] "model"        
    ## 
    ## $class
    ## [1] "lm"

There is no as.data.frame method for an `lm` object.

``` r

as.data.frame(results)
#> Error in as.data.frame.default(results) :
#>  cannot coerce class ‘"lm"’ to a data.frame
```

Unlike the example of having a new type of data in a column of a simple
data frame (for which we would create a `sfl`) frame in the “Using
skimr” vignette, this is a different type of challenge: an object that
we might wish to skim, but that cannot be directly skimmed. Therefore we
need to make it into an object that is either a data frame or coercible
to a data frame.

In the case of the lm object, the `model` attribute is already a data
frame. So a very simple way to solve the challenge is to skim
`results$model` directly.

``` r

skim(results$model)
```

|                                                  |                |
|:-------------------------------------------------|:---------------|
| Name                                             | results\$model |
| Number of rows                                   | 71             |
| Number of columns                                | 2              |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |                |
| Column type frequency:                           |                |
| factor                                           | 1              |
| numeric                                          | 1              |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |                |
| Group variables                                  | None           |

Data summary {.table}

**Variable type: factor**

| skim_variable | n_missing | complete_rate | ordered | n_unique | top_counts |
|:---|---:|---:|:---|---:|:---|
| feed | 0 | 1 | FALSE | 6 | soy: 14, cas: 12, lin: 12, sun: 12 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate |   mean |    sd |  p0 |   p25 | p50 |   p75 | p100 | hist  |
|:--------------|----------:|--------------:|-------:|------:|----:|------:|----:|------:|-----:|:------|
| weight        |         0 |             1 | 261.31 | 78.07 | 108 | 204.5 | 258 | 323.5 |  423 | ▆▆▇▇▃ |

This is works, but we could go one step further and create a new
function for doing this directly.

``` r

skim_lm <- function(.data) {
  .data <- .data$model
  skimr::skim(.data)
}

lm(weight ~ feed, data = chickwts) |> skim_lm()
```

|                                                  |            |
|:-------------------------------------------------|:-----------|
| Name                                             | Piped data |
| Number of rows                                   | 71         |
| Number of columns                                | 2          |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |            |
| Column type frequency:                           |            |
| factor                                           | 1          |
| numeric                                          | 1          |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |            |
| Group variables                                  | None       |

Data summary {.table}

**Variable type: factor**

| skim_variable | n_missing | complete_rate | ordered | n_unique | top_counts |
|:---|---:|---:|:---|---:|:---|
| feed | 0 | 1 | FALSE | 6 | soy: 14, cas: 12, lin: 12, sun: 12 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate |   mean |    sd |  p0 |   p25 | p50 |   p75 | p100 | hist  |
|:--------------|----------:|--------------:|-------:|------:|----:|------:|----:|------:|-----:|:------|
| weight        |         0 |             1 | 261.31 | 78.07 | 108 | 204.5 | 258 | 323.5 |  423 | ▆▆▇▇▃ |

If desired, a more complex function can be created. For example, the lm
object also contains fitted values and residuals. We could incorporate
these in the data frame.

``` r

skim_lm <- function(.data, fit = FALSE) {
  .data <- .data$model
  if (fit) {
    .data <- .data |>
      dplyr::bind_cols(
        fitted = data.frame(results$fitted.values),
        residuals = data.frame(results$residuals)
      )
  }
  skimr::skim(.data)
}
```

``` r

skim_lm(results, fit = TRUE)
```

|                                                  |            |
|:-------------------------------------------------|:-----------|
| Name                                             | Piped data |
| Number of rows                                   | 71         |
| Number of columns                                | 4          |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |            |
| Column type frequency:                           |            |
| factor                                           | 1          |
| numeric                                          | 3          |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |            |
| Group variables                                  | None       |

Data summary {.table}

**Variable type: factor**

| skim_variable | n_missing | complete_rate | ordered | n_unique | top_counts |
|:---|---:|---:|:---|---:|:---|
| feed | 0 | 1 | FALSE | 6 | soy: 14, cas: 12, lin: 12, sun: 12 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate | mean | sd | p0 | p25 | p50 | p75 | p100 | hist |
|:---|---:|---:|---:|---:|---:|---:|---:|---:|---:|:---|
| weight | 0 | 1 | 261.31 | 78.07 | 108.00 | 204.50 | 258.00 | 323.50 | 423.00 | ▆▆▇▇▃ |
| results.fitted.values | 0 | 1 | 261.31 | 57.46 | 160.20 | 218.75 | 246.43 | 323.58 | 328.92 | ▃▃▅▃▇ |
| results.residuals | 0 | 1 | 0.00 | 52.86 | -123.91 | -34.41 | 1.57 | 38.17 | 103.09 | ▂▅▇▅▃ |

A second example of the need for a special function is with `dist`
objects. The `UScitiesD` data set is an example of this.

``` r

class(UScitiesD)
```

    ## [1] "dist"

``` r

UScitiesD
```

    ##               Atlanta Chicago Denver Houston LosAngeles Miami NewYork
    ## Chicago           587                                                
    ## Denver           1212     920                                        
    ## Houston           701     940    879                                 
    ## LosAngeles       1936    1745    831    1374                         
    ## Miami             604    1188   1726     968       2339              
    ## NewYork           748     713   1631    1420       2451  1092        
    ## SanFrancisco     2139    1858    949    1645        347  2594    2571
    ## Seattle          2182    1737   1021    1891        959  2734    2408
    ## Washington.DC     543     597   1494    1220       2300   923     205
    ##               SanFrancisco Seattle
    ## Chicago                           
    ## Denver                            
    ## Houston                           
    ## LosAngeles                        
    ## Miami                             
    ## NewYork                           
    ## SanFrancisco                      
    ## Seattle                678        
    ## Washington.DC         2442    2329

A `dist` object is most often, as in this case, lower triange matrices
of distances, which can be measured in various ways. There are many
packages that produce dist objects and/or take dist objects as inputs,
including those for cluster analysis and multidimensional scaling.

A simple solution to this is to follow a similar design to that for `lm`
objects.

``` r

skim_dist <- function(.data) {
  .data <- data.frame(as.matrix(.data))
  skimr::skim(.data)
}
```

However, this has the limitation of treating the dist data as though it
is simple numeric data.

What we might want to do, instead, is to create a new class, for
example, “distance” that is specifically for distance data. This will
allow it to have its own `sfl` or skimr function list.

As handling gets more complex, rather than make a new function it can be
more powerful to define an `as.data.frame` S3 method for dist objects,
which will allow it to integrate with skimr more completely and uses to
use the [`skim()`](https://docs.ropensci.org/skimr/reference/skim.md)
function directly. In a package you will want to export this.

``` r

as.data.frame.dist <- function(.data) {
  .data <- data.frame(as.matrix(.data))

  .data[] <- lapply(.data, structure, class = "distance", nms = names(.data))
  .data
}

as.data.frame(UScitiesD)
```

    ##               Atlanta Chicago Denver Houston LosAngeles Miami NewYork
    ## Atlanta             0     587   1212     701       1936   604     748
    ## Chicago           587       0    920     940       1745  1188     713
    ## Denver           1212     920      0     879        831  1726    1631
    ## Houston           701     940    879       0       1374   968    1420
    ## LosAngeles       1936    1745    831    1374          0  2339    2451
    ## Miami             604    1188   1726     968       2339     0    1092
    ## NewYork           748     713   1631    1420       2451  1092       0
    ## SanFrancisco     2139    1858    949    1645        347  2594    2571
    ## Seattle          2182    1737   1021    1891        959  2734    2408
    ## Washington.DC     543     597   1494    1220       2300   923     205
    ##               SanFrancisco Seattle Washington.DC
    ## Atlanta               2139    2182           543
    ## Chicago               1858    1737           597
    ## Denver                 949    1021          1494
    ## Houston               1645    1891          1220
    ## LosAngeles             347     959          2300
    ## Miami                 2594    2734           923
    ## NewYork               2571    2408           205
    ## SanFrancisco             0     678          2442
    ## Seattle                678       0          2329
    ## Washington.DC         2442    2329             0

However, until an `sfl` is created, `skimr` will not recognize the class
and fall back to treating the data as if it were character data.

``` r

skim(UScitiesD)
```

    ## Warning: Couldn't find skimmers for class: distance; No user-defined `sfl`
    ## provided. Falling back to `character`.
    ## Warning: Couldn't find skimmers for class: distance; No user-defined `sfl`
    ## provided. Falling back to `character`.
    ## Warning: Couldn't find skimmers for class: distance; No user-defined `sfl`
    ## provided. Falling back to `character`.
    ## Warning: Couldn't find skimmers for class: distance; No user-defined `sfl`
    ## provided. Falling back to `character`.
    ## Warning: Couldn't find skimmers for class: distance; No user-defined `sfl`
    ## provided. Falling back to `character`.
    ## Warning: Couldn't find skimmers for class: distance; No user-defined `sfl`
    ## provided. Falling back to `character`.
    ## Warning: Couldn't find skimmers for class: distance; No user-defined `sfl`
    ## provided. Falling back to `character`.
    ## Warning: Couldn't find skimmers for class: distance; No user-defined `sfl`
    ## provided. Falling back to `character`.
    ## Warning: Couldn't find skimmers for class: distance; No user-defined `sfl`
    ## provided. Falling back to `character`.
    ## Warning: Couldn't find skimmers for class: distance; No user-defined `sfl`
    ## provided. Falling back to `character`.

|                                                  |           |
|:-------------------------------------------------|:----------|
| Name                                             | UScitiesD |
| Number of rows                                   | 10        |
| Number of columns                                | 10        |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |           |
| Column type frequency:                           |           |
| character                                        | 10        |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |           |
| Group variables                                  | None      |

Data summary {.table}

**Variable type: character**

| skim_variable | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:--------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| Atlanta       |         0 |             1 |   1 |   4 |     0 |       10 |          0 |
| Chicago       |         0 |             1 |   1 |   4 |     0 |       10 |          0 |
| Denver        |         0 |             1 |   1 |   4 |     0 |       10 |          0 |
| Houston       |         0 |             1 |   1 |   4 |     0 |       10 |          0 |
| LosAngeles    |         0 |             1 |   1 |   4 |     0 |       10 |          0 |
| Miami         |         0 |             1 |   1 |   4 |     0 |       10 |          0 |
| NewYork       |         0 |             1 |   1 |   4 |     0 |       10 |          0 |
| SanFrancisco  |         0 |             1 |   1 |   4 |     0 |       10 |          0 |
| Seattle       |         0 |             1 |   1 |   4 |     0 |       10 |          0 |
| Washington.DC |         0 |             1 |   1 |   4 |     0 |       10 |          0 |

The solution to this is to define an `sfl` (skimr function list)
specifically for the `distance` class.

### Defining sfl’s for a package

`skimr` has an opinionated list of functions for each class
(e.g. numeric, factor) of data. The core package supports many commonly
used classes, but there are many others. You can investigate these
defaults by calling
[`get_default_skimmer_names()`](https://docs.ropensci.org/skimr/reference/get_default_skimmers.md).

What if your data type, like `distance`, isn’t covered by defaults?
`skimr` usually falls back to treating the type as a character, which
isn’t necessarily helpful. In this case, you’re best off adding your
data type with
[`skim_with()`](https://docs.ropensci.org/skimr/reference/skim_with.md).

Before we begin, we’ll be using the following custom summary statistics
throughout. These functions find the nearest and furthest other location
for each location.

One thing that is important to be aware of when creating statistics
functions for skimr is that skimr largely uses tibbles rather than base
data frames. This means that many base operations do not work as
expected.

``` r

get_nearest <- function(column) {
  closest <- which.min(column[column != 0])
  cities <- attr(column, "nms")[column != 0]
  toString(cities[closest])
}

get_furthest <- function(column) {
  furthest <- which.max(column[column != 0])
  cities <- attr(column, "nms")[column != 0]
  toString(cities[furthest])
}
```

This function, like all summary functions used by `skimr` has two
notable features.

- It accepts a vector as its single argument
- it returns a scalar, or in R terminology, a vector of length 1.

There are a lot of functions that fulfill these criteria:

- Existing functions from base, stats, or other packages,
- lambda’s created using the Tidyverse-style syntax
- custom functions that have been defined in the `skimr` package
- custom functions that you have defined.

Not fulfilling the two criteria can lead to some very confusing behavior
within `skimr`. Beware! An example of this issue is the base
[`quantile()`](https://rdrr.io/r/stats/quantile.html) function in
default `skimr` percentiles are returned by using
[`quantile()`](https://rdrr.io/r/stats/quantile.html) five times. In the
case of these functions, there could be ties which would result in
returning vectors that have length greater than 1. This is handled by
collapsing all of the tied values into a single string.

Notice, also, that in the case of distance data we may wish to exclude
distances of 0, which indicate the distance from a place to itself. In
finding the minimum our function looks only at the distance to other
places.

There are at least two ways that you might want to customize skimr
handling of a special data type within a package or your own work. The
first is to create a custom skimming function.

``` r

skim_with_dist <- skim_with(
  distance = sfl(
    nearest = get_nearest,
    furthest = get_furthest
  )
)
```

    ## Creating new skimming functions for the following classes: distance.
    ## They did not have recognized defaults. Call get_default_skimmers() for more information.

``` r

skim_with_dist(UScitiesD)
```

|                                                  |           |
|:-------------------------------------------------|:----------|
| Name                                             | UScitiesD |
| Number of rows                                   | 10        |
| Number of columns                                | 10        |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |           |
| Column type frequency:                           |           |
| distance                                         | 10        |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |           |
| Group variables                                  | None      |

Data summary {.table}

**Variable type: distance**

| skim_variable | n_missing | complete_rate | nearest       | furthest     |
|:--------------|----------:|--------------:|:--------------|:-------------|
| Atlanta       |         0 |             1 | Washington.DC | Seattle      |
| Chicago       |         0 |             1 | Atlanta       | SanFrancisco |
| Denver        |         0 |             1 | LosAngeles    | Miami        |
| Houston       |         0 |             1 | Atlanta       | Seattle      |
| LosAngeles    |         0 |             1 | SanFrancisco  | NewYork      |
| Miami         |         0 |             1 | Atlanta       | Seattle      |
| NewYork       |         0 |             1 | Washington.DC | SanFrancisco |
| SanFrancisco  |         0 |             1 | LosAngeles    | Miami        |
| Seattle       |         0 |             1 | SanFrancisco  | Miami        |
| Washington.DC |         0 |             1 | NewYork       | SanFrancisco |

The example above creates a new *function*, and you can call that
function on a specific column with `distance` data to get the
appropriate summary statistics. The `skim_with` factory also uses the
default skimrs for things like factors, characters, and numerics.
Therefore our `skim_with_dist` is like the regular `skim` function with
the added ability to summarize `distance` columns.

While this works for any data type and you can also include it within
any package (assuming your users load skimr), there is a second, even
better, approach. To take full advantage of `skimr`, we’ll dig a bit
into its API.

### Adding new methods

`skimr` has a lookup mechanism, based on the function
[`get_skimmers()`](https://docs.ropensci.org/skimr/reference/get_skimmers.md),
to find default summary functions for each class. This is based on the
S3 class system. You can learn more about it in [*Advanced
R*](https://adv-r.hadley.nz/s3.html).

This requires that you add `skimr` to your list of dependencies.

To export a new set of defaults for a data type, create a method for the
generic function `get_skimmers`. Each of those methods returns an `sfl`
(skimr function list) This is the same list-like data structure used in
the
[`skim_with()`](https://docs.ropensci.org/skimr/reference/skim_with.md)
example above. But note! There is one key difference. When adding a
generic we also want to identify the `skim_type` in the `sfl`. You will
probably want to use `skimr::get_skimmers.distance()` but that will not
work in a vignette.

In a package you will want to export this.

``` r

#' @importFrom skimr get_skimmers
#' @export
get_skimmers.distance <- function(column) {
  sfl(
    skim_type = "distance",
    nearest = get_nearest,
    furthest = get_furthest
  )
}
```

The same strategy follows for other data types.

- Create a method
- return an `sfl`
- make sure that the `skim_type` is included.

Users of your package should load `skimr` to get the
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md) function
(although you could import and reexport it). Once loaded, a call to
[`get_default_skimmer_names()`](https://docs.ropensci.org/skimr/reference/get_default_skimmers.md)
will return defaults for your data types as well!

``` r

get_default_skimmer_names()
```

    ## $AsIs
    ## [1] "n_unique"   "min_length" "max_length"
    ## 
    ## $character
    ## [1] "min"        "max"        "empty"      "n_unique"   "whitespace"
    ## 
    ## $complex
    ## [1] "mean"
    ## 
    ## $Date
    ## [1] "min"      "max"      "median"   "n_unique"
    ## 
    ## $difftime
    ## [1] "min"      "max"      "median"   "n_unique"
    ## 
    ## $distance
    ## [1] "nearest"  "furthest"
    ## 
    ## $factor
    ## [1] "ordered"    "n_unique"   "top_counts"
    ## 
    ## $haven_labelled
    ## [1] "mean" "sd"   "p0"   "p25"  "p50"  "p75"  "p100" "hist"
    ## 
    ## $list
    ## [1] "n_unique"   "min_length" "max_length"
    ## 
    ## $logical
    ## [1] "mean"  "count"
    ## 
    ## $numeric
    ## [1] "mean" "sd"   "p0"   "p25"  "p50"  "p75"  "p100" "hist"
    ## 
    ## $POSIXct
    ## [1] "min"      "max"      "median"   "n_unique"
    ## 
    ## $Timespan
    ## [1] "min"      "max"      "median"   "n_unique"
    ## 
    ## $ts
    ##  [1] "start"      "end"        "frequency"  "deltat"     "mean"      
    ##  [6] "sd"         "min"        "max"        "median"     "line_graph"

They will then be able to use
[`skim()`](https://docs.ropensci.org/skimr/reference/skim.md) directly.

``` r

skim(UScitiesD)
```

|                                                  |           |
|:-------------------------------------------------|:----------|
| Name                                             | UScitiesD |
| Number of rows                                   | 10        |
| Number of columns                                | 10        |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |           |
| Column type frequency:                           |           |
| distance                                         | 10        |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |           |
| Group variables                                  | None      |

Data summary {.table}

**Variable type: distance**

| skim_variable | n_missing | complete_rate | nearest       | furthest     |
|:--------------|----------:|--------------:|:--------------|:-------------|
| Atlanta       |         0 |             1 | Washington.DC | Seattle      |
| Chicago       |         0 |             1 | Atlanta       | SanFrancisco |
| Denver        |         0 |             1 | LosAngeles    | Miami        |
| Houston       |         0 |             1 | Atlanta       | Seattle      |
| LosAngeles    |         0 |             1 | SanFrancisco  | NewYork      |
| Miami         |         0 |             1 | Atlanta       | Seattle      |
| NewYork       |         0 |             1 | Washington.DC | SanFrancisco |
| SanFrancisco  |         0 |             1 | LosAngeles    | Miami        |
| Seattle       |         0 |             1 | SanFrancisco  | Miami        |
| Washington.DC |         0 |             1 | NewYork       | SanFrancisco |

### Conclusion

This is a very simple example. For some packages the custom statistics
will likely be much more complex. The flexibility of `skimr` allows you
to manage that.

Thanks to Jakub Nowosad, Tiernan Martin, Edzer Pebesma, Michael Sumner,
and Kyle Butts for inspiring and helping with the development of this
code.
