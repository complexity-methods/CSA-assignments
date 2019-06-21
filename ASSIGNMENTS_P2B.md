---
title: "DCS Assignments Session 4"
author: "Fred Hasselman & Maarten Wijnants"
date: "1/14/2019"
output: 
  bookdown::html_document2: 
    variant: markdown+hard_line_breaks
    fig_caption: yes
    highlight: pygments
    keep_md: yes
    self_contained: yes
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

* [Main Assignments Page](https://darwin.pwo.ru.nl/skunkworks/courseware/1718_DCS/assignments/)


## **Fluctuation analyses**

Install package `casnet` to use the functions `fd_sda()`, `fd_psd()` and `fd_dfa()` and `plotFD_loglog()`.
Check the manual pages for these functions.


```r
# Install casnet if necessary: https://github.com/FredHasselman/casnet
# !!Warning!! Beta version...
# library(devtools)
# devtools::install_github("FredHasselman/invctr")
# devtools::install_github("FredHasselman/casnet")
library(invctr)
library(casnet)
library(rio)
library(plyr)
library(dplyr)
library(ggplot2)
```


We'll use the data from last week:


```r
library(rio)
library(pracma)

# Reload the data
series <- rio::import("https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/raw/master/assignments/assignment_data/BasicTSA_arma/series.sav", setclass = "tbl_df")

TS1 <- rio::import("https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/raw/master/assignments/assignment_data/RelativeRoughness/TS1.xlsx", col_names=FALSE)
TS2 <- rio::import("https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/raw/master/assignments/assignment_data/RelativeRoughness/TS2.xlsx", col_names=FALSE)
TS3 <- rio::import("https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/raw/master/assignments/assignment_data/RelativeRoughness/TS3.xlsx", col_names=FALSE)


# The Excel files did not have any column names, so let's create them in the `data.frame`
colnames(TS1) <- "TS1"
colnames(TS2) <- "TS2"
colnames(TS3) <- "TS3"

# randperm()
TS1Random <- TS1$TS1[randperm(length(TS1$TS1))]

# sample()
TS1Random <- sample(TS1$TS1, length(TS1$TS1))
TS2Random <- sample(TS2$TS2, length(TS2$TS2))
TS3Random <- sample(TS3$TS3, length(TS3$TS3))

TS3Norm <- scale(TS3$TS3)
```


###  Standardised Dispersion Analysis {.tabset .tabset-fade .tabset-pills}


#### Questions {-}

* Common data preparation before running fluctuation analyses like `SDA`:
    + Normalize the time series (using `sd` based on `N`, not `N-1`, e.g. by using `ts_standardise()`, or use function arguments)
    + Check whether time series length is a power of `2`. If you use `N <- log2(length(y))`, the number you get is `2^N`. You need an integer power for the length in order to create equal bin sizes. You can pad the series with 0s, or make it shorter before analysis.
* Perform `sda` on the 3 HRV time series, or any of the other series you already analysed.
* Compare to what you find for `fd_sda` to the other techniques (`fd_RR`,`SampEn`, `acf`, `pacf`)



#### Answers {-}



```r

# ACF assignment data `series`
fdS1 <- fd_sda(series$TS_1, returnPLAW = TRUE)
## 
## 
## fd_sda:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 10)
## Slope = -0.64 | FD = 1.64 
## 
##  Fit range (n = 9)
## Slope = -0.61 | FD = 1.61
## 
## ~~~o~~o~~casnet~~o~~o~~~
fdS2 <- fd_sda(series$TS_2, returnPLAW = TRUE)
## 
## 
## fd_sda:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 10)
## Slope = -0.58 | FD = 1.58 
## 
##  Fit range (n = 9)
## Slope = -0.55 | FD = 1.55
## 
## ~~~o~~o~~casnet~~o~~o~~~
fdS3 <- fd_sda(series$TS_3, returnPLAW = TRUE)
## 
## 
## fd_sda:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 10)
## Slope = -0.64 | FD = 1.64 
## 
##  Fit range (n = 9)
## Slope = -0.58 | FD = 1.58
## 
## ~~~o~~o~~casnet~~o~~o~~~

# If you asked to return the powerlaw (returnPLAW = TRUE) the function plotFD_loglog() can make a plot
plotFD_loglog(fdS1,title = "SDA", subtitle = "Series 1")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-4-1.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-4-2.png)<!-- -->

```r
plotFD_loglog(fdS2,title = "SDA", subtitle = "Series 2")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-4-3.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-4-4.png)<!-- -->

```r
plotFD_loglog(fdS3,title = "SDA", subtitle = "Series 3")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-4-5.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-4-6.png)<!-- -->



**Values of other time series**


```r

# RR assignment data TS1, TS2, TS3 and shuffled versions
# This is just a check so you can see which bin sizes to expect
# It is also possible to force calclulation of specific bin sizes, see the manual pages for more info ?fd_sda
L1 <- floor(log2(length(TS1$TS1)))
L2 <- floor(log2(length(TS2$TS2)))
L3 <- floor(log2(length(TS3$TS3)))

