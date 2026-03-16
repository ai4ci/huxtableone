# Find the package environment for a given environment or function

This searches the environment stack looking for the first environment
that defines a package. This is the package that the function is defined
in. If no namespace environment is found then the function is in the
global environment.

## Usage

``` r
.find_namespace(env = rlang::caller_env())
```

## Arguments

- env:

  an environment or function.

## Value

a namespaced package environment

## Unit tests



    env = .find_namespace(rlang::abort)
    testthat::expect_equal(unname(getNamespaceName(env)), "rlang")

    # TODO: this test fails when running in testthat environment but not otherwise
    # env = new.env(parent=emptyenv())
    # assign("f", function() {"test"}, envir = env)
    # env2 = .find_namespace(env$f)
    # testthat::expect_null(.find_namespace(env$f))
