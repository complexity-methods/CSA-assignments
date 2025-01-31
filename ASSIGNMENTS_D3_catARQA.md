---
title: "Categorical Auto-RQA"
author: "Fred Hasselman"
date: "1/14/2019"
output: 
  bookdown::html_document2: 
    variant: markdown+hard_line_breaks
    fig_caption: yes
    highlight: pygments
    keep_md: yes
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
    pandoc_args: ["--number-offset","2"]
editor_options: 
  chunk_output_type: console
---





# (Cross-) Recurrence Quantification Analysis Software

There are several packages in `R` you could use to do `(C)RQA` analyses, but you can also find a `Matlab` toolbox for `(C)RQA` here: [CRP toolbox](http://tocsy.pik-potsdam.de/CRPtoolbox/), there are `Python` libraries as well, see [this page](http://www.recurrence-plot.tk/programmes.php) for an overview of software. We will use the functions in package `casnet`.

\BeginKnitrBlock{rmdimportant}<div class="rmdimportant"> The package `crqa()` was mainly designed to run categorical Cross-Recurrence Quantification Analysis (see [Coco & Dale (2014)](http://journal.frontiersin.org/Journal/10.3389/fpsyg.2014.00510/abstract) and for R code see appendices in [Coco & Dale (2013)](http://arxiv.org/abs/1310.0201)). The article is a good reference work for details on how to conduct RQA analysis.</div>\EndKnitrBlock{rmdimportant}


You'll need the latest version of `casnet`:


```r
# Install casnet if necessary: https://github.com/FredHasselman/casnet
# !!Warning!! Very Beta...
# library(devtools)
# devtools::install_github("FredHasselman/invctr")
# devtools::install_github("FredHasselman/casnet")
library(invctr)
library(casnet)
library(rio)
```


Package [`casnet`](https://fredhasselman.github.io/casnet) has 2 functions that will perform auto- and cross-recurrence quantification analyses:         

* Function `crqa_cl()` uses a precompiled program that is run from the command line (see [command line recurrence plot tools](http://tocsy.pik-potsdam.de/commandline-rp.php)). It will take as input one or two timeseries and construct a recurrence matrix and is very fast compared to other methods. The first time you run the command `crqa_cl()` the `casnet` package will try to download the correct executables for your operating system, look for messages in the console to find out if the script succeeded. The output of `crqa_cl()` will be similar to the [CRP toolbox](http://tocsy.pik-potsdam.de/CRPtoolbox/) and [`Python` libraries](http://www.recurrence-plot.tk/programmes.php) by Norbert Marwan.
  - If downloading or running `crqa_cl()` fails, this is usually because you do not have enough rights to execute a command line program on your machine. In that case, figure out how to get those rights, or use `crqa_rp()`.
* Function `crqa_rp()` assumes you have already contructed a recurrence matrix using function `rp()`. The output of `crqa_rp()` contains more measures than `crqa_cl()` some of which are still experimental.


## **Categorical Auto-RQA** 


### RQA by 'hand' {.tabset .tabset-fade .tabset-pills}

Let's start with a small time series... open and/or download this Google sheet: https://docs.google.com/spreadsheets/d/1k4FYDbszfCReCM5_wcw6NIEgSFtTPIy3qmf-kFiZjy8/edit?usp=sharing 

The spreadsheet displays a short categorical time series of 5 observed categories labelled `A` through `E`.
Below the time series a matrix is displayed with zeroes in the upper triangle. The columns and rows of the matrix are labelled according to the categorical time series **y** in `B2:B13`, starting in the lower left corner. The *Line Of Incidence* (LOI) is the diagonal of the matrix going from the lower left corner to the upper right corner. Like the diagonal of a correlation matrix, the LOI in Auto-RQA will contain only recurrent points (`1`), because that's where the column labels will be identical to the row labels.


#### Questions {-}

Because this is Auto-RQA and we are comparing the time series to itself, the recurrence matrix will be symmetrical, so we only have to look at the upper triangle.

* Fill the upper triangle with recurrent points by changing a `0` into a `1`
  - Start with the label in cell `C29` (**A**) and look upwards (`C27` to `C17`). If you see the value **A** recurring, change the `0` into a `1`
   - Proceed to `D29` and find recurrences of the value **B** in the future (in `D26` to `D17`)
   - Continue to fill the upper triangle
* Count the number of `1`s, the recurrent points (`RN`) and put the value in cell `B32` (*Total*)
* Count how many recurrent points are part of a vertical line, and how many are on a diagonal line, put the values in cell `B33` and `B34` respectively.
* Diagonal line lengths:
  - Determine the frequency of occurrence of specific lengths of diagonal lines, record the values in cells `Q17` to `Q20`
  - Record the maximal diagonal line length in `V22`
  - The mean of the lengths of diagonal lines (*L*) will be calculated in cell `V21`
* Vertical line lengths: 
  - Determine the frequency of occurrence of specific lengths of vertical lines, record the values in cells `Q24` to `Q27`
  - Record the maximal vertical line length in `V27`
  - The mean of the lengths of vertical lines (*Trapping Time*) will be calculated in cell `V21`
* Several values are auto-calculated, if you click on the cell you can see how this is done, by looking at the formula bar.
  - Can you understand the calculation of the number of possible recurrent points in `X17`?
* Suppose you randomise the time series... which of the following values do you think would change, and in what way (larger/smaller)
  - Recurrence Rate (`RR`)
  - Mean diagonal line length (`L`, or `MEAN_dl`)
  - Maximum diagonal line length (`L_max`, or `MAX_dl`)
  - Entropy of distribution of diagonal line lengths (`L_entr`, or `ENT_dl`)

#### Answers {-}

* The correct matrix is on the second tab of the spreadsheet.
* The calculation of the number of possible recurrent points in cell `X17` is the size of the matrix (12*12) divided by 2 (because we are only looking at the upper triangle), minus the length of the diagonal, because these aren't actually recurrent points.
* Which of the following values do you think would change if `y` is randomised?
  - Recurrence Rate (`RR`) - **No change**, none of the values are changed, so the number of values that will recur is the unchanged.
  - Mean diagonal line length (`L`, or `MEAN_dl`) - **Likely to be lower**, shuffling will likely break up line structures, but if there are just a few categories and/or the time series is short, it could be the same or higher.
  - Maximum diagonal line length (`L_max`, or `MAX_dl`) - **Very likely to be lower**, shuffling will likely break up line structures, but if there are just a few categories and/or the time series is short, it could be the same or higher.
  - Entropy of distribution of diagonal line lengths (`L_entr`, or `ENT_dl`) - - **Difficult to predict**, shuffling will break up line structures and could increase entropy (more different line lengths), but it is also possible that ther will be only lines of length 2 or 3 left after randomisation, this will be a very homogeneous distribution which has a high entropy. This is more likely if there are just a few categories and/or the time series is short.


### RQA and executive control

`casnet` comes with a vignette demonstrating the usage of `crqa_cl()`, see https://fredhasselman.github.io/casnet/articles/cl_RQA.html, or the manual pages. 

* Study the vignette and try to understand the conclusion based on the surrogate tests.