# You can also ask for a plot directly by passing doPlot = TRUE
fdTS1  <- fd_sda(TS1$TS1[1:2^L1], doPlot = TRUE, tsName = "TS1")
## 
## 
## fd_sda:	Sample rate was set to 1.
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-1.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-2.png)<!-- -->

```
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 9)
## Slope = -0.16 | FD = 1.16 
## 
##  Fit range (n = 8)
## Slope = -0.18 | FD = 1.18
## 
## ~~~o~~o~~casnet~~o~~o~~~

fdTS1r <- fd_sda(TS1Random[1:2^L1], doPlot = TRUE, tsName = "TS1 shuffled")
## 
## 
## fd_sda:	Sample rate was set to 1.
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-3.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-4.png)<!-- -->

```
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 9)
## Slope = -0.67 | FD = 1.67 
## 
##  Fit range (n = 8)
## Slope = -0.52 | FD = 1.52
## 
## ~~~o~~o~~casnet~~o~~o~~~


fdTS2 <- fd_sda(TS2$TS2[1:2^L2], returnPLAW = TRUE)
## 
## 
## fd_sda:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 9)
## Slope = -0.04 | FD = 1.04 
## 
##  Fit range (n = 8)
## Slope = -0.06 | FD = 1.06
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS2,subtitle = "TS2")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-5.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-6.png)<!-- -->

```r

fdTS2r <- fd_sda(TS2Random[1:2^L2], returnPLAW = TRUE)
## 
## 
## fd_sda:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 9)
## Slope = -0.54 | FD = 1.54 
## 
##  Fit range (n = 8)
## Slope = -0.58 | FD = 1.58
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS2r,subtitle = "TS2 shuffled")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-7.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-8.png)<!-- -->

```r

fdTS3 <- fd_sda(TS3$TS3[1:2^L3], returnPLAW = TRUE)
## 
## 
## fd_sda:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 9)
## Slope = -0.63 | FD = 1.63 
## 
##  Fit range (n = 8)
## Slope = -0.6 | FD = 1.6
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS3,subtitle = "TS3")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-9.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-10.png)<!-- -->

```r

fdTS3r <- fd_sda(TS3Random[1:2^L3], returnPLAW = TRUE)
## 
## 
## fd_sda:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 9)
## Slope = -0.56 | FD = 1.56 
## 
##  Fit range (n = 8)
## Slope = -0.56 | FD = 1.56
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS3r,subtitle = "TS3 shuffled")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-11.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-12.png)<!-- -->

```r

fdTS3n <- fd_sda(TS3Norm[1:2^L3], returnPLAW = TRUE)
## 
## 
## fd_sda:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 9)
## Slope = -0.63 | FD = 1.63 
## 
##  Fit range (n = 8)
## Slope = -0.6 | FD = 1.6
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS3n,subtitle = "TS3 standardised")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-13.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-14.png)<!-- -->

```r


# Logistic map
y1 <- growth_ac(r = 2.9, type="logistic",N = 1024)
fdL29 <- fd_sda(y1, returnPLAW = TRUE)
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 10)
## Slope = -0.41 | FD = 1.41 
## 
##  Fit range (n = 9)
## Slope = -0.4 | FD = 1.4
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdL29, subtitle = "Logistic Map: r =  2.9")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-15.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-16.png)<!-- -->

```r

y2 <- growth_ac(r = 4, type="logistic", N = 1024)
fdL4 <- fd_sda(y2, returnPLAW = TRUE)
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 10)
## Slope = -0.64 | FD = 1.64 
## 
##  Fit range (n = 9)
## Slope = -0.58 | FD = 1.58
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdL4, subtitle = "Logistic Map: r =  4")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-17.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-5-18.png)<!-- -->



###  Spectral Slope {.tabset .tabset-fade .tabset-pills}


#### Questions {-}

* Common data preparation before running spectral analyses:
    + Normalize the time series (using `sd` based on `N`, not `N-1`)
    + Check whether time series length is a power of `2`. If you use `N <- log2(length(y))`, the number you get is `2^N`. You need an integer power for the length in order to create equal bin sizes. You can pad the series with 0s, or make it shorter before analysis.
* Perform `fd_psd()` on the 3 HRV time series, or any of the other series you already analysed.
* Compare to what you find for `fd_psd` to the other measures (`fd_RR`,`SampEn`, `acf`, `pacf`)


#### Answers {-}



```r

# ACF assignment data `series`
fdS1 <- fd_psd(series$TS_1, doPlot = TRUE)
## 
## 
## fd_psd:	Sample rate was set to 1.
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-6-1.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-6-2.png)<!-- -->

```
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 512)
## Slope = 0.18 | FD = 1.57 
## 
##  Hurvich-Deo (n = 36)
## Slope = 0.29 | FD = 1.61
## 
## ~~~o~~o~~casnet~~o~~o~~~
fdS2 <- fd_psd(series$TS_2, returnPLAW = TRUE)
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 512)
## Slope = -0.27 | FD = 1.4 
## 
##  Hurvich-Deo (n = 79)
## Slope = -0.98 | FD = 1.2
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdS2,subtitle = "Series 2")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-6-3.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-6-4.png)<!-- -->

