
# GCSM

The goal of GCSM is to implement the generic composite similarity
measure (GCSM), described in “A generic composite measure of similarity
between geospatial variables” by Liu et al. (2020)
[doi:10.1016/j.ecoinf.2020.101169](https://doi.org/10.1016/j.ecoinf.2020.101169).
This package also provides implements of SSIM and CMSC. Functions are
given to compute composite similarity between vectors (e.g, `gcsm`), on
spatial windows (e.g., `gcsm_sw`) or temporal windows (e.g., `gcsm_tw`).
They are implemented in C++ with
[RcppArmadillo](https://github.com/RcppCore/RcppArmadillo). OpenMP is
used for parallel computing.

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
#> [1] 0.8320781
cmsc(x, x + noise, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] 0.9014253
## dissimilariry
gcsm(y, x + noise, xmin = 0, xmax = 1, ymin = 0, ymax = 1)
#> [1] -0.8320781
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
#> [1,] 0.9598027 0.9554981 0.9414164 0.9051556 0.9381126 0.9581013
#> [2,] 0.9612400 0.9502093 0.9404996 0.9378249 0.9513824 0.9540864
#> [3,] 0.9486808 0.9259066 0.9161810 0.9470607 0.9580740 0.9556207
#> [4,] 0.9563071 0.9357359 0.9293909 0.9481971 0.9581873 0.9525351
#> [5,] 0.9605745 0.9532348 0.9445640 0.9421224 0.9409076 0.9364858
#> [6,] 0.9709259 0.9676898 0.9619197 0.9497676 0.9279447 0.9016685
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
