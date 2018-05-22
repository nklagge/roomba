
roomba
======

[![Travis build status](https://travis-ci.com/ropenscilabs/roomba.svg?branch=master)](https://travis-ci.com/ropenscilabs/roomba)

This is a package to transform large, multi-nested lists into a more user-friendly format (i.e. a `tibble`) in `R`. The initial focus is on making processing of return values from `jsonlite::fromJSON()` queries more seamless, but ideally this package should be useful for deeply-nested lists from an array of sources.

*Key features:*

-   `roomba()` searches deeply-nested list for names specified in `cols` (a character vector) and returns a `tibble` with the associated column titles. Nothing further about nesting hierarchy or depth need be specified.
-   Handles empty values gracefully by substituting `NULL` values with `NA` or user-specified value in `.default`, or truncates lists appropriately.
-   Option to `.keep` `any` or `all` data from the columns supplied

Installation
------------

You can install the development version from [GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("ropenscilabs/roomba")
```

Example
-------

Say we have some JSON from a pesky API.

``` r
library(roomba)

json <- '
  {
    "stuff": {
      "buried": {
        "deep": [
        {
          "location": "here",
          "name": "Amanda",
          "super_power": "flight",
          "more_nested_stuff": 4
        },
        {
          "location": "here",
          "name": "Christine",
          "super_power": "water-breathing"
        },
        {
          "location": "here",
          "name": "Isabella",
          "super_power": "teleportation"
        }
        ],
        "alsodeep": 2342423234,
        "deeper": {
          "foo": [
          {
            "location": "not here",
            "name": "Jim"
          },
          {
             "location": "here",
             "name": "Laura",
             "super_power": "git"
          }
          ]
        }
      }
    }
  }'
```

The JSON becomes a nested R list

``` r
super_data <- jsonlite::fromJSON(json, simplifyVector = FALSE)
```

Which we can pull data into the columns we want with `roomba`.

``` r
super_data %>%
  roomba(cols = c("name", "super_power", "location"), keep = any)
#> # A tibble: 5 x 3
#>   location name      super_power    
#>   <chr>    <chr>     <chr>          
#> 1 here     Amanda    flight         
#> 2 here     Christine water-breathing
#> 3 here     Isabella  teleportation  
#> 4 not here Jim       <NA>           
#> 5 here     Laura     git
```