```r
fdS3 <- fd_psd(series$TS_3, returnPLAW = TRUE)
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 512)
## Slope = 0.12 | FD = 1.54 
## 
##  Hurvich-Deo (n = 32)
## Slope = 0.02 | FD = 1.51
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdS3,subtitle = "Series 3")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-6-5.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-6-6.png)<!-- -->



**Values of other time series**


```r

# This is just a check so you can see which frequency range to expect
# It is also possible to force calclulation of specific bin sizes, see the manual pages for more info ?fd_psd
L1 <- floor(log2(length(TS1$TS1)))
L2 <- floor(log2(length(TS2$TS2)))
L3 <- floor(log2(length(TS3$TS3)))

# RR assignment data TS1, TS2, TS3 and shuffled versions

fdTS1 <- fd_psd(TS1$TS1[1:2^L1], doPlot = TRUE, tsName = "TS1")
## 
## 
## fd_psd:	Sample rate was set to 1.
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-1.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-2.png)<!-- -->

```
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 256)
## Slope = -0.92 | FD = 1.22 
## 
##  Hurvich-Deo (n = 33)
## Slope = -0.53 | FD = 1.32
## 
## ~~~o~~o~~casnet~~o~~o~~~

fdTS1r <- fd_psd(TS1Random[1:2^L1], returnPLAW = TRUE)
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 256)
## Slope = -0.14 | FD = 1.45 
## 
##  Hurvich-Deo (n = 21)
## Slope = -0.43 | FD = 1.35
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS1r,subtitle = "TS1 shuffled")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-3.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-4.png)<!-- -->

```r

fdTS2 <- fd_psd(TS2$TS2[1:2^L2], returnPLAW = TRUE)
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 256)
## Slope = -0.79 | FD = 1.24 
## 
##  Hurvich-Deo (n = 17)
## Slope = -1.12 | FD = 1.18
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS2,subtitle = "TS2")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-5.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-6.png)<!-- -->

```r

fdTS2r <- fd_psd(TS2Random[1:2^L2], returnPLAW = TRUE)
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 256)
## Slope = -0.05 | FD = 1.48 
## 
##  Hurvich-Deo (n = 20)
## Slope = -0.01 | FD = 1.5
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS2r,subtitle = "TS2 shuffled")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-7.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-8.png)<!-- -->

```r

fdTS3 <- fd_psd(TS3$TS3[1:2^L3], returnPLAW = TRUE)
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 256)
## Slope = 0.19 | FD = 1.57 
## 
##  Hurvich-Deo (n = 16)
## Slope = 0.97 | FD = 1.79
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS3,subtitle = "TS3")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-9.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-10.png)<!-- -->

```r

fdTS3r <- fd_psd(TS3Random[1:2^L3], returnPLAW = TRUE)
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 256)
## Slope = -0.01 | FD = 1.5 
## 
##  Hurvich-Deo (n = 27)
## Slope = 0.26 | FD = 1.6
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS3r,subtitle = "TS3 shuffled")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-11.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-12.png)<!-- -->

```r

fdTS3n <- fd_psd(TS3Norm[1:2^L3], returnPLAW = TRUE)
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 256)
## Slope = 0.19 | FD = 1.57 
## 
##  Hurvich-Deo (n = 16)
## Slope = 0.97 | FD = 1.79
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS3n,subtitle = "TS3 standardised")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-13.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-14.png)<!-- -->

```r


# Logistic map
y1 <- growth_ac(r = 2.9, type="logistic",N = 1024)
fdL29 <- fd_psd(as.numeric(y1), returnPLAW = TRUE)
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 512)
## Slope = -1.26 | FD = 1.16 
## 
##  Hurvich-Deo (n = 22)
## Slope = -5.23 | FD = 1.08
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdL29, subtitle = "Logistic Map: r =  2.9")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-15.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-16.png)<!-- -->

```r

y2 <- growth_ac(r = 4, type="logistic", N = 1024)
fdL4 <- fd_psd(as.numeric(y2), returnPLAW = TRUE)
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 512)
## Slope = 0.12 | FD = 1.54 
## 
##  Hurvich-Deo (n = 32)
## Slope = 0.02 | FD = 1.51
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdL4, subtitle = "Logistic Map: r =  4")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-17.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-7-18.png)<!-- -->


###  Detrended Fluctuation Analysis {.tabset .tabset-fade .tabset-pills}


#### Questions {-}

* Data preparation before running `DFA` analyses are not really necessary, except perhaps:
    + Check whether time series length is a power of `2`. If you use `N <- log2(length(y))`, the number you get is `2^N`. You need an integer power for the length in order to create equal bin sizes. You can pad the series with 0s, or make it shorter before analysis.
