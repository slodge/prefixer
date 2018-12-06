# prefixer:: <img src="man/figures/logo_prefixer.png" width=200 align="right" />

> Prefix function with their namespace and other tools for writing functions

[![Travis-CI Build Status](https://travis-ci.org/dreamRs/prefixer.svg?branch=master)](https://travis-ci.org/dreamRs/prefixer)
[![Project Status: Active – The project has reached a stable, usable state and is being actively developed.](http://www.repostatus.org/badges/latest/active.svg)](http://www.repostatus.org/#active)


## Overview

It can be useful to prefix function in a script to prevent use of the wrong one, e.g. `stats::filter` vs `dplyr::filter` or `plyr::summarise` vs `dplyr::summarise`.
This package provide a Shiny gadget to interactively add prefix to function in a script, if a function exist in several packages, you can choose the one you want use.

If you're in a package, you can generate `@importFrom` tag from function definition and after remove prefix if needeed.


Installation :

```r
# with remotes
remotes::install_github("dreamRs/prefixer")

# or with install-github.me service (based on remotes)
source("https://install-github.me/dreamRs/prefixer")

# or with devtools:
devtools::install_github("dreamRs/prefixer")
```


:warning: functions of this package will modify your scripts



## prefixer:: gadget

You can launch the addin via RStudio's Addins menu. Interface looks like this :

![](inst/img/prefixerUI.png)


```r
fread_dir <- function(path, pattern = "\\.csv$") {
  paths <- list.files(path = path, pattern = pattern, full.names = TRUE)
  files <- lapply(paths, fread)
  files <- setNames(files, paths)
  rbindlist(l = files, idcol = "path")
}
```

Becomes => 

```r
fread_dir <- function(path, pattern = "\\.csv$") {
  paths <- list.files(path = path, pattern = pattern, full.names = TRUE)
  files <- lapply(paths, data.table::fread)
  files <- stats::setNames(files, paths)
  data.table::rbindlist(l = files, idcol = "path")
}
```




## @importFrom

From prefixed functions, you can generate roxygen `@importFrom` tag via addin *@importFrom*.

Or manually with `import_from(fun = fread_dir)`:

```r
#' @importFrom data.table fread rbindlist
#' @importFrom stats setNames
fread_dir <- function(path, pattern = "\\.csv$") {
  paths <- list.files(path = path, pattern = pattern, full.names = TRUE)
  files <- lapply(paths, data.table::fread)
  files <- stats::setNames(files, paths)
  data.table::rbindlist(l = files, idcol = "path")
}
```


## Unprefix

After generated `@importFrom` tags, you can if you want remove prefix before your functions via addin *Unprefix* : 

```r
#' @importFrom data.table fread rbindlist
#' @importFrom stats setNames
fread_dir <- function(path, pattern = "\\.csv$") {
  paths <- list.files(path = path, pattern = pattern, full.names = TRUE)
  files <- lapply(paths, fread)
  files <- setNames(files, paths)
  rbindlist(l = files, idcol = "path")
}
```


## Roxygen comments

Addin *Roxygen comment* allow to comment selected line with `#'`.


## Not-ASCII

Addin *Not-ASCII* escape all not-ASCII characters between quotes to Unicode, to avoid warning in R CMD check :

```r
frenchFileInput <- function(inputId) {
  fileInput(
    inputId = inputId,
    label = "Sélectionner un fichier :",
    buttonLabel = "Parcourir...",
    placeholder = "Aucun fichier sélectionné"
  )
}
```
becomes =>

```r
frenchFileInput <- function(inputId) {
  fileInput(
    inputId = inputId,
    label = "S\u00e9lectionner un fichier :",
    buttonLabel = "Parcourir...",
    placeholder = "Aucun fichier s\u00e9lectionn\u00e9"
  )
}
```

