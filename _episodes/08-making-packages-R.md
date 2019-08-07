---
# Please do not edit this file directly; it is auto generated.
# Instead, please edit 08-making-packages-R.md in _episodes_rmd/
title: "Making Packages in R"
teaching: 30
exercises: 0
questions:
- "How do I collect my code together so I can reuse it and share it?"
- "How do I make my own packages?"
objectives:
- "Describe the required structure of R packages."
- "Create the required structure of a simple R package."
- "Write documentation comments that can be automatically compiled to R's native help and documentation format."

keypoints:
- "A package is the basic unit of reusability in R."
- "Every package must have a DESCRIPTION file and an R directory containing code. These are created by us."
- "A NAMESPACE file is needed as well, and a man directory containing documentation, but both can be autogenerated."
source: Rmd
---



Why should you make your own R packages?

**Reproducible research!**

An R package is the **basic unit of reusable code**.
If you want to reuse code later or want others to be able to use your code, you should put it in a package.

An R package requires four components:

*   a DESCRIPTION file with metadata about the package
*   an R directory with the code
*   a man directory with documentation (we will create this automatically)
*   a NAMESPACE file listing user-level functions in the package (we will also create this automatically)

*There are other optional components. [Read the "R packages" book][r-pkgs-desc] for much more information.*

[r-pkgs-desc]: http://r-pkgs.had.co.nz/description.html

### DESCRIPTION file

~~~
Package: Package name
Title: Brief package description
Description: Longer package description
Version: Version number(major.minor.patch)
Author: Name and email of package creator
Maintainer: Name and email of package maintainer (who to contact with issues)
License: Abbreviation for an open source license
~~~
{: .source}

The package name can only contain letters and numbers and has to start with a letter.

### .R files

Functions don't all have to be in one file or each in separate files.
How you organize them is up to you.
Suggestion: organize in a logical manner so that you know which file holds which functions.

### Making your first R package

Let's turn our temperature conversion functions into an R package.


~~~
fahrenheit_to_kelvin <- function(temp_F) {
    # Converts Fahrenheit to Kelvin
    temp_K <- ((temp_F - 32) * (5/9)) + 273.15
    temp_K
}
~~~
{: .language-r}


~~~
kelvin_to_celsius <- function(temp_K) {
  # Converts Kelvin to Celsius
  temp_C <- temp_K - 273.15
  temp_C
}
~~~
{: .language-r}


~~~
fahrenheit_to_celsius <- function(temp_F) {
  # Converts Fahrenheit to Celsius using fahrenheit_to_kelvin() and kelvin_to_celsius()
  temp_K <- fahrenheit_to_kelvin(temp_F)
  temp_C <- kelvin_to_celsius(temp_K)
  temp_C
}
~~~
{: .language-r}

We will use the [`devtools`][devtools] and [`roxygen2`][roxygen2] packages, which make creating packages in R relatively simple. Both can be installed from CRAN like this:


~~~
install.packages(c("devtools", "roxygen2"))  # installations can be `c`ombined
library("devtools")
library("roxygen2")
~~~
{: .language-r}

[devtools]: https://cran.r-project.org/package=devtools
[roxygen2]: https://cran.r-project.org/package=roxygen2


Set your working directory, and then use the `create` function to start making your package.
Keep the name simple and unique.
  - package_to_convert_temperatures_between_kelvin_fahrenheit_and_celsius (BAD)
  - tempConvert (GOOD)


~~~
setwd(parentDirectory)
create("tempConvert")
~~~
{: .language-r}

Add our functions to the R directory.
Place each function into a separate R script and add documentation like this:


~~~
#' Converts Fahrenheit to Kelvin
#'
#' This function converts input temperatures in Fahrenheit to Kelvin.
#' @param temp_F The temperature in Fahrenheit.
#' @return The temperature in Kelvin.
#' @export
#' @examples
#' fahrenheit_to_kelvin(32)

fahrenheit_to_kelvin <- function(temp_F) {
  temp_K <- ((temp_F - 32) * (5/9)) + 273.15
  temp_K
}
~~~
{: .language-r}

The `roxygen2` package reads lines that begin with `#'` as comments to create the documentation for your package.
Descriptive tags are preceded with the `@` symbol. For example, `@param` has information about the input parameters for the function.
Now, we will use `roxygen2` to convert our documentation to the standard R format.


~~~
setwd("./tempConvert")
document()
~~~
{: .language-r}

Take a look at the package directory now.
The /man directory has a .Rd file for each .R file with properly formatted documentation.

Overall, your package directory should look something like this:

<img src="../fig/R-package-structure.svg" alt="R Package Structure" width="500" />

Now, let's load the package and take a look at the documentation.


~~~
setwd("..")
install("tempConvert")

?fahrenheit_to_kelvin
~~~
{: .language-r}

Notice there is now a tempConvert environment that is the parent environment to the global environment.


~~~
search()
~~~
{: .language-r}

Now that our package is loaded, let's try out some of the functions.


~~~
fahrenheit_to_celsius(32)
~~~
{: .language-r}



~~~
[1] 0
~~~
{: .output}



~~~
fahrenheit_to_kelvin(212)
~~~
{: .language-r}



~~~
[1] 373.15
~~~
{: .output}



~~~
kelvin_to_celsius(273.15)
~~~
{: .language-r}



~~~
[1] 0
~~~
{: .output}

> ## Creating a Package for Distribution
>
> 1. Create some new functions for your tempConvert package to convert from Kelvin to Fahrenheit or from Celsius to Kelvin or Fahrenheit.
> 2. Create a package for our `analyze` function so that it will be easy to load when more data arrives.
> 
> > ## Solution
> >
> > ~~~
> > #' Converts Kelvin to Fahrenheit
> > #'
> > #' This function converts input temperatures in Kelvin to Fahrenheit.
> > #' @param temp_K The temperature in Kelvin.
> > #' @return The temperature in Fahrenheit.
> > #' @export
> > #' @examples
> > #' kelvin_to_fahrenheit(273.15)
> > 
> > kelvin_to_fahrenheit <- function(temp_K) {
> >   temp_F <- ((temp_K - 273.15) * (9/5)) + 32
> >   temp_F
> > }
> > ~~~
> > {: .language-r}
> >
> > ~~~
> > #' Converts Celsius to Kelvin
> > #'
> > #' This function converts input temperatures in Celsius to Kelvin.
> > #' @param temp_C The temperature in Celsius.
> > #' @return The temperature in Kelvin.
> > #' @export
> > #' @examples
> > #' celsius_to_kelvin(0)
> > 
> > celsius_to_kelvin <- function(temp_C) {
> >   temp_K <- temp_C + 273.15
> >   temp_K
> > }
> > ~~~
> > {: .language-r}
> > 
> > ~~~
> > #' Converts Celsius to Fahrenheit
> > #'
> > #' This function converts input temperatures in Celsius to Fahrenheit.
> > #' @param temp_C The temperature in Celsius.
> > #' @return The temperature in Fahrenheit.
> > #' @export
> > #' @examples
> > #' celsius_to_fahrenheit(0)
> > 
> > celsius_to_fahrenheit <- function(temp_C) {
> >   temp_F <- (temp_C * 9/5) + 32
> >   temp_F
> > }
> > ~~~
> > {: .language-r}
> {: .solution}
{: .challenge}

{% include links.md %}