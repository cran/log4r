
<!-- README.md is generated from README.Rmd. Please edit that file. -->

# log4r

<!-- badges: start -->

[![CRAN
status](https://www.r-pkg.org/badges/version/log4r)](https://cran.r-project.org/package=log4r)
[![R-CMD-check](https://github.com/johnmyleswhite/log4r/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/johnmyleswhite/log4r/actions/workflows/R-CMD-check.yaml)
<!-- badges: end -->

**log4r** is a fast, lightweight, object-oriented approach to logging in
R based on the widely-emulated [Apache
Log4j](https://logging.apache.org/log4j/) project.

**log4r** differs from other R logging packages in its focus on
performance and simplicity. As such, it has fewer features – although it
is still quite extensible, as seen below – but is much faster. See
`vignette("performance", package = "log4r")` for details.

Unlike other R logging packages, **log4r** also has first-class support
for structured logging. See
`vignette("structured-logging", package = "log4r")` for details.

## Installation

The package is available from CRAN:

``` r
install.packages("log4r")
```

If you want to use the development version, you can install the package
from GitHub as follows:

``` r
# install.packages("remotes")
remotes::install_github("johnmyleswhite/log4r")
```

## Usage

Logging is configured by passing around `logger` objects created by
`logger()`. By default, this will log to the console and suppress
messages below the `"INFO"` level:

``` r
logger <- logger()

info(logger, "Located nearest gas station.")
#> INFO  [2019-09-04 16:31:04] Located nearest gas station.
warn(logger, "Ez-Gas sensor network is not available.")
#> WARN  [2019-09-04 16:31:04] Ez-Gas sensor network is not available.
debug(logger, "Debug messages are suppressed by default.")
```

Logging destinations are controlled by **Appenders**, a few of which are
provided by the package. For instance, if we want to debug-level
messages to a file:

``` r
log_file <- tempfile()
logger <- logger("DEBUG", appenders = file_appender(log_file))

info(logger, "Messages are now written to the file instead.")
debug(logger, "Debug messages are now visible.")

readLines(log_file)
#> [1] "INFO  [2019-09-04 16:31:04] Messages are now written to the file instead."
#> [2] "DEBUG [2019-09-04 16:31:04] Debug messages are now visible."
```

The `appenders` parameter takes a list, so you can log to multiple
destinations transparently.

For local development or simple batch R scripts run manually, writing
log messages to a file for later inspection is convenient. However, for
deployed R applications or automated scripts it is more likely you will
need to send logs to a central location; see
`vignette("logging-beyond-local-files", package = "log4r")`.

To control the format of the messages you can change the **Layout** used
by each appender. Layouts are functions; you can write your own quite
easily:

``` r
my_layout <- function(level, ...) {
  paste0(format(Sys.time()), " [", level, "] ", ..., collapse = "")
}

logger <- logger(appenders = console_appender(my_layout))
info(logger, "Messages should now look a little different.")
#> 2019-09-04 16:31:04 [INFO] Messages should now look a little different.
```

With an appropriate layout, you can also use *structured logging*,
enriching log messages with contextual fields:

``` r
logger <- logger(appenders = console_appender(logfmt_log_layout()))
info(
  logger, message = "processed entries", file = "catpics_01.csv",
  entries = 4124, elapsed = 2.311
)
#> level=INFO ts=2021-10-22T20:19:21Z message="processed entries" file=catpics_01.csv entries=4124 elapsed=2.311
```

## Older APIs

The 0.2 API is still supported:

``` r
logger <- create.logger()

logfile(logger) <- log_file
level(logger) <- "INFO"

debug(logger, 'A Debugging Message')
info(logger, 'An Info Message')
warn(logger, 'A Warning Message')
error(logger, 'An Error Message')
fatal(logger, 'A Fatal Error Message')

readLines(log_file)
#> [1] "INFO  [2019-09-04 16:31:05] An Info Message"      
#> [2] "WARN  [2019-09-04 16:31:05] A Warning Message"    
#> [3] "ERROR [2019-09-04 16:31:05] An Error Message"     
#> [4] "FATAL [2019-09-04 16:31:05] A Fatal Error Message"
```

## License

The package is available under the terms of the Artistic License 2.0.
