
# GCSM

The goal of GCSM is to implement the generic composite similarity
measure (GCSM), described in “A generic composite measure of similarity
between geospatial variables” by Liu et al. (2020)
[doi:10.1016/j.ecoinf.2020.101169](https://doi.org/10.1016/j.ecoinf.2020.101169).
This package also provides implements of SSIM and CMSC. Functions are
given to compute composite similarity between vectors (e.g, `gcsm`), on
spatial windows (e.g., `gcsm_sw`) or temporal windows (e.g., `gcsm_tw`).
They are implemented in C++ with RcppArmadillo. OpenMP is used for
parallel computing.

## Installation

You can install the package from [GitHub](https://github.com/) with:

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
#> [1] 0.8538475
cmsc(x, x + noise, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] 0.9205917
## dissimilariry
gcsm(y, x + noise, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] -0.8538475
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
#> [1,] 0.9330725 0.9543268 0.9580800 0.9531022 0.9464806 0.9553984
#> [2,] 0.9301401 0.9414081 0.9372444 0.9455836 0.9492206 0.9603127
#> [3,] 0.9481841 0.9332954 0.8836522 0.9105995 0.9308114 0.9485747
#> [4,] 0.9561241 0.9353591 0.8877687 0.8985309 0.9210743 0.9317091
#> [5,] 0.9691957 0.9609796 0.9540076 0.9455161 0.9402787 0.9241170
#> [6,] 0.9709043 0.9684239 0.9720521 0.9685025 0.9622086 0.9443346
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
