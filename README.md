
# GCSM

<!-- badges: start -->

[![R-CMD-check](https://github.com/liuyadong/GCSM/workflows/R-CMD-check/badge.svg)](https://github.com/liuyadong/GCSM/actions)
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
#> [1] 0.8897559
cmsc(x, x + noise, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] 0.9343575
## dissimilariry
gcsm(y, x + noise, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] -0.8897559
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
#> [1,] 0.9175712 0.9100422 0.8961625 0.9493718 0.9603352 0.9672205
#> [2,] 0.9337187 0.9210430 0.8816175 0.9333139 0.9436170 0.9545060
#> [3,] 0.9523075 0.9402990 0.9179069 0.9327686 0.9231006 0.9139099
#> [4,] 0.9532904 0.9484003 0.9479293 0.9533212 0.9356022 0.9017036
#> [5,] 0.9059504 0.9120740 0.9468424 0.9586003 0.9539784 0.9307388
#> [6,] 0.7597572 0.8207329 0.9241325 0.9523249 0.9585352 0.9501642
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
