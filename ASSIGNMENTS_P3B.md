---
title: "DCS Assignments session 6"
author: "Fred Hasselman & Maarten Wijnants"
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
    pandoc_args: ["--number-offset","5"]
editor_options: 
  chunk_output_type: console
---





# **Quick Links** {-}

* [Main Assignments Page](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/)


# About (Cross-) Recurrence Quantification Analysis in `R`

You can use `R` to run `(C)RQA` analyses and these assignments assume you'll use `R`, but you can also find a `Matlab` toolbox for `(C)RQA` here: [CRP toolbox](http://tocsy.pik-potsdam.de/CRPtoolbox/), there are `Python` libraries as well, see [this page](http://www.recurrence-plot.tk/programmes.php) for an overview of software. 

There are several `R` packages which can perform (C)RQA analysis, we will not use [`crqa`](https://cran.r-project.org/web/packages/crqa/index.html), but the functions in package [`casnet`](https://fredhasselman.github.io/casnet/).

\BeginKnitrBlock{rmdimportant}<div class="rmdimportant"> 
The package `crqa()` was mainly designed to run categorical Cross-Recurrence Quantification Analysis (see [Coco \& Dale (2014)](http://journal.frontiersin.org/Journal/10.3389/fpsyg.2014.00510/abstract) and for R code see appendices in [Coco & Dale (2013)](http://arxiv.org/abs/1310.0201)). The article is a good reference for RQA analysis.</div>\EndKnitrBlock{rmdimportant}

To do the assignments you'll need tpo install the latest version of [`casnet`](https://fredhasselman.github.io/casnet/) from Github!

**NOTE:** Package *invctr 0.1.0* has been accepted for distribution on CRAN, so you don't  have to install it seperately from Github anymore. See: https://cran.r-project.org/web/packages/invctr/index.html 


```r
# Install casnet if necessary: https://github.com/FredHasselman/casnet
# !!Warning!! Very Beta...
# library(devtools)
# devtools::install_github("FredHasselman/casnet")
library(casnet)
library(rio)
```


Package [`casnet`](https://fredhasselman.github.io/casnet) has 2 functions that will perform auto- and cross-recurrence quantification analyses:         

* Function `crqa_cl()` uses a precompiled program that is run from the command line (see [command line recurrence plot tools](http://tocsy.pik-potsdam.de/commandline-rp.php)). It will take as input one or two timeseries and construct a recurrence matrix and is very fast compared to other methods. The first time you run the command `crqa_cl()` the `casnet` package will try to download the correct executables for your operating system, look for messages in the console to find out if the script succeeded. The output of `crqa_cl()` will be similar to the [CRP toolbox](http://tocsy.pik-potsdam.de/CRPtoolbox/) and [`Python` libraries](http://www.recurrence-plot.tk/programmes.php) by Norbert Marwan.
  - If downloading or running `crqa_cl()` fails, this is usually because you do not have enough rights to execute a command line program on your machine. In that case, figure out how to get those rights, or use `crqa_rp()`.
  - `casnet` has a vignette demonstrating usage of `crqa_cl()`, see e.g. https://fredhasselman.github.io/casnet/articles/cl_RQA.html 
* Function `crqa_rp()` assumes you have already contructed a recurrence matrix using function `rp()`. The output of `crqa_rp()` contains more measures than `crqa_cl()` some of which are still experimental.



## Categorical CRQA and the Diagonal Recurrence Profile {.tabset .tabset-fade .tabset-pills}


In 2008 there was some commotion about a speech held by Barack Obama in Milwaukee ([news item](https://www.cbsnews.com/news/obama-accused-of-plagiarism-in-speech/)). According to the media the speech was very similar to a speech held the gouverneur of Massachusetts (Deval Patrick) some years earlier. Here are some fragments of the speeches,

Obama (2008):

> Don't tell me words don't matter. "I have a dream." Just words? "We hold these truths to be self-evident, that all men are created equal." Just words? "We have nothing to fear but fear itself"—just words? Just speeches?

Patrick (2006): 

> "We hold these truths to be self-evident, that all men are created equal." Just words—just words! "We have nothing to fear but fear itself." Just words! "Ask not what your country can do for you, ask what you can do for your country." Just words! "I have a dream." Just words!



```r
# Load the speeches as timeseries

speeches <- rio::import("/Users/fred/ownCloud/Onderwijs/Dynamics of Complex Systems 2018-2019/session 6_RQA and CRQA/Obama.csv")

speeches <- plyr::colwise(as.numeric)(speeches)
```

```
## Warning in lapply(structure(list(V1 = c("5", "2", "3", "36", "5", "6",
## "7", : NAs introduced by coercion
```

```r
speeches$V1[is.na(speeches$V1)] <- -2

colnames(speeches) <- c("Obama","Patrick")

# speeches <- rio::import("https://raw.githubusercontent.com/FredHasselman/The-Complex-Systems-Approach-Book/master/assignments/assignment_data/RQA_circletrace/CRQA_categorical.csv")
```


### Questions {-}


* Plot both time series. 

* The coding assigned a `-2` to words that do not occur in Patrick's speech (instead of assigning them a unique code).
    - Do you think this has an effect on the CRQA measures?
    - What & why?

* Because both fragments weren't of equal size, one series is "filled" at the end with the value `0`
    -  Do you think this has an effect on the CRQA measures? 
    - What & why?

* Perform a categorical CRQA:
   - First create a cross recurrence matrix using function `rp()`. 
   - Look at the manual page what the values mean, look at last the categorical RQA assignment for the values of `emLag`, `emDim` and `emRad`
   - Get recurrence measures using `crqa_rp()` (ignore the warning),
   - Plot the recurrence plot using `rp_plot()`
   - Also look at the diagonal recurrence profile using `crqa_diagProfile()`
   
* What is your opinion about the similarity of these speeches?


### Answers {-}

* Adding a value which occurs in just 1 series (whether it is `-2`, or `0`), but not the other makes sure that value will not be recurring in a Cross Recurrence Plot



```r
# Plot the speech series
plot(ts(speeches))

# Cross recurrence matrix
RM <- rp(y1 = speeches$Obama, y2 = speeches$Patrick, emDim = 1, emLag = 1, emRad = 0)

# Measures
crqa_out <- crqa_rp(RM)
```

```
## Warning in max(freqvec_vl, na.rm = TRUE): no non-missing arguments to max;
## returning -Inf
```

```
## Warning in max(freqvec_hl, na.rm = TRUE): no non-missing arguments to max;
## returning -Inf
```

```r
# Plot
rp_plot(RM, plotDimensions = TRUE, plotMeasures = TRUE)
```

```
## Warning in max(freqvec_vl, na.rm = TRUE): no non-missing arguments to max;
## returning -Inf

## Warning in max(freqvec_vl, na.rm = TRUE): no non-missing arguments to max;
## returning -Inf
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-4-1.png)<!-- -->![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-4-2.png)<!-- -->

```r
# Diagonal profile
crqa_diagProfile(RM,diagWin = 40)
```

```
## 
## Profile 1
```

```
## Warning in crqa_diagProfile(RM, diagWin = 40): NAs introduced by coercion
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-4-3.png)<!-- -->


## Continuous CRQA and the Diagonal Profile {.tabset .tabset-fade .tabset-pills}


### Questions {-}

* Create two sinewave variables for CRQA analysis and use the $x$ and $y$ coordinates of the circletracing data:


```r
y1 <- sin(1:500*2*pi/67)
y2 <- sin(.01*(1:500*2*pi/67)^2)

# Here are the circle trace data
xy <- import("https://raw.githubusercontent.com/FredHasselman/The-Complex-Systems-Approach-Book/master/assignments/assignment_data/RQA_circletrace/mouse_circle_xy.csv") 

circle_x <- xy$x[1:500]
circle_y <- xy$y[1:500]
```

* You have just created two sine(-like) waves. We’ll examine if and how they are coupled in a shared phase space. 
   + As a first step plot them.
   + Also plot the cirlce trace state space

* Find a common embedding delay and an embedding dimension (if you calculate an embedding dimension for each signal separately, as a rule of thumb use the highest embedding dimension you find in further analyses).

* We can now create an unthresholded cross recurrence matrix. 
    + use function `rp()`
    + Fill in the values you decided on for embedding delay and embedding dimension
    
* Run the CRQA using `crqa_cl()` using th time series as input, or `crqa_rp()` using a thresholded matrix as input.
    + You can find a radius automatically, look in the `casnet` manual for function `crqa_radius()`. 
    + Most functions, like `rp_plot()` will call `crqa_radius()` if no radius is provided in the argume t `emRad`.
    + Request a radius which will give us about 5\% recurrent points (default setting).
    + You can create a thresholded matrix by calling function `di2bi()`,
* Produce a plot of the recurrence matrix using `rp_plot()`. Look at the manual pages.

* Can you understand what is going on? 
  + For the simulated data: Explain the the lack of recurrent points at the beginning of the time series.
  + For the circle trace: How could one see these are not deterministic sine waves?

* Examine the synchronisation under the diagonal LOS. Look in the manual of `crqa_diagProfile()`. More elaborate explanation can be found in [Coco & Dale (2014)](http://journal.frontiersin.org/Journal/10.3389/fpsyg.2014.00510/abstract). 
  + To get the diagonal profile from a recurrence matrix `crqa_diagProfile(RM, winDiag = , ...)`. Where `winDiag` is will indicate the window size `-winDiag ... 0 ... +winDiag`.
  + How far is the peak in RR removed from 0 (Line of Synchronisation)? 

* To check whether the patterns are real by conducting a Cross-RQA with a shuffled/surrogate version of e.g. time series $y2$.
   + Function `crqa_diagProfile()` can do a shuffled analysis, check the manual pages. 
   + Use `Nshuffle = 1` (or wait a long time)

> NOTE: If you generate surrogate timeseries, make sure the RR is the same for all surrogates. Try to keep the RR in the same range by using.


### Answers {-}


* You have just created two sine(-like) waves. We’ll examine if and how they are coupled in a shared phase space. 
   + As a first step plot them.
   + Also plot the cirlce trace state space


```r
library(rio)
y1 <- sin(1:1000*2*pi/67)
y2 <- sin(.01*(1:1000*2*pi/67)^2)

# Here are the circle trace data
xy <- import("https://raw.githubusercontent.com/FredHasselman/The-Complex-Systems-Approach-Book/master/assignments/assignment_data/RQA_circletrace/mouse_circle_xy.csv") 

circle_x <- xy$x[1:1000]
circle_y <- xy$y[1:1000]

# Time series
plot(cbind(ts(y1),ts(y2)))
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

```r
plot(cbind(ts(circle_x),ts(circle_y)))
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-6-2.png)<!-- -->

```r
# State space
plot(y1,y2,type="l", pty="s",main = "Sine waves")
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-6-3.png)<!-- -->

```r
plot(circle_x,circle_y,type="l", pty="s",main = "Mouse Circle Trace")
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-6-4.png)<!-- -->

* Find a common embedding delay and an embedding dimension (if you calculate an embedding dimension for each signal separately, as a rule of thumb use the highest embedding dimension you find in further analyses).


```r
params_y1 <- crqa_parameters(y1)
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

```r
params_y2 <- crqa_parameters(y2)
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-7-2.png)<!-- -->

> The diagnostic plot of the sine wave `y1` looks a bit odd. This is due to the fact that mutual information will discretize the time series, and the sine will  fill all bins with the same values, because it is perfectly repeating itself. We'll use emDim = 2 and lag = 1


```r
emLag <- 1
emDim <- 2

RM <- rp(y1=y1, y2=y2,emDim = emDim, emLag = emLag)

# Unthresholded Matrix
rp_plot(RM, plotDimensions = TRUE)
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

```r
# Thresholded Matrix, based on 5% RR
emRad <- crqa_radius(RM = RM, targetValue = .05)$Radius
```

```
## 
## Searching for a radius that will yield 0.05 for RR
```

```
## 
## Converged! Found an appropriate radius...
```

```r
RP <- di2bi(RM, emRad = emRad)
rp_plot(RP, plotDimensions = TRUE)
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-8-2.png)<!-- -->

```r
# The diagonal profile
drp <- crqa_diagProfile(RP, diagWin = 50)
```

```
## 
## Profile 1
```

```
## Warning in crqa_diagProfile(RP, diagWin = 50): NAs introduced by coercion
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-8-3.png)<!-- -->

```r
# Compare CRQA with one randomly shuffled series
drpRND <- crqa_diagProfile(RP, diagWin = 50, doShuffle = TRUE, y1 = y1, y2 = y2, Nshuffle = 19)
```

```
## Calculating diagonal recurrence profiles... 
## 
## Profile 1
## Profile 2
## Profile 3
## Profile 4
## Profile 5
## Profile 6
## Profile 7
## Profile 8
## Profile 9
## Profile 10
## Profile 11
## Profile 12
## Profile 13
## Profile 14
## Profile 15
## Profile 16
## Profile 17
## Profile 18
## Profile 19
## Profile 20
```

```
## Warning in crqa_diagProfile(RP, diagWin = 50, doShuffle = TRUE, y1 = y1, :
## NAs introduced by coercion
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-8-4.png)<!-- -->



```r
params_cx <- crqa_parameters(circle_x)
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

```r
params_cy <- crqa_parameters(circle_y)
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-9-2.png)<!-- -->

> The circle trace data can be embedded in 3 dimensions. This is not so strange, even though the circle was drawn in 2 dimensions, there are also speed/acceleration and accuracy constraints.



```r
emLag <- median(params_cx$optimLag, params_cy$optimLag)
emDim <- max(params_cx$optimDim, params_cy$optimDim)

RM <- rp(y1=circle_x, y2=circle_y,emDim = emDim, emLag = emLag)

# Unthresholded Matrix
rp_plot(RM, plotDimensions = TRUE)
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

```r
# Thresholded Matrix, based on 5% RR
emRad <- crqa_radius(RM = RM, targetValue = .05)$Radius
```

```
## 
## Searching for a radius that will yield 0.05 for RR
```

```
## 
## Converged! Found an appropriate radius...
```

```r
RP <- di2bi(RM, emRad = emRad)
rp_plot(RP, plotDimensions = TRUE)
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-10-2.png)<!-- -->

```r
# The diagonal profile
drp <- crqa_diagProfile(RP, diagWin = 100)
```

```
## 
## Profile 1
```

```
## Warning in crqa_diagProfile(RP, diagWin = 100): NAs introduced by coercion
```

![](ASSIGNMENTS_P3B_files/figure-html/unnamed-chunk-10-3.png)<!-- -->