* Perform `fd_dfa()` on the 3 HRV time series, or any of the other series you already analysed.
* Compare to what you find for `fd_dfa` to the other measures (`fd_RR`,`SampEn`, `acf`, `pacf`)


#### Answers {-}




```r

# ACF assignment data `series`
fdS1 <- fd_dfa(series$TS_1, doPlot = TRUE)
## 
## 
## fd_dfa:	Sample rate was set to 1.
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-8-1.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-8-2.png)<!-- -->

```
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Detrended FLuctuation Analysis 
## 
##  Full range (n = 30)
## Slope = 0.44 | FD = 1.55 
## 
##  Exclude large bin sizes (n = 25)
## Slope = 0.45 | FD = 1.55
## 
## ~~~o~~o~~casnet~~o~~o~~~
fdS2 <- fd_dfa(series$TS_2, returnPLAW = TRUE)
## 
## 
## fd_dfa:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Detrended FLuctuation Analysis 
## 
##  Full range (n = 30)
## Slope = 1.15 | FD = 1.15 
## 
##  Exclude large bin sizes (n = 25)
## Slope = 1.31 | FD = 1.11
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdS2,subtitle = "Series 2")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-8-3.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-8-4.png)<!-- -->

```r
fdS3 <- fd_dfa(series$TS_3, returnPLAW = TRUE)
## 
## 
## fd_dfa:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Detrended FLuctuation Analysis 
## 
##  Full range (n = 30)
## Slope = 0.46 | FD = 1.54 
## 
##  Exclude large bin sizes (n = 25)
## Slope = 0.48 | FD = 1.52
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdS3,subtitle = "Series 3")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-8-5.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-8-6.png)<!-- -->


**Values of other time series**


```r

# This is just a check so you can see which bin sizes to expect
# It is also possible to force calclulation of specific bin sizes, see the manual pages for more info ?fd_dfa
L1 <- floor(log2(length(TS1$TS1)))
L2 <- floor(log2(length(TS2$TS2)))
L3 <- floor(log2(length(TS3$TS3)))

# RR assignment data TS1, TS2, TS3 and shuffled versions

fdTS1 <- fd_dfa(TS1$TS1[1:2^L1], doPlot = TRUE, tsName = "TS1")
## 
## 
## fd_dfa:	Sample rate was set to 1.
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-1.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-2.png)<!-- -->

```
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Detrended FLuctuation Analysis 
## 
##  Full range (n = 30)
## Slope = 0.93 | FD = 1.23 
## 
##  Exclude large bin sizes (n = 25)
## Slope = 0.98 | FD = 1.21
## 
## ~~~o~~o~~casnet~~o~~o~~~

fdTS1r <- fd_dfa(TS1Random[1:2^L1], returnPLAW = TRUE)
## 
## 
## fd_dfa:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Detrended FLuctuation Analysis 
## 
##  Full range (n = 30)
## Slope = 0.58 | FD = 1.44 
## 
##  Exclude large bin sizes (n = 25)
## Slope = 0.54 | FD = 1.47
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS1r,subtitle = "TS1 shuffled")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-3.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-4.png)<!-- -->

```r

fdTS2 <- fd_dfa(TS2$TS2[1:2^L2], returnPLAW = TRUE)
## 
## 
## fd_dfa:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Detrended FLuctuation Analysis 
## 
##  Full range (n = 30)
## Slope = 1.23 | FD = 1.13 
## 
##  Exclude large bin sizes (n = 25)
## Slope = 1.31 | FD = 1.11
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS2,subtitle = "TS2")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-5.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-6.png)<!-- -->

```r

fdTS2r <- fd_dfa(TS2Random[1:2^L2], returnPLAW = TRUE)
## 
## 
## fd_dfa:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Detrended FLuctuation Analysis 
## 
##  Full range (n = 30)
## Slope = 0.47 | FD = 1.53 
## 
##  Exclude large bin sizes (n = 25)
## Slope = 0.5 | FD = 1.5
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS2r,subtitle = "TS2 shuffled")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-7.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-8.png)<!-- -->

```r

fdTS3 <- fd_dfa(TS3$TS3[1:2^L3], returnPLAW = TRUE)
## 
## 
## fd_dfa:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Detrended FLuctuation Analysis 
## 
##  Full range (n = 30)
## Slope = 0.41 | FD = 1.58 
## 
##  Exclude large bin sizes (n = 25)
## Slope = 0.45 | FD = 1.54
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS3,subtitle = "TS3")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-9.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-10.png)<!-- -->

```r

fdTS3r <- fd_dfa(TS3Random[1:2^L3], returnPLAW = TRUE)
## 
## 
## fd_dfa:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Detrended FLuctuation Analysis 
## 
##  Full range (n = 30)
## Slope = 0.47 | FD = 1.53 
## 
##  Exclude large bin sizes (n = 25)
## Slope = 0.48 | FD = 1.51
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS3r,subtitle = "TS3 shuffled")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-11.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-12.png)<!-- -->

```r

