# ATC index


The Anatomical Therapeutic Chemical (ATC) index
([ATC](https://atcddd.fhi.no/atc_ddd_index/)) is a system for the
classification of substances intended for therapeutic use in medicine.

Created using [ATCtools](https://github.com/amr-pub/ATC-tools).

## Combine .CSVs

``` r
library(here)
library(dplyr)
library(purrr)
library(readr)
library(stringr)

# Get a list of file paths for files in the directory.
filepaths <- list.files(here::here(), full.names = TRUE)

# Get a list of file paths for .CSVs with one to two character names.
filepaths <- filepaths[stringr::str_detect(filepaths, "/[A-Za-z0-9]{1,2}.csv")]

# Get a list of file/obj names.
filenames <- stringr::str_extract(filepaths, "([A-Z])(?=.csv)")

# Read all .CSVs; no vectorized read_csv because columns don't match.
purrr::map2(filenames, filepaths, \(x,y) {assign(x, readr::read_csv(file = y), envir = .GlobalEnv)})

# Row bind all .CSV objects.
ATC       <- dplyr::bind_rows(lapply(filenames, \(x) {eval(as.symbol(x))}))

# Write the ATCvet CSV.
readr::write_csv(ATC, here::here("ATC.csv"))
```
