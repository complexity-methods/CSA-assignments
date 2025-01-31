---
title: "An Introduction to Analytic Toolbox of Complexity Science"
author: "Fred Hasselman & Maarten Wijnants"
date: "1/14/2019"
output: 
  bookdown::html_document2: 
    fig_caption: yes
    highlight: pygments
    keep_md: yes
    self_contained: no
    number_sections: yes
    theme: spacelab
    toc: yes
    toc_float: true
    collapsed: false
    smooth_scroll: true
    code_folding: show
    bibliography: [refs.bib, packages.bib]
    biblio-style: apalike
    csl: apa.csl
    includes:
        before_body: assignmentstyle.html
---






# **Assignments for the Complex Systems Approach Book** {-}

These assignments were designed to prepare you for "real world" modelling and data analysis problems. That is, after completing the assignments you should be able to decide whether the phenomenon you study could benefit from a complex systems approach and which type of analyses would be a good place to start. The models and techniques discussed here are **not** a definite collection of available techniques, this is really just the tip of the iceberg.

### General Guidelines {-}

* Read the instructions carefully.
* Do not skip any of the steps.
* Do not copy-paste from the assignment text into a spreadsheet or syntax editor (except for text in `code blocks`).
* Study the solutions and lecture notes.





## **Four main topics** {-}

**I. Introduction to the mathematics of change**

- Modelling (nonlinear) growth and Deterministic Chaos ([Lecture 1 - Assignments](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/ASSIGNMENTS_P1A.html))
- Multivariate systems: Predator-Prey dynamics ([Lecture 2 - Assignments](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/ASSIGNMENTS_P1A.html))
- Nonlinear regression: Fitting parameters of analytic solutions. Potential functions ([Lecture 2 - Assignments](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/ASSIGNMENTS_P1B.html))

**II. Time Series Analysis: Temporal Correlations and Fractal Scaling**

- Basic timeseries analysis: Quantifying order (auto/cross correlation functions) and disorder (sample entropy, relative roughness) in time series ([Lecture 3 - Assignments]((https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/ASSIGNMENTS_P2.html))
- Scaling phenomena I: How to measure complexity? Fluctuation analyses (DFA, SDA and spectral slope) and global fractal dimension ([Lecture 4 - Assignments](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/ASSIGNMENTS_P2.html))
- Scaling phenomena II: Global versus local fractal dimension, fractal physiology and complexity matching ([Lecture 5 - Assignments](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/ASSIGNMENTS_P2.html))

**III. Quantifying Recurrences in State Space**

- Takens’ Theorem and State-Space reconstruction ([Lecture 6 - Assignments](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/ASSIGNMENTS_P3.html))
- Recurrence Quantification Analysis (RQA) of continuous and categorical data ([Lecture 6 - Assignments](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/ASSIGNMENTS_P3.html))
- Cross-Recurrence Quantification Analysis (CRQA) of dyadic interaction ([Lecture 7 - Assignments](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/ASSIGNMENTS_P3.html))
- Early Warning Signals of order transitions in complex systems ([Lecture 7 - Assignments](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/ASSIGNMENTS_P3.html))

**IV. Complex Networks**

- Small-world and Scale-free networks ([Lecture 8 - Assignments](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/ASSIGNMENTS_P4.html))
- Symptom networks and Networks of Quantified Recurrences ([Lecture 8 - Assignments ](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/ASSIGNMENTS_P4.html))
- Early Warning Signals of order transitions in clinical settings ([Lecture 8 - Assignments](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/ASSIGNMENTS_P4.html))


## Using `R`! {-}

We recommend installing the latest version of [**R**](https://www.r-project.org) and [**RStudio**](https://www.rstudio.com). Rstudio is not strictly necessary, but especially new users will have a somewhat more comfortable expe**R**ience. If you are completeley new to `R` you might want to [check these notes in the course book](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/some-notes-on-using-r.html)


### Packages needed for the assignments {-}

If you are working from a private computer, you'll need to install the packages below, just copy and paste the command in `R`. Depending on your computer and internet connection, this might take a while to complete. If you run into any errors, skip the package and try the others, e.g. by adding them through the user interface of **Rstudio** (`Tools >> Installl Packages...`).

These packages are already installed in the computer rooms at the university, except for our custom package `casnet`.


```r
# If you install casnet using the code below, you will not need to run this code below
# install.packages(c("devtools", "rio","plyr", "tidyverse","Matrix", "ggplot2", "lattice", "latticeExtra", "grid", "gridExtra", "scales", "dygraphs","rgl", "plot3D","fractal", "nonlinearTseries", "crqa","signal", "sapa", "ifultools", "pracma", "nlme", "lme4", "lmerTest", "minpack.lm", "igraph","qgraph","graphicalVAR","bootGraph","IsingSampler","IsingFit"), dependencies = TRUE)

# Install casnet if necessary: https://github.com/FredHasselman/casnet
# !!Warning!! Very Beta...
# Auto config of Marwan's commandline rqa works on Windows, MacOS and probably Linux as well
library(devtools)
devtools::install_github("FredHasselman/invctr")
devtools::install_github("FredHasselman/casnet")
```

> NOTE: Sometimes R will ask whether you want to install a newer version of a package which still has to be built from the source code. I would suggest to select NO, because this will take more time and might cause problems. 


### Files on GitHub {-}

All the files (data, scripts and files that generated this document) are in a repository on [Github](https://github.com/FredHasselman/The-Complex-Systems-Approach-Book). Github keeps track of all the different versions of the files in a repository.

* If you want to download a file that is basically a text file (e.g. an `R` script), find a button on the page named `raw`, press it and copy the text in your browser, or save it as a text file.
* For non-text files, a `download` button will be present somewhere on the page.

The main package we use will be `casnet` the [**Complex Adaptive Systems and NETworks**](https://github.com/FredHasselman/casnet) toolbox. Here is a nice page which contains all the manual entries https://fredhasselman.github.io/casnet/index.html. 

If this toolbox for some reason does install correctly you can try to download and source the file containing most of the functions we'll use.
First, download the files `nlRtsa_SOURCE.R` and `prepare_command_line_crp.R` from [Github](https://github.com/FredHasselman/casnet/R) and type `source('nlRtsa_SOURCE.R')` and `source('prepare_command_line_crp.R')` in `R`. Alternatively, source the directly from Github if you have package `devtools` installed.

```r
# Only use this if the regular installation procedure does not work!!!
library(devtools)
source_url("https://raw.githubusercontent.com/FredHasselman/casnet/master/R/prepare_command_line_crp.R")
source_url("https://raw.githubusercontent.com/FredHasselman/casnet/master/R/nlRtsa_SOURCE.R")
```

### Working with timeseries in `R` {-}

There are many different ways to handle and plot timeseries data in `R`, I summarised some of them [in the course book](https://darwin.pwo.ru.nl/skunkworks/courseware/1718_DCS/plotTS.html)