fdTS3n <- fd_dfa(TS3Norm[1:2^L3], returnPLAW = TRUE)
## 
## 
## fd_dfa:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Detrended FLuctuation Analysis 
## 
##  Full range (n = 30)
## Slope = 0.41 | FD = 1.58 
## 
##  Exclude large bin sizes (n = 25)
## Slope = 0.45 | FD = 1.54
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdTS3n,subtitle = "TS3 standardised")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-13.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-14.png)<!-- -->

```r


# Logistic map
y1 <- growth_ac(r = 2.9, type="logistic",N = 1024)
fdL29 <- fd_dfa(y1, returnPLAW = TRUE)
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Detrended FLuctuation Analysis 
## 
##  Full range (n = 30)
## Slope = 0.48 | FD = 1.52 
## 
##  Exclude large bin sizes (n = 25)
## Slope = 0.62 | FD = 1.41
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdL29, subtitle = "Logistic Map: r =  2.9")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-15.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-16.png)<!-- -->

```r

y2 <- growth_ac(r = 4, type="logistic", N = 1024)
fdL4 <- fd_dfa(y2, returnPLAW = TRUE)
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Detrended FLuctuation Analysis 
## 
##  Full range (n = 30)
## Slope = 0.46 | FD = 1.54 
## 
##  Exclude large bin sizes (n = 25)
## Slope = 0.48 | FD = 1.52
## 
## ~~~o~~o~~casnet~~o~~o~~~
plotFD_loglog(fdL4, subtitle = "Logistic Map: r =  4")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-17.png)<!-- -->![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-9-18.png)<!-- -->



## **Surrogate testing: Beyond the straw-man null-hypothesis** 

