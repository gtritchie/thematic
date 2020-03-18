
<!-- README.md is generated from README.Rmd. Please edit that file -->

# thematic

<!-- badges: start -->

[![CRAN
status](https://www.r-pkg.org/badges/version/thematic)](https://CRAN.R-project.org/package=thematic)
[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://www.tidyverse.org/lifecycle/#experimental)
[![Travis build
status](https://travis-ci.org/rstudio/thematic.svg?branch=master)](https://travis-ci.org/rstudio/thematic)
[![Codecov test
coverage](https://codecov.io/gh/rstudio/thematic/branch/master/graph/badge.svg)](https://codecov.io/gh/rstudio/thematic?branch=master)
<!-- badges: end -->

Simple and automatic theming of **ggplot2**, **lattice**, and **base**
graphics.

## Installation

**thematic** is not yet available on [CRAN](https://CRAN.R-project.org),
but you can install it now with:

``` r
remotes::install_github("cpsievert/thematic")
library(thematic)
```

## Getting started

**thematic** provides a single entry point to controling the main colors
and fonts of **ggplot2**, **lattice**, and **base** graphics. To start a
(global) theme, give `thematic_begin()` background/foreground/accent
colors and a `font_spec()`. All of these arguments are optional, but
**thematic** works best if you specify at least `bg`/`fg`/`accent`.

``` r
library(thematic)
thematic_begin(
  bg = "#444444", fg = "#e4e4e4", accent = "#749886", 
  font = font_spec(family = "Oxanium", scale = 1.25, auto_install = TRUE)
)
```

As you’ll see in the examples below, **thematic** uses these colors to
inform consistent defaults not only for the plot’s overall theme (e.g.,
plot/panel background, text color, etc), but also for `accent`ed
graphical markers (e.g., **ggplot2** geom defaults), as well as
`sequential` and `qualitative` color scales (these scaling defaults can
also be controlled via `thematic_begin()`). `thematic_begin()` works by
modifying global state in such a way that all your future plots “just
work” (with [some exceptions]()), so best practice is to call
`thematic_end()` when you’re done using **thematic**.

## ggplot2

``` r
library(ggplot2)
ggplot(faithfuld, aes(waiting, eruptions, z = density)) +
  geom_raster(aes(fill = density)) + 
  geom_contour()
```

<img src="man/figures/README-unnamed-chunk-3-1.png" width="70%" style="display: block; margin: auto;" />

See the ggplot2 article for more examples and explanation.

### Base

Base R graphics don’t have a “global” notion of `sequential` colorscales
(i.e., they must be implemented as a part of each plotting call); but in
most cases, you can provide the current sequential colorscale by doing
something like `col = thematic_current("sequential")`:

``` r
image(volcano)
image(volcano, col = thematic_current("sequential"))
```

<img src="man/figures/README-unnamed-chunk-4-1.png" width="70%" style="display: block; margin: auto;" /><img src="man/figures/README-unnamed-chunk-4-2.png" width="70%" style="display: block; margin: auto;" />

## Lattice

``` r
lattice::show.settings()
```

<img src="man/figures/README-unnamed-chunk-5-1.png" width="70%" style="display: block; margin: auto;" />

## Google fonts

There are two main controls for fonts currently: `family` and `scale`.
`scale` (defaults to 1) is multiplied against all relevant font sizes.
If `family` references a font that doesn’t exist on the system, but the
font is available on [Google Fonts](https://fonts.google.com/),
**thematic** will try to download and register the font files for you
(as long as `auto_install = TRUE`).

``` r
font <- font_spec(family = "Oxanium", scale = 1.25, auto_install = TRUE)
thematic_begin("black", "white", font = font)
p
```

## With shiny auto-theming

**shiny** uses **thematic**’s functionality to implement it’s
auto-theming feature.

TODO: an example of overriding auto-theming defaults

## Known issues

Auto-installed fonts (i.e., custom fonts downloaded from Google Fonts)
currently don’t work at all with the RStudio graphics device. *If* you
have the **showtext** package installed, they should work with
**rmarkdown**/**knitr** as well as with the default `device` in
`thematic_with_device()` (the latter will also work if you have **ragg**
instead of **showtext** installed):

``` r
file <- thematic_with_device(plot(1), res = 144)
```

If you’re in RStudio, you can preview the resulting `file` with
`file.show(file)`. Moreover, to embed the `file` in
**rmarkdown**/**knitr**, do `knitr::include_graphics(file)`.

### Manually installing fonts

If you want to use custom font(s) that aren’t Google Font(s), you’ll
need to manually download and install/register them with R. Since
**thematic** has implicit **ragg** and **showtext** integration, after
downloading the font files, you can use `systemfonts::register_font()`
and `sysfonts::font_add()` to register them. Another, more expensive,
but permanent way to make custom fonts available to R is with
`extrafont::import_font()` and `extrafont::loadfonts()`.
