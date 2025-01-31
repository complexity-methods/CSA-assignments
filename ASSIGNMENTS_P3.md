---
title: "DCS Assignments session 5"
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
    pandoc_args: ["--number-offset","4"]
editor_options: 
  chunk_output_type: console
---





# **Quick Links** {-}

* [Main Assignments Page](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/)


# About (Cross-) Recurrence Quantification Analysis in `R`

You can use `R` to run `(C)RQA` analyses and these assignments assume you'll use `R`, but you can also find a `Matlab` toolbox for `(C)RQA` here: [CRP toolbox](http://tocsy.pik-potsdam.de/CRPtoolbox/), there are `Python` libraries as well, see [this page](http://www.recurrence-plot.tk/programmes.php) for an overview of software. 

There are several `R` packages which can perform (C)RQA analysis, we will not use [`crqa`](https://cran.r-project.org/web/packages/crqa/index.html), but the functions in package `casnet`.

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



## **Phase Space Reconstruction** 


Here is a great video summary of the phase space reconstruction technique: https://youtu.be/6i57udsPKms


\BeginKnitrBlock{rmdnote}<div class="rmdnote">**R-packages for Phase Space Reconstruction**

Package `casnet` relies on packages `fractal` and `rgl` to reconstruct phase space. Package `rgl` is used for plotting and can cause problems on some windows machines. On windows You'll need to check if you have the [X Window System](http://www.x.org/wiki/) for interactive 3D plotting. This Linux desktop system comes installed in some way or form on most Mac and Windows systems. You can test if it is present by running `rgl::open3d()` in `R`, which will try to open an interactive plotting device.</div>\EndKnitrBlock{rmdnote}


### Reconstruct the Lorenz attractor {.tabset .tabset-fade .tabset-pills}

Package `fractal` includes the 3 dimensions of the Lorenz system in the chaotic regime. Run this code `rgl::plot3d(lorenz,type="l")` with all three packages loaded to get an interactive 3D plot of this strange attractor.

We'll reconstruct the attractor based on just dimension `X` of the system using the data from package `fractal`, and functions from package `casnet`. Don't forget: *always look at the manual pages of a function* you are running.

\BeginKnitrBlock{rmdimportant}<div class="rmdimportant">Package `fractal` and package `nonlinearTseries` use functions with similar names, perhapp better to not load them together, or always use `fractal::` and `nonlinearTseries::` if you want to call the functions direclty.</div>\EndKnitrBlock{rmdimportant}


#### Questions {-}

* Use `lx <- lorenz[1:2048,1]` to reconstruct the phase space based on `lx`. 
    + Find an optimal embedding lag and dimension by calling `crqa_parameters()`.
    + Use `nnThres = .01`. This is the proportion of remaining nearest neighbours below which we consider the number of dimensions optimal. Because this time series was generated by a simulation of a system, we can use a much lower threshold. For real data, which is much noisier, you can use `0.1` or `0.2`.
    + **NOTE:** If you have a slow computer don't use `2048`, but use e.g. `512`.
    
This `crqa_parameters()` function will call several other functions (e.g. `fractal::timeLag()` with `method = "mutual"` and `nonlinearTseries::findAllNeighbours()` for the false nearest neighbourhood search). Look at the manual page, you can run the `crqa_parameters()` function with the defaults settings, it will show a diagnostic plot. Because we have to search over a range of parameters it can take a while to get the results...

* Based on the output:
    + Choose an embedding lag $\tau$ (in `casnet` functions this is the argument `emLag`). Remember, this is just an optimisation choice. Sometimes there is no first minimum, or no global minimum.
    + Find an appropriate embedding dimension by looking when the false nearest neighbours drop below 1\%. 
    
* Now you can use this information to reconstruct the phase space: 
    + Embed the time series using `ts_embed()` (you can also use `fractal::embedSeries()`).
    + Inspect the object that was returned
    + Use  `rgl::plot3d()` to plot the reconstructed space. Plot the reconstructed phase space. (You'll need to use `as.matrix()` on the object created by `fractal::embedSeries()`)

#### Answers {-}

* Use `lx <- lorenz[1:2048,1]` to reconstruct the phase space based on `lx`. 
    + Find an optimal embedding lag and dimension by calling `crqa_parameters()`.


```r
library(rgl)
library(ggplot2)
library(fractal)
```

```
## Loading required package: splus2R
```

```
## Loading required package: ifultools
```

```r
library(invctr)
library(casnet)

# The X data
lx <- lorenz[1:2048,1]

# Search for the parameters,
params <- crqa_parameters(lx)
```

![](ASSIGNMENTS_P3_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

```r
# Assign the optimal parameters
emLag <- params$optimLag
emDim <- params$optimDim
```

* Now you can use this information to reconstruct the phase space


```r
# For X
lx_emb <- ts_embed(y=lx,emLag = emLag, emDim = emDim)

rgl::plot3d(lx_emb, type="l")

# For Y
ly <- lorenz[1:2048,2]

# Search for the parameters,
params <- crqa_parameters(ly)
```

![](ASSIGNMENTS_P3_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

```r
# Assign the optimal parameters
emLag <- params$optimLag
emDim <- params$optimDim

# Embed the time series
ly_emb <- ts_embed(y=ly,emLag = emLag, emDim = emDim)
rgl::plot3d(ly_emb,type="l")

# For Z
lz <- lorenz[1:2048,3]

# Search for the parameters,
params <- crqa_parameters(lz)
```

![](ASSIGNMENTS_P3_files/figure-html/unnamed-chunk-5-2.png)<!-- -->

```r
# Assign the optimal parameters
emLag <- params$optimLag
emDim <- params$optimDim

# Embed the time series
lz_emb <- ts_embed(y=lz,emLag = emLag, emDim = emDim)
rgl::plot3d(lz_emb,type="l")
```


### Reconstruct the Predator-Prey model {.tabset .tabset-fade .tabset-pills}



#### Questions {-}

Use the same procedure as above to reconstruct the state space of the predator-prey system. (Look [at the solutions](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/ASSIGNMENTS_P1A.html#221_foxes_and_rabbits_in_r) to get a `Foxes` or `Rabbit` series).

 * You should get a 2D state space, so 3D plotting might be a bit too much for this system.

#### Answers{-}


```r
library(plyr)
library(ggplot2)
# Get the time series
# Parameters
N  <- 1000
a  <- d <- 1
b  <- c <- 2 
R0 <- F0 <- 0.1
Ra  <- as.numeric(c(R0, rep(NA,N-1)))
Fx  <- as.numeric(c(F0, rep(NA,N-1)))

# Time constant
delta <- 0.01

# Numerical integration of the predator-prey system
l_ply(seq_along(Ra), function(t){
    Ra[[t+1]] <<- (a - b * Fx[t]) * Ra[t] * delta + Ra[t] 
    Fx[[t+1]] <<- (c * Ra[t] - d) * Fx[t] * delta + Fx[t] 
    })

# lets take Foxes and search
params <- crqa_parameters(Fx)
```

![](ASSIGNMENTS_P3_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

```r
# Assign the optimal parameters
emLag <- params$optimLag
emDim <- params$optimDim

# Embed the time series
Fx_emb <- ts_embed(y=Fx,emLag = emLag, emDim = emDim)

# Now take Rabbits and search parameters
params <- crqa_parameters(Ra)
```

![](ASSIGNMENTS_P3_files/figure-html/unnamed-chunk-6-2.png)<!-- -->

```r
# Assign the optimal parameters
emLag <- params$optimLag
emDim <- params$optimDim

# Embed the time series
Ra_emb <- ts_embed(y=Ra,emLag = emLag, emDim = emDim)

# Plot
df.pp <- cbind.data.frame(Foxes = c(Fx, Fx_emb[,1], Ra_emb[,1]), Rabbits = c(Ra, Fx_emb[,2], Ra_emb[,2]), Type = c(rep("Original",N+1), rep("reconstructed Foxes",NROW(Fx_emb)), rep("reconstructed Rabbits",NROW(Ra_emb))))

ggplot(df.pp,aes(x=Foxes, y = Rabbits, group = Type)) +
  geom_path(aes(colour=Type), size=1) +
  theme_bw() +
  coord_equal()
```

![](ASSIGNMENTS_P3_files/figure-html/unnamed-chunk-6-3.png)<!-- -->



## Continuous Auto-RQA


### RQA of the Lorenz attractor {.tabset .tabset-fade .tabset-pills}

Perform an RQA on the reconstructed state space of the Lorenz system. You'll need a radius (also called: threshold, or $\epsilon$) in order to decide which points are close together (recurrent). You have already seen `casnet` provides a function which will automatically select the best parameter settings for phase space reconstruction: `crqa_parameters()`. 


#### Questions {-}

* First get a radius that gives you a fixed recurrence rate use `crqa_radius()` look at the manual pages.

* Best way to ensure you are using the same parameters in each function is to create some lists with parameter settings (check the `crqa_cl()` manual to figure out what these parameters mean)


#### Answers{-}


```r
library(fractal)
library(casnet)

# Lorenz X
lx <- lorenz[1:2048,1]
emLag = 17
emDim = 3

(emRad <- crqa_radius(y1 = lx, emLag = emLag, emDim = emDim)$Radius)
```

```
## 
## Auto-recurrence: Setting diagonal to (1 + max. distance) for analyses
```

```
## lower and upper are both 0 (no band, just diagonal)
##  using: diag(mat) <- 38.5536...
```

```
## 
## Searching for a radius that will yield 0.05 for RR
```

```
## 
## Converged! Found an appropriate radius...
```

```
## [1] 3.629007
```

```r
# RQA analysis
(out <- crqa_cl(lx,emDim = emDim, emLag = emLag, emRad = emRad))
```

```
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
## Performing auto-RQA
## 
## ...using sequential processing...
##   |                                                                       |                                                               |   0%  |                                                                       |o~oo~oo~oo~oo~oo~oo~oo~oo~oo~oo~oo~oo~oo~oo~oo~oo~oo~oo~oo~oo~o| 100%
## 
## Completed in:
##    user  system elapsed 
##   1.848   0.055   1.955 
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```
##                                 .id        RR      DET  DET.RR     LAM
## 1 window: 1 | start: 1 | stop: 2048 0.0499637 0.998963 19.9938 0.99918
##   LAM.DET L_max       L  L_entr         DIV V_max      TT  V_entr      T1
## 1 1.00022  2013 24.6951 3.76081 0.000496771    35 10.1375 2.75883 15.9085
##       T2 W_max  W_mean  W_entr W_prob       F_min emDim emLag    emRad
## 1 175.25  1533 164.849 4.67858     58 0.000652316     3    17 3.629007
##   DLmin VLmin  distNorm
## 1     2     2 EUCLIDEAN
```


We can plot the recurrence matrix...


```r
library(casnet)

# Unthresholded matrix (no radius applied)
RM <- rp(y1 = lx, emDim = emDim, emLag = emLag)

# plot it
rp_plot(RM)
```

![](ASSIGNMENTS_P3_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

```r
# Thresholded by the radius
RMth <- di2bi(RM,emRad = emRad)

rp_plot(RMth, plotDimensions = TRUE, plotMeasures = TRUE)
```

![](ASSIGNMENTS_P3_files/figure-html/unnamed-chunk-8-2.png)<!-- -->


### Distinguish between Logisitc Map and White noise? {.tabset .tabset-fade .tabset-pills}

RQA promises to be able to distinguish between different dynamical regimes, for example dynamics due to different parameter settings of the logistic map, see e.g. https://en.wikipedia.org/wiki/Recurrence_quantification_analysis#Time-dependent_RQA 


#### Questions {-}

Use RQA to compare white noise to the deterministic chaos series of previous assignments.


```r
library(rio)
library(pracma)

# Reload the data
series <- rio::import("https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/raw/master/assignments/assignment_data/BasicTSA_arma/series.sav", setclass = "tbl_df")
```

#### Answers {-}

Discussed in the next session




### RQA of circle-tracing data {.tabset .tabset-fade .tabset-pills}

We'll analyse a time series recorded from asubject who is tracing a circle using a computer mouse:   [circle tracing data](https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/tree/master/assignments/assignment_data/RQA_circletrace).


#### Questions {-}

* Perform an RQA on one of the coordinate series (x or y)
* Study what happens to the RQA measures if you shuffle the temporal order.
* Package `fractal` contains a function `surrogate`. This will create so-called *constrained* realisations of the time series. You can look at the vignette in package `casnet` `cl_CRQA` ([also available in Github](https://github.com/FredHasselman/casnet/tree/master/inst/doc)) Look at the help pages of the function, or study the *Surrogates Manual* of the [TISEAN software](http://www.mpipks-dresden.mpg.de/~tisean/Tisean_3.0.1/index.html) and create two surrogate series, one based on `phase` and one on `aaft`.
     + Look at the RQA measures and think about which $H_0$ should probably be rejected.
     + If you want to be more certain, you'll have to create a test (more surrogates). The [TISEAN manual](http://www.mpipks-dresden.mpg.de/~tisean/Tisean_3.0.1/docs/surropaper/node5.html#SECTION00030000000000000000) provides all the info you need to construct such a test:
     
     > "For a minimal significance requirement of 95\% , we thus need at least 19 or 39 surrogate time series for one- and two-sided tests, respectively. The conditions for rank based tests with more samples can be easily worked out. Using more surrogates can increase the discrimination power."
  

