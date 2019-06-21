
<!-- README.md is generated from README.Rmd. Please edit that file. -->
log4r [![Travis-CI Build Status](https://travis-ci.org/johnmyleswhite/log4r.png?branch=master)](https://travis-ci.org/johnmyleswhite/log4r) [![AppVeyor Build Status](https://ci.appveyor.com/api/projects/status/github/johnmyleswhite/log4r?branch=master)](https://ci.appveyor.com/project/johnmyleswhite/log4r)
===================================================================================================================================================================================================================================================================================================================

Introduction
------------

The log4r package is meant to provide a clean, lightweight object-oriented approach to logging in R based roughly on the widely-emulated log4j API.

Installation
------------

-   Stable CRAN version:

``` r
install.packages("log4r")
```

-   Development version on GitHub:

``` r
devtools::install_github("johnmyleswhite/log4r")
```

Usage
-----

Logging is configured by passing around `logger` objects created by `logger()`. By default, this will log to the console and suppress messages below the `"INFO"` level:

``` r
logger <- logger()

info(logger, "Located nearest gas station.")
#> INFO  [2019-03-26 18:35:08] Located nearest gas station.
warn(logger, "Ez-Gas sensor network is not available.")
#> WARN  [2019-03-26 18:35:08] Ez-Gas sensor network is not available.
debug(logger, "Debug messages are suppressed by default.")
```

Logging destinations are controlled by **Appenders**, a few of which are provided by the package. For instance, if we want to debug-level messages to a file:

``` r
log_file <- tempfile()
logger <- logger("DEBUG", appenders = file_appender(log_file))

info(logger, "Messages are now written to the file instead.")
debug(logger, "Debug messages are now visible.")

readLines(log_file)
#> [1] "INFO  [2019-03-26 18:35:08] Messages are now written to the file instead."
#> [2] "DEBUG [2019-03-26 18:35:08] Debug messages are now visible."
```

The `appenders` parameter takes a list, so you can log to multiple destinations transparently.

To control the format of the messages you can change the **Layout** used by each appender. Layouts are functions; you can write your own quite easily:

``` r
my_layout <- function(level, ...) {
  paste0(format(Sys.time()), " [", level, "] ", ..., collapse = "")
}

logger <- logger(appenders = console_appender(my_layout))
info(logger, "Messages should now look a little different.")
#> 2019-03-26 18:35:08 [INFO] Messages should now look a little different.
```

Older APIs
----------

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
#> [1] "INFO  [2019-03-26 18:35:08] An Info Message"      
#> [2] "WARN  [2019-03-26 18:35:08] A Warning Message"    
#> [3] "ERROR [2019-03-26 18:35:08] An Error Message"     
#> [4] "FATAL [2019-03-26 18:35:08] A Fatal Error Message"
```

The log4r Priority Levels
-------------------------

`log4r` supports five priority levels. In order from lowest to highest priority, they are:

-   `"DEBUG"`
-   `"INFO"`
-   `"WARN"`
-   `"ERROR"`
-   `"FATAL"`

Keep in Mind
------------

-   Use a log file in a non-existent directory will fail. In general, no effort is made to create directories.

-   Only messages at or above the current threshold are logged. Messages below this level are simply ignored.

-   Using the internal priority level constants using the `:::` notation is deprecated, but no warning is given. It is safer to simply use strings or numeric constants.
