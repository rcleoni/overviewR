
<!-- README.md is generated from README.Rmd. Please edit that file -->

# overviewR <img src='man/figures/logo.png' align="right" height="139" />

<!-- badges: start -->

[![Travis build
status](https://travis-ci.com/cosimameyer/overviewR.svg?branch=master)](https://travis-ci.com/cosimameyer/overviewR)
[![License: GPL
v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
<!-- badges: end -->

The goal of overviewR is to make it easy to get an overview of your data
by displaying your sample information. At the moment, you have two
functions (`overview_tab` and `overview_crosstab`) that allow you to
generate a tabular overview of your general sample and conditional
sample. `overview_print` then converts this output to nicely usable tex
code.

## Installation

You can install the released version of overviewR from
[GitHub](https://github.com/cosimameyer/overviewR) with:

``` r
library(devtools)
devtools::install_github("cosimameyer/overviewR")
```

<!--[CRAN](https://CRAN.R-project.org) with:
``` r
install.packages("overviewR")
```
-->

## Example

Before we delve into the functions, we need some data to showcase the
magic that `overviewR` can perform. We have 19 observations for 5
countries (Rwanda, Angola, Benin, UK, and France) stored in the
`countries` variable, over a time period between 1990 to 1999 (`years`).
As you can tell, we have some gaps in the time frame. We also add
randomly generated values for the GDP (`gdp`) and the population size
(`population`).

``` r
# Generate some data
# Set a seed to make it reproducable
set.seed(68163)

df_combined <- data.frame(
  # Countries
  countries  = c(
    rep("RWA", 4),
    rep("AGO", 8),
    rep("BEN", 2),
    rep("GBR", 5),
    rep("FRA", 3)
  ),
  # Time frame
  years =
    c(
      seq(1990, 1995),
      seq(1990, 1992),
      seq(1995, 1999),
      seq(1991, 1999, by = 2),
      seq(1993, 1999, by = 3)
    ),
  # GDP
  gdp =
    runif(22, 10000, 40000),
  # Population
  population =
    runif(22, 100, 50000),
  stringsAsFactors = FALSE
) 
```

Let’s have a first look at the data:

``` r
head(df_combined)
#>   countries years      gdp population
#> 1       RWA  1990 25738.10   15557.17
#> 2       RWA  1991 32562.67   10408.66
#> 3       RWA  1992 15010.20   34508.06
#> 4       RWA  1993 19833.02   14597.29
#> 5       AGO  1994 12260.58   40796.76
#> 6       AGO  1995 36345.42   13842.45
```

As you can see, we need a data frame that has one column with an id (in
this case `countries`) and one column with a time variable (here
`years`). If your dataset does not have this format, consider using
[`pivot_wider()` or
`pivot_longer()`](https://tidyr.tidyverse.org/reference/pivot_longer.html)
to get to the format.

Load the package

``` r
library(overviewR)
```

### `overview_tab`

Generate some general overview using `overview_tab`.

``` r
output_table <- overview_tab(dat = df_combined, id = countries, time = years)
```

    # countries time_frame
    # RWA           1990 - 1993
    # AGO           1994 - 1995, 1990 - 1992, 1996 - 1997
    # BEN           1998 - 1999
    # GBR           1991, 1993, 1995, 1997, 1999
    # FRA           1993, 1996, 1999

We store the output in the object `output_table` to access it later.
<!-- This function automatically generates an object and stores it in your environment so that you can access it later. -->

### `overview_crosstab`

If you want to generate a cross table that divides our data
conditionally on two factors, this can be done with `overview_crosstab`.

``` r
output_crosstab <- overview_crosstab(
    dat = df_combined,
    cond1 = gdp,
    cond2 = population,
    threshold1 = 30000,
    threshold2 = 15000,
    id = countries,
    time = years
  )
```

``` 
```

We also store the output in the object, this time in `output_crosstab`,
to access it later.

<!-- The resulting data frame is again stored as an object in your environment so that you can access it later. -->

### `overview_print`

To generate an easily usable LaTeX output, `overviewR` offers the
function `overview_print`.

We will start with our general sample overview, stored in
`output_table`.

``` r
overview_print(obj = output_table)
```

<details>

<summary>TeX output</summary>

    % Overview table generated in R version 3.6.3 (2020-02-29) using overviewR 
     \begin{table}[ht] 
     \centering 
     \caption{Time and scope of the sample} 
     \begin{tabular}{ll} 
     \hline 
     &  \\ \hline 
     RWA & 1990 - 1993 \\ AGO & 1994 - 1995, 1990 - 1992, 1996 - 1997 \\ BEN & 1998 - 1999 \\ GBR & 1991, 1993, 1995, 1997, 1999 \\ FRA & 1993, 1996, 1999 \\ \hline 
     \end{tabular} 
     \end{table} 

</details>

The default already gives us a nice title (“Time and scope of the
sample”) but can be modified in the argument `title = ...`.

``` r
overview_print(obj = output_table, title = "Cool new title for our awesome table")
```

<details>

<summary>TeX output</summary>

    % Overview table generated in R version 3.6.3 (2020-02-29) using overviewR 
     \begin{table}[ht] 
     \centering 
     \caption{Cool new title for our awesome table} 
     \begin{tabular}{ll} 
     \hline 
     &  \\ \hline 
     RWA & 1990 - 1993 \\ AGO & 1994 - 1995, 1990 - 1992, 1996 - 1997 \\ BEN & 1998 - 1999 \\ GBR & 1991, 1993, 1995, 1997, 1999 \\ FRA & 1993, 1996, 1999 \\ \hline 
     \end{tabular} 
     \end{table} 

</details>

The same function also text formatted cross tables, using the argument
`crosstab = TRUE`. We will do this by using the object `output_crosstab`
that was stored from our cross table above. There are also options to
label the respective conditions (`cond1` and `cond2`) as you can see in
the example.

``` r
overview_print(
  obj = output_crosstab,
  title = "Cross table of the sample",
  crosstab = TRUE,
  cond1 = "GDP",
  cond2 = "Population"
)
```

<details>

<summary>TeX output</summary>

    % Overview table generated in R version 3.6.3 (2020-02-29) using overviewR 
     % Please add the following required packages to your document preamble: 
     % \usepackage{multirow} 
     % \usepackage{tabularx} 
     % \newcolumntype{b}{X} 
     % \newcolumntype{s}{>{\hsize=.5\hsize}X} 
     
     \begin{table}[] 
     \begin{tabularx}{\textwidth}{ssbb} 
     \hline & & 
     \multicolumn{2}{c}{\textbf{GDP}} \\  & & \textbf{Fulfilled} & 
     \textbf{Not fulfilled} \\ \hline \\ \multirow{2}{*}{\textbf{Population}} & \textbf{Fulfilled} & 
     RWA (1990 - 1991), AGO (1990), GBR (1993, 1997), FRA (1996) & AGO (1994, 1991 - 1992), GBR (1991), FRA (1993, 1999)\\  \\ \hline \\ & \textbf{Not fulfilled} &  RWA (1992 - 1993), AGO (1996), GBR (1999) & AGO (1995, 1997), BEN (1998 - 1999), GBR (1995)\\  \hline \\ \end{tabularx} 
     \end{table} 

</details>

With `save_out = TRUE` you can also export both outputs as .tex files
and store them on your device.

``` r
overview_print(obj = output_table, save_out = TRUE)
```

The output is also compatible with other functions such as
[xtable](https://cran.r-project.org/web/packages/xtable/xtable.pdf) or
[flextable](https://cran.r-project.org/web/packages/flextable/vignettes/overview.html).

*This package is built by [Cosima Meyer](https://cosimameyer.github.io)
and [Dennis Hammerschmidt](http://dennis-hammerschmidt.rbind.io).*