Look at the explanation of surrogate analysis on the [TiSEAN website](https://www.pks.mpg.de/~tisean/Tisean_3.0.1/)
(Here is [direct link](https://www.pks.mpg.de/~tisean/Tisean_3.0.1/docs/surropaper/node5.html) to the relevant sections)


### Constrained realisations of complex signals {.tabset .tabset-fade .tabset-pills}

We'll look mostly at three different kinds of surrogates:

* *Randomly shuffled*: $H_0:$ The time series data are independent random numbers drawn from some probability distribution.
* *Phase Randomised*: $H_0:$ The time series data were generated by a stationary linear stochastic process
* *Amplitude Adjusted ,Phase Randomised*: $H_0:$ The time series data were generated by a rescaled linear stochastic process.


#### Questions {-}

* Figure out how many surrogates you minimally need for a 2-sided test.
* Package `nonlinearTseries` has a function `FFTsurrogate` and package `fractal` `surrogate`. Look them up and try to create a surrogate test for some of the time series of the previous assignments.
    + If you use `fractal::surrogate()`, choose either `aaft` (amplitude adjusted Fourier transform) or `phase` (phase randomisation)
    + `nonlinearTseries::FFTsurrogates` will calculate phase randomised surrogates.
* In package `casnet` there's a function that will calculate and display a point-probability based on a distribution of surrogate measures and one observed measure: `plotSUR_hist`



#### Answers {-}


Create a number of surrogates, calulate the slopes and plot the results.

* First let's get the values we found earlier (we'll use the FD from `sda`, then we do the same for `psd`,but you can take any of the measures as well)



```r
library(fractal)

# RR assignment data `TS1,TS2,TS3`
# Now we'll trim the time series length to a power of 2 to get the cleanest results, this is not strictly necessary 
L1 <- floor(log2(length(TS1$TS1)))
L2 <- floor(log2(length(TS2$TS2)))
L3 <- floor(log2(length(TS2$TS2)))

TS1_FDsda <- fd_sda(TS1$TS1[1:2^L1])$fitRange$FD
```

```
## 
## 
## fd_sda:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 9)
## Slope = -0.16 | FD = 1.16 
## 
##  Fit range (n = 8)
## Slope = -0.18 | FD = 1.18
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```r
TS2_FDsda <- fd_sda(TS2$TS2[1:2^L2])$fitRange$FD
```

```
## 
## 
## fd_sda:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 9)
## Slope = -0.04 | FD = 1.04 
## 
##  Fit range (n = 8)
## Slope = -0.06 | FD = 1.06
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```r
TS3_FDsda <- fd_sda(TS3$TS3[1:2^L3])$fitRange$FD
```

```
## 
## 
## fd_sda:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 9)
## Slope = -0.63 | FD = 1.63 
## 
##  Fit range (n = 8)
## Slope = -0.6 | FD = 1.6
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```r
y1<-growth_ac(r = 2.9,type="logistic",N = 2^L1)
TSlogMapr29 <- fd_sda(y1)$fitRange$FD
```

```
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 9)
## Slope = -0.4 | FD = 1.4 
## 
##  Fit range (n = 8)
## Slope = -0.37 | FD = 1.37
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```r
y2<-growth_ac(r = 4,type="logistic", N = 2^L1)
TSlogMapr4 <- fd_sda(y2)$fitRange$FD
```

```
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 9)
## Slope = -0.97 | FD = 1.97 
## 
##  Fit range (n = 8)
## Slope = -0.62 | FD = 1.62
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```r
# These were rendomised
TS1R_FDsda <- fd_sda(TS1Random[1:2^L1])$fitRange$FD
```

```
## 
## 
## fd_sda:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 9)
## Slope = -0.67 | FD = 1.67 
## 
##  Fit range (n = 8)
## Slope = -0.52 | FD = 1.52
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```r
TS2R_FDsda <- fd_sda(TS1Random[1:2^L2])$fitRange$FD
```

```
## 
## 
## fd_sda:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 9)
## Slope = -0.67 | FD = 1.67 
## 
##  Fit range (n = 8)
## Slope = -0.52 | FD = 1.52
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```r
TS3R_FDsda <- fd_sda(TS1Random[1:2^L3])$fitRange$FD
```

```
## 
## 
## fd_sda:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Standardised Dispersion Analysis 
## 
##  Full range (n = 9)
## Slope = -0.67 | FD = 1.67 
## 
##  Fit range (n = 8)
## Slope = -0.52 | FD = 1.52
## 
## ~~~o~~o~~casnet~~o~~o~~~
```


* To create the surrogates using `fractal::surrogate()`
    + You'll need to repeatedly call the function and store the result
    + To get into a dataframe we have to use `unclass()` otherwise `R` doesn't recognise the object
    + Function `t()` trnasposes the data from 39 rows to 39 columns
* Once you have the surrogates in a datafram, repeatedly calculate the measure you want to compare, in this case `fd_sda()`.


```r
# NOW CREATE SURROGATES
library(dplyr)

# For a two-sided test at alpha = .05 we need N=39
Nsurrogates <- 39

TS1surrogates <- as.data.frame(t(ldply(1:Nsurrogates, function(s) unclass(surrogate(x=TS1$TS1[1:2^L1] ,method="aaft")))))
colnames(TS1surrogates) <- paste0("S",1:NCOL(TS1surrogates))

# Let's look at the surrogates and originals series...
# NOTE: This is a bit elaborate, because ggplot2 wants to have data in tidy (= long) format, which means all the time series data have to in 1 column!

# Make data frame to plot:
TS1surrogates$Obs <- TS1$TS1[1:2^L1]
TS1surrogates$Time <- 1:NROW(TS1surrogates)
df <- TS1surrogates %>% tidyr::gather(key=variable, value = Y, -Time)

# This will make the observed series red
eval(parse(text = c("col <- c('Obs' = 'red3',",paste0("'",colnames(TS1surrogates)%[%39,"'"," = 'black'", collapse=","),")")))

ggplot(df,aes(x=Time, y = Y, group = variable, colour = variable)) + 
  geom_path(show.legend = FALSE) +
  facet_grid(variable~.)  +
  scale_colour_manual(values = col) +
  scale_x_continuous(expand = c(0,0)) +
  theme_bw() +
  theme(axis.text = element_blank(), strip.text = element_blank(), strip.background = element_blank())
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

```r


# Now we calculate FD for each series
TS1surrogates_FD <- laply(1:Nsurrogates,function(s) fd_sda(y=TS1surrogates[,s], silent = TRUE)$fitRange$FD)

TS2surrogates <- t(ldply(1:Nsurrogates, function(s) unclass(surrogate(x=TS2$TS2[1:2^L2] ,method="aaft"))))
colnames(TS2surrogates) <- paste0("aaftSurr",1:NCOL(TS2surrogates))

TS2surrogates_FD <- laply(1:Nsurrogates,function(s) fd_sda(y=TS2surrogates[,s], silent = TRUE)$fitRange$FD)


TS3surrogates <- t(ldply(1:Nsurrogates, function(s) unclass(surrogate(x=TS3$TS3[1:2^L3] ,method="aaft"))))
colnames(TS3surrogates) <- paste0("aaftSurr",1:NCOL(TS3surrogates))

TS3surrogates_FD <- laply(1:Nsurrogates,function(s) fd_sda(y=TS3surrogates[,s], silent = TRUE)$fitRange$FD)


TSr29surrogates <- t(ldply(1:Nsurrogates, function(s) unclass(surrogate(x=y1, method="aaft"))))
colnames(TSr29surrogates) <- paste0("aaftSurr",1:NCOL(TSr29surrogates))

TSr29surrogates_FD <- laply(1:Nsurrogates,function(s) fd_sda(y=TSr29surrogates[,s], silent = TRUE)$fitRange$FD)


TSr4surrogates <- t(ldply(1:Nsurrogates, function(s) unclass(surrogate(x=y2, method="aaft"))))
colnames(TSr4surrogates) <- paste0("aaftSurr",1:NCOL(TSr4surrogates))

TSr4surrogates_FD <- laply(1:Nsurrogates,function(s) fd_sda(y=TSr4surrogates[,s], silent = TRUE)$fitRange$FD)
```

* Collect the results in a dataframe, so we can compare.


```r
x <- data.frame(Source = c("TS1", "TS2", "TS3", "TSlogMapr29", "TSlogMapr4"), 
                FDsda = c(TS1_FDsda, TS2_FDsda, TS3_FDsda, TSlogMapr29, TSlogMapr4), 
                FDsdaRandom =  c(TS1R_FDsda, TS2R_FDsda, TS3R_FDsda,NA,NA), 
                FDsdaAAFT.median= c(median(TS1surrogates_FD),median(TS2surrogates_FD),median(TS3surrogates_FD),median(TSr29surrogates_FD),median(TSr4surrogates_FD)))
```


* Call function `plotSUR_hist()` to see the results of the test.


```r
plotSUR_hist(surrogateValues = TS1surrogates_FD, observedValue = TS1_FDsda, sides = "two.sided", doPlot = TRUE, measureName = "sda FD TS1")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

```r
plotSUR_hist(surrogateValues = TS2surrogates_FD, observedValue = TS2_FDsda, sides = "two.sided", doPlot = TRUE, measureName = "sda FD TS2")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-13-2.png)<!-- -->

```r
plotSUR_hist(surrogateValues = TS3surrogates_FD, observedValue = TS3_FDsda,sides = "two.sided", doPlot = TRUE, measureName = "sda FD TS3")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-13-3.png)<!-- -->

```r
plotSUR_hist(surrogateValues = TSr29surrogates_FD, observedValue = TSlogMapr29,sides = "two.sided", doPlot = TRUE, measureName = "sda FD logMap r=2.9")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-13-4.png)<!-- -->

