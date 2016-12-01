
<!-- README.md is generated from README.Rmd. Please edit that file -->
CBC bindings for R
==================

This package provides bindings to the [COIN-CBC solver](https://projects.coin-or.org/Cbc).

It is currently work in progress. I have only tested it on a Mac so far.

Quickstart
----------

-   `-lCbc -lCbcSolver` need to be in your `PKG_LIBS` flags.
-   The header files of the coin libraries (`cbc`, `clp`, `osi`, `coinutils`) should to be in the include path of your cpp compiler.

``` r
devtools::install_github("dirkschumacher/rcbc")
```

``` r
library(rcbc)
# max 1 * x + 2 * y
# s.t.
#   x + y <= 1
#   x, y binary
A <- matrix(c(1, 1), ncol = 2, nrow = 1)
result <- CBC_solve(
 obj = c(1, 2),
 mat = A, # <- can also be a sparse matrix
 is_integer = c(TRUE, TRUE),
 row_lb = -Inf, row_ub = 1, max = TRUE,
 col_lb = c(0, 0), col_ub = c(1, 1),
 cbc_args = list("SEC" = "1"))
```

``` r
solution_status(result)
#> [1] "optimal"
```

``` r
objective_value(result)
#> [1] 2
```

``` r
column_solution(result)
#> [1] 0 1
```

Another example
---------------

Here we solve a larger Knapsack problem

``` r
set.seed(1)
max_capacity <- 1000
n <- 100
weights <- round(runif(n, max = max_capacity))
cost <- round(runif(n) * 100)

A <- matrix(weights, ncol = n, nrow = 1)
result <- CBC_solve(
 obj = cost,
 mat = A, 
 is_integer = rep.int(TRUE, n),
 row_lb = 0, row_ub = max_capacity, max = TRUE,
 col_lb = rep.int(0, n), col_ub = rep.int(1, n))
```

``` r
solution_status(result)
#> [1] "optimal"
```

``` r
objective_value(result)
#> [1] 607
```

``` r
column_solution(result)
#>   [1] 0 0 0 0 0 0 0 0 0 1 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0
#>  [36] 0 0 1 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0
#>  [71] 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1 0 0 1 0 0 0 0 0 0 0 0
```

Cbc Parameters
--------------

CBC has a number of [parameters](https://projects.coin-or.org/CoinBinary/export/1059/OptimizationSuite/trunk/Installer/files/doc/cbcCommandLine.pdf). You can pass them to the solver using the `cbc_args` argument.

For example the code below sets the timelimit of the solver to 5 seconds:

``` r
CBC_solve(..., cbc_args = list("sec" = 5))
```

TODO
----

-   Easy installation on all platforms
-   Add callback support
-   Write a ROI plugin.

Contribution
------------

Feel free to open issues and send PRs.