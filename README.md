
# GCSM

<!-- badges: start -->

[![R-CMD-check](https://github.com/liuyadong/GCSM/workflows/R-CMD-check/badge.svg)](https://github.com/liuyadong/GCSM/actions)
[![CRAN
status](https://www.r-pkg.org/badges/version/GCSM)](https://CRAN.R-project.org/package=GCSM)
[![R-CMD-check](https://github.com/liuyadong/GCSM/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/liuyadong/GCSM/actions/workflows/R-CMD-check.yaml)
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
#> [1] 0.8438327
cmsc(x, x + noise, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] 0.9262687
## dissimilariry
gcsm(y, x + noise, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] -0.8438327
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
#> [1,] 0.9400044 0.9439833 0.9348286 0.9220044 0.9163602 0.9286758
#> [2,] 0.9506770 0.9548234 0.9387744 0.9230389 0.8870794 0.9021104
#> [3,] 0.9493531 0.9532574 0.9279886 0.9208246 0.8784030 0.9029298
#> [4,] 0.9132100 0.9333228 0.9168807 0.8992412 0.8577266 0.8920520
#> [5,] 0.8585278 0.9051925 0.9263586 0.9268652 0.9316172 0.9424565
#> [6,] 0.8440742 0.9072937 0.9442298 0.9433688 0.9478457 0.9510028
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