```r
plotSUR_hist(surrogateValues = TSr4surrogates_FD, observedValue = TSlogMapr4, sides = "two.sided", doPlot = TRUE, measureName = "sda FD logMap r=4")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-13-5.png)<!-- -->

**FD: SPECTRAL SLOPE**


```r
# RR assignment data `TS1,TS2,TS3`
L1 <- floor(log2(length(TS1$TS1)))
L2 <- floor(log2(length(TS2$TS2)))
L3 <- floor(log2(length(TS2$TS2)))

TS1_FDpsd <- fd_psd(TS1$TS1[1:2^L1])$fitRange$FD
```

```
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 256)
## Slope = -0.92 | FD = 1.22 
## 
##  Hurvich-Deo (n = 33)
## Slope = -0.53 | FD = 1.32
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```r
TS2_FDpsd <- fd_psd(TS2$TS2[1:2^L2])$fitRange$FD
```

```
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 256)
## Slope = -0.79 | FD = 1.24 
## 
##  Hurvich-Deo (n = 17)
## Slope = -1.12 | FD = 1.18
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```r
TS3_FDpsd <- fd_psd(TS3$TS3[1:2^L3])$fitRange$FD
```

```
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 256)
## Slope = 0.19 | FD = 1.57 
## 
##  Hurvich-Deo (n = 16)
## Slope = 0.97 | FD = 1.79
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```r
# Logistic map - make it the length of L1
y1<-growth_ac(r = 2.9,type="logistic",N = 2^L1)
# Turn standardisation and detrending off!
TSlogMapr29 <- fd_psd(y1, standardise = FALSE, detrend = FALSE)$fitRange$FD
```

```
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 256)
## Slope = -0.95 | FD = 1.21 
## 
##  Hurvich-Deo (n = 13)
## Slope = -4.69 | FD = 1.08
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```r
y2<-growth_ac(r = 4,type="logistic", N = 2^L1)
TSlogMapr4 <- fd_psd(y2, standardise = FALSE, detrend = FALSE)$fitRange$FD
```

```
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 256)
## Slope = 0.08 | FD = 1.53 
## 
##  Hurvich-Deo (n = 25)
## Slope = 0.44 | FD = 1.66
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```r
TS1R_FDpsd <- fd_psd(TS1Random[1:2^L1])$fitRange$FD
```

```
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 256)
## Slope = -0.14 | FD = 1.45 
## 
##  Hurvich-Deo (n = 21)
## Slope = -0.43 | FD = 1.35
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```r
TS2R_FDpsd <- fd_psd(TS1Random[1:2^L2])$fitRange$FD
```

```
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 256)
## Slope = -0.14 | FD = 1.45 
## 
##  Hurvich-Deo (n = 21)
## Slope = -0.43 | FD = 1.35
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```r
TS3R_FDpsd <- fd_psd(TS1Random[1:2^L3])$fitRange$FD
```

```
## 
## 
## fd_psd:	Sample rate was set to 1.
## 
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Power Spectral Density Slope 
## 
##  All frequencies (n = 256)
## Slope = -0.14 | FD = 1.45 
## 
##  Hurvich-Deo (n = 21)
## Slope = -0.43 | FD = 1.35
## 
## ~~~o~~o~~casnet~~o~~o~~~
```


* To create the surrogates using `fractal::surrogate()`
    + You'll need to repeatedly call the function and store the result
    + To get into a dataframe we have to use `unclass()` otherwise `R` doesn't recognise the object
    + Function `t()` trnasposes the data from 39 rows to 39 columns
