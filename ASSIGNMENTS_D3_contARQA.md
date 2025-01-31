---
title: "Continuous Auto-RQA"
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





# Phase Space Reconstruction and Continuous Auto-RQA


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

```
## Registered S3 method overwritten by 'xts':
##   method     from
##   as.zoo.xts zoo
```

![](ASSIGNMENTS_D3_contARQA_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

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

![](ASSIGNMENTS_D3_contARQA_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

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

![](ASSIGNMENTS_D3_contARQA_files/figure-html/unnamed-chunk-4-2.png)<!-- -->

```r
# Assign the optimal parameters
emLag <- params$optimLag
emDim <- params$optimDim

# Embed the time series
lz_emb <- ts_embed(y=lz,emLag = emLag, emDim = emDim)
rgl::plot3d(lz_emb,type="l")
```


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
##   1.972   0.020   1.998 
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

![](ASSIGNMENTS_D3_contARQA_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

```r
# Thresholded by the radius
RMth <- di2bi(RM,emRad = emRad)

rp_plot(RMth, plotDimensions = TRUE, plotMeasures = TRUE)
```

![](ASSIGNMENTS_D3_contARQA_files/figure-html/unnamed-chunk-6-2.png)<!-- -->


### Distinguish between Logistic Map and White noise? {.tabset .tabset-fade .tabset-pills}

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

We'll analyse a time series recorded from a subject who is tracing a circle using a computer mouse:   [circle tracing data](https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/tree/master/assignments/assignment_data/RQA_circletrace).


#### Questions {-}

* Perform an RQA on one of the coordinate series (x or y)
* Study what happens to the RQA measures if you shuffle the temporal order.
* Package `fractal` contains a function `surrogate`. This will create so-called *constrained* realisations of the time series. You can look at the vignette in package `casnet` `cl_CRQA` ([also available in Github](https://github.com/FredHasselman/casnet/tree/master/inst/doc)) Look at the help pages of the function, or study the *Surrogates Manual* of the [TISEAN software](http://www.mpipks-dresden.mpg.de/~tisean/Tisean_3.0.1/index.html) and create two surrogate series, one based on `phase` and one on `aaft`.
     + Look at the RQA measures and think about which $H_0$ should probably be rejected.
     + If you want to be more certain, you'll have to create a test (more surrogates). The [TISEAN manual](http://www.mpipks-dresden.mpg.de/~tisean/Tisean_3.0.1/docs/surropaper/node5.html#SECTION00030000000000000000) provides all the info you need to construct such a test:
     
     > "For a minimal significance requirement of 95\% , we thus need at least 19 or 39 surrogate time series for one- and two-sided tests, respectively. The conditions for rank based tests with more samples can be easily worked out. Using more surrogates can increase the discrimination power."
  


### EXTRA: Convergent cross mapping

Have a look at the package [`rEDM`, you can use it to do a convergent cross mapping analysis](https://cran.r-project.org/web/packages/rEDM/vignettes/rEDM-tutorial.html)



[Sugihara, G., May, R., Ye, H., Hsieh, C. H., Deyle, E., Fogarty, M., & Munch, S. (2012). Detecting causality in complex ecosystems. science, 338(6106), 496-500.](https://science.sciencemag.org/content/338/6106/496)
