# Fix unicode histograms on Windows

This functions changes your session's locale to address issues with
printing histograms on Windows on versions of R below 4.2.1.

## Usage

``` r
fix_windows_histograms()
```

## Details

There are known issues with printing the spark-histogram characters when
printing a data frame, appearing like this:
"\<U+2582\>\<U+2585\>\<U+2587\>". This longstanding problem originates
in the low-level code for printing dataframes. This was addressed in R
version 4.2.1.

## See also

[`skim_without_charts()`](https://docs.ropensci.org/skimr/reference/skim.md)