* Once you have the surrogates in a dataframe, repeatedly calculate the measure you want to compare, in this case `fd_sda()`.


```r
# NOW CREATE SURROGATES
library(dplyr)

# For a two-sided test at alpha = .05 we need N=39
Nsurrogates <- 39

TS1surrogates <- t(ldply(1:Nsurrogates, function(s) unclass(fractal::surrogate(x=TS1$TS1[1:2^L1] ,method="aaft"))))
colnames(TS1surrogates) <- paste0("aaftSurr",1:NCOL(TS1surrogates))

TS1surrogates_FD <- laply(1:Nsurrogates,function(s){fd_psd(y=TS1surrogates[,s], silent = TRUE)$fitRange$FD})


TS2surrogates <- t(ldply(1:Nsurrogates, function(s) unclass(surrogate(x=TS2$TS2[1:2^L2] ,method="aaft"))))
colnames(TS2surrogates) <- paste0("aaftSurr",1:NCOL(TS2surrogates))

TS2surrogates_FD <- laply(1:Nsurrogates,function(s) fd_psd(y=TS2surrogates[,s], silent = TRUE)$fitRange$FD)


TS3surrogates <- t(ldply(1:Nsurrogates, function(s) unclass(surrogate(x=TS3$TS3[1:2^L3] ,method="aaft"))))
colnames(TS3surrogates) <- paste0("aaftSurr",1:NCOL(TS3surrogates))

TS3surrogates_FD <- laply(1:Nsurrogates,function(s) fd_psd(y=TS3surrogates[,s], silent = TRUE)$fitRange$FD)


TSr29surrogates <- t(ldply(1:Nsurrogates, function(s) unclass(surrogate(x=y1, method="aaft"))))
colnames(TSr29surrogates) <- paste0("aaftSurr",1:NCOL(TSr29surrogates))

TSr29surrogates_FD <- laply(1:Nsurrogates,function(s) fd_psd(y=TSr29surrogates[,s], silent = TRUE)$fitRange$FD)


TSr4surrogates <- t(ldply(1:Nsurrogates, function(s) unclass(surrogate(x=y2, method="aaft"))))
colnames(TSr4surrogates) <- paste0("aaftSurr",1:NCOL(TSr4surrogates))

TSr4surrogates_FD <- laply(1:Nsurrogates,function(s) fd_psd(y=TSr4surrogates[,s], silent = TRUE)$fitRange$FD)
```


* Call function `plotSUR_hist()` to get the results of the test.


```r
plotSUR_hist(surrogateValues = TS1surrogates_FD, observedValue = TS1_FDpsd, sides = "two.sided", doPlot = TRUE, measureName = "psd FD TS1")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-16-1.png)<!-- -->

```r
plotSUR_hist(surrogateValues = TS2surrogates_FD, observedValue = TS2_FDpsd,sides = "two.sided", doPlot = TRUE, measureName = "psd FD TS2")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-16-2.png)<!-- -->

```r
plotSUR_hist(surrogateValues = TS3surrogates_FD, observedValue = TS3_FDpsd,sides = "two.sided", doPlot = TRUE, measureName = "psd FD TS3")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-16-3.png)<!-- -->

```r
plotSUR_hist(surrogateValues = TSr29surrogates_FD, observedValue = TSlogMapr29,sides = "two.sided", doPlot = TRUE, measureName = "psd FD logMap r=2.9")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-16-4.png)<!-- -->

```r
plotSUR_hist(surrogateValues = TSr4surrogates_FD, observedValue = TSlogMapr4, sides = "two.sided", doPlot = TRUE, measureName = "psd FD logMap r=4")
```

![](ASSIGNMENTS_P2B_files/figure-html/unnamed-chunk-16-5.png)<!-- -->


 * Let's print the results into a table
 


```r
x$FDpsd <- c(TS1_FDpsd,TS2_FDpsd,TS3_FDpsd,TSlogMapr29,TSlogMapr4)
x$FDpsdRandom <- c(TS1R_FDpsd, TS2R_FDpsd, TS3R_FDpsd,NA,NA)
x$FDpsdAAFT.median <- c(median(TS1surrogates_FD),median(TS2surrogates_FD),median(TS3surrogates_FD),median(TSr29surrogates_FD),median(TSr4surrogates_FD))
knitr::kable(x, digits = 2, booktabs=TRUE,formt="html")
```



Source         FDsda   FDsdaRandom   FDsdaAAFT.median   FDpsd   FDpsdRandom   FDpsdAAFT.median
------------  ------  ------------  -----------------  ------  ------------  -----------------
TS1             1.18          1.52               1.24    1.32          1.35               1.27
TS2             1.06          1.52               1.10    1.18          1.35               1.14
TS3             1.60          1.52               1.62    1.79          1.35               1.58
TSlogMapr29     1.37            NA               1.37    1.08            NA               1.36
TSlogMapr4      1.62            NA               1.56    1.66            NA               1.56


