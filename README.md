
# GCSM

<!-- badges: start -->

[![R-CMD-check](https://github.com/liuyadong/GCSM/workflows/R-CMD-check/badge.svg)](https://github.com/liuyadong/GCSM/actions)
[![CRAN
status](https://www.r-pkg.org/badges/version/GCSM)](https://CRAN.R-project.org/package=GCSM)
<!-- badges: end -->

The goal of GCSM is to implement the generic composite similarity
measure (GCSM), described in “A generic composite measure of similarity
between geospatial variables” by Liu et al. (2020)
[doi:10.1016/j.ecoinf.2020.101169](https://doi.org/10.1016/j.ecoinf.2020.101169).
This package also provides implementations of SSIM and CMSC. Functions
are given to compute composite similarity between vectors (e.g, `gcsm`),
on spatial windows (e.g., `gcsm_sw`) or temporal windows (e.g.,
`gcsm_tw`). They are implemented in C++ with
[RcppArmadillo](https://github.com/RcppCore/RcppArmadillo). OpenMP is
used to facilitate parallel computing.

## Installation

You can install the released version of GCSM from CRAN with:

``` r
install.packages("GCSM")
```

Or install the development version from GitHub with:

``` r
# install.packages("devtools")
devtools::install_github("liuyadong/GCSM")
```

## Examples

Composite similarity between vectors:

``` r
library(GCSM)

x = runif(9)
gcsm(x, x)
#> [1] 1
cmsc(x, x)
#> [1] 1

# mean shift
gcsm(x, x - 0.2, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] 0.8
cmsc(x, x - 0.2, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] 0.96
gcsm(x, x + 0.2, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] 0.8
cmsc(x, x + 0.2, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] 0.96
## dissimilarity
y = 1 - x # y is the perfect antianalog of x
gcsm(y, x)
#> [1] -1
gcsm(y, x - 0.2, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] -0.8
gcsm(y, x + 0.2, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] -0.8

# random noise
noise = rnorm(9, mean = 0, sd = 0.1)
gcsm(x, x + noise, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] 0.7719099
cmsc(x, x + noise, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] 0.9427791
## dissimilariry
gcsm(y, x + noise, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] -0.7719099
```

Composite similarity on spatial windows:

``` r
x = matrix(runif(36), nrow = 6, ncol = 6)
gcsm_sw(x, x + 0.2, xmin = 0, xmax = 1, ymin = 0, ymax = 1, ksize = 3)
#>      [,1] [,2] [,3] [,4] [,5] [,6]
#> [1,]  0.8  0.8  0.8  0.8  0.8  0.8
#> [2,]  0.8  0.8  0.8  0.8  0.8  0.8
#> [3,]  0.8  0.8  0.8  0.8  0.8  0.8
#> [4,]  0.8  0.8  0.8  0.8  0.8  0.8
#> [5,]  0.8  0.8  0.8  0.8  0.8  0.8
#> [6,]  0.8  0.8  0.8  0.8  0.8  0.8
cmsc_sw(x, x + 0.2, xmin = 0, xmax = 1, ymin = 0, ymax = 1, ksize = 3)
#>      [,1] [,2] [,3] [,4] [,5] [,6]
#> [1,] 0.96 0.96 0.96 0.96 0.96 0.96
#> [2,] 0.96 0.96 0.96 0.96 0.96 0.96
#> [3,] 0.96 0.96 0.96 0.96 0.96 0.96
#> [4,] 0.96 0.96 0.96 0.96 0.96 0.96
#> [5,] 0.96 0.96 0.96 0.96 0.96 0.96
#> [6,] 0.96 0.96 0.96 0.96 0.96 0.96
ssim_sw(x, x + 0.2, xmin = 0, xmax = 1, ymin = 0, ymax = 1, ksize = 3)
#>           [,1]      [,2]      [,3]      [,4]      [,5]      [,6]
#> [1,] 0.9411454 0.9214168 0.9169390 0.9461954 0.9712785 0.9777024
#> [2,] 0.9625560 0.9545538 0.9517716 0.9632256 0.9717116 0.9736731
#> [3,] 0.9703725 0.9675556 0.9610270 0.9679905 0.9633441 0.9609509
#> [4,] 0.9688934 0.9684905 0.9655600 0.9679028 0.9587779 0.9518538
#> [5,] 0.9538236 0.9484908 0.9404195 0.9511968 0.9568499 0.9606823
#> [6,] 0.9476272 0.9330108 0.9286503 0.9456641 0.9650384 0.9701094
```

Composite similarity on temporal windows:

``` r
x = array(runif(81), dim = c(3, 3, 9))
gcsm_tw(x, x + 0.2, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#>      [,1] [,2] [,3]
#> [1,]  0.8  0.8  0.8
#> [2,]  0.8  0.8  0.8
#> [3,]  0.8  0.8  0.8
cmsc_tw(x, x + 0.2, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#>      [,1] [,2] [,3]
#> [1,] 0.96 0.96 0.96
#> [2,] 0.96 0.96 0.96
#> [3,] 0.96 0.96 0.96
```
