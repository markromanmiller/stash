Stash
================

I work with lots of data. Oftentimes I need to tell my program, “Hey,
this is a heavy function. Save the result, OK?” This is the role of
caching libraries. There are many available on CRAN for R code, but none
fit the philosophy I had when working on my thesis.

## Philosophy

The philosphy of `stash` is that the user of this library, like me, is
**rushed** and **forgetful.** This leads to several design
considerations:

  - **Cache entries are only cached as the result of a function call.**
    I don’t want to try to remember which step in a pipeline I cached
    the result on.
  - **The global identifier must be automatically computed from the
    call.** I don’t want to come up with a naming scheme for the cache
    entries, and I certainly won’t remember all the details every time.
  - **Dependencies must be automatically computed from the call.**
    Oftentimes I refactor out a larger function into smaller functions,
    then change one of those smaller functions. I don’t want to remember
    to add each smaller function to the list of dependencies.

## Interface

The most important function `do.call.stash`, which mimics the syntax of
base R’s `do.call`.

``` r
library(stash)

# Default cache location is ~/.stash-r/cache/"
# For a demo, put it in a temporary directory.
cache_dir <- file.path(tempdir(), "readme-test")
dir.create(cache_dir)
options(stash.cache_path = cache_dir)

heavy_function <- function(x) {
  Sys.sleep(3)
  mean(x)
}

result <- NULL
system.time({result <- do.call.stash(heavy_function, list(c(1:20)))})
```

    ##    user  system elapsed 
    ##   0.003   0.000   3.005

``` r
result
```

    ## [1] 10.5

``` r
system.time({result <- do.call.stash(heavy_function, list(c(1:20)))})
```

    ##    user  system elapsed 
    ##   0.001   0.000   0.001

``` r
result
```

    ## [1] 10.5

The other is setting the directory to store cached results, through
`options(stash.cache_path = "the/path/to/your/cache/")`. This call is
visible above.

## Contact

For comments, questions, and requests related to this library, Github
issues are best.
