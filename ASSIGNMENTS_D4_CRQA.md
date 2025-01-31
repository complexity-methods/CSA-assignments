---
title: "Cross-RQA"
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
    pandoc_args: ["--number-offset","5"]
editor_options: 
  chunk_output_type: console
---




# **Cross-Recurrence Analysis**

Cross recurrewnce analysis can be used to study synchronisation and coupling direction. There are other several orther packages and software that can run (C)RQA:

* The paper accompanying package `crqa` by Coco & Dale (2014) gives a good introduction and of RQA and CRQA: https://www.frontiersin.org/articles/10.3389/fpsyg.2014.00510/full 
* Here's a tutorial on Multidimensional RQA and digonal profiles: https://www.frontiersin.org/articles/10.3389/fpsyg.2018.02232/full 
* Here's a site comparing different analysis software packages for CRQA: https://github.com/JuliaDynamics/RecurrenceAnalysis.jl/wiki/Comparison-of-software-packages-for-RQA 



### Categorical Cross-RQA and the Diagonal Recurrence Profile {.tabset .tabset-fade .tabset-pills}


In 2008 there was some commotion about a speech held by Barack Obama in Milwaukee ([news item](https://www.cbsnews.com/news/obama-accused-of-plagiarism-in-speech/)). According to the media the speech was very similar to a speech held the gouverneur of Massachusetts (Deval Patrick) some years earlier. Here are some fragments of the speeches,

Obama (2008):

> Don't tell me words don't matter. "I have a dream." Just words? "We hold these truths to be self-evident, that all men are created equal." Just words? "We have nothing to fear but fear itself"—just words? Just speeches?

Patrick (2006): 

> "We hold these truths to be self-evident, that all men are created equal." Just words—just words! "We have nothing to fear but fear itself." Just words! "Ask not what your country can do for you, ask what you can do for your country." Just words! "I have a dream." Just words!



```r
# Load the speeches as timeseries
library(casnet)
obama2008 <- strsplit(x = "dont tell me words dont matter I have a dream just words we hold these truths to be selfevident that all men are created equal just words We have nothing to fear but fear itself just words just speeches", split = " ")[[1]]

patrick2006 <- strsplit(x = "we hold these truths to be selfevident that all men are created equal just words just words we have nothing to fear but fear itself just words ask not what your country can do for you ask what you can do for your country just words I have a dream just words", split = " ")[[1]]

uniqueID <- as.numeric_discrete(c(obama2008,patrick2006))

speeches <- as.data.frame(ts_trimfill(x=uniqueID[1:length(obama2008)], y=uniqueID[(length(obama2008)+1):length(uniqueID)]))
colnames(speeches) <- c("Obama","Patrick")
```


#### Questions {-}

* Plot both time series. 

* Because both fragments weren't of equal size, one series is "filled" at the end with the value `0`
    - Do you think this has an effect on the CRQA measures? 
    - What & why?

* Perform a categorical CRQA:
   - First create a cross recurrence matrix using function `rp()`. 
   - Look at the manual page what the values mean, look at last the categorical RQA assignment for the values of `emLag`, `emDim` and `emRad`
   - Get recurrence measures using `rp()` (ignore the warning),
   - Plot the recurrence plot using `rp_plot()`
   - Also look at the diagonal recurrence profile using `rp_diagProfile()`
   
* What is your opinion about the similarity of these speeches?


#### Answers {-}

* Adding a value which occurs in just 1 series (whether it is `-2`, or `0`), but not the other makes sure that value will not be recurring in a Cross Recurrence Plot.



```r
# Plot the speech series
plot(ts(speeches))

# Cross recurrence matrix
RM <- rp(y1 = as.numeric_discrete(speeches$Obama), 
         y2 = as.numeric_discrete(speeches$Patrick), emDim = 1, emLag = 1, emRad = 0)

# Measures
crqa_out <- rp_measures(RM, silent = FALSE)
```

```
## 
## ~~~o~~o~~casnet~~o~~o~~~
## 
##  Global Measures 
##              Global Max.rec.points N.rec.points Recurrence.Rate Singular.points
## 1 Recurrence Matrix           2652           71       0.0262574              15
##   Divergence Repetitiveness Anisotropy
## 1 0.06666667              0         NA
## 
## 
##  Line-based Measures 
##   Line.based N.lines N.points.on.lines      Measure      Rate     Mean  Max
## 1   Diagonal      15                56  Determinism 0.7887324 3.733333   15
## 2   Vertical       0                 0 V Laminarity 0.0000000      NaN -Inf
## 3 Horizontal       0                 0 H Laminarity 0.0000000      NaN -Inf
##   Entropy.of.lengths Relative.entropy CoV.of.lengths
## 1           1.080574        0.2748275      0.9891909
## 2           0.000000        0.0000000             NA
## 3           0.000000        0.0000000             NA
## 
## ~~~o~~o~~casnet~~o~~o~~~
```

```r
# Plot
rp_plot(RM, plotDimensions = TRUE, plotMeasures = TRUE)
```

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-3-1.png)<!-- -->![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-3-2.png)<!-- -->

```r
# Diagonal profile --> The plot function is a bit broken, here we make our plot
drp <- rp_diagProfile(RM, diagWin = 40, doShuffle = FALSE, doPlot = FALSE)
```

```
## 
## Profile 1
```

```r
library(ggplot2)
ggplot(drp, aes(x = as.numeric_discrete(labels), y = RR, colour = variable)) + 
  geom_line() + theme_bw()
```

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-3-3.png)<!-- -->


### Continuous CRQA and the Diagonal Profile {.tabset .tabset-fade .tabset-pills}


#### Questions {-}

* Create two sinewave variables for CRQA analysis and use the $x$ and $y$ coordinates of a circletracing experiment:


```r
library(rio)
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
    
* Run the CRQA using `rp_cl()` using th time series as input, or `rp()` using a thresholded matrix as input.
    + You can find a radius automatically, look in the `casnet` manual for function `est_radius()`. 
    + Most functions, like `rp_plot()` will call `est_radius()` if no radius is provided in the argument `emRad`.
    + Request a radius which will give us about 5\% recurrent points (default setting).
    + You can create a thresholded matrix by calling function `di2bi()`,
* Produce a plot of the recurrence matrix using `rp_plot()`. Look at the manual pages.

* Can you understand what is going on? 
  + For the simulated data: Explain the the lack of recurrent points at the beginning of the time series.
  + For the circle trace: How could one see these are not deterministic sine waves?

* Examine the synchronisation under the diagonal LOS. Look in the manual of `rp_diagProfile()`. More elaborate explanation can be found in [Coco & Dale (2014)](http://journal.frontiersin.org/Journal/10.3389/fpsyg.2014.00510/abstract). 
  + To get the diagonal profile from a recurrence matrix `rp_diagProfile(RM, winDiag = , ...)`. Where `winDiag` is will indicate the window size `-winDiag ... 0 ... +winDiag`.
  + How far is the peak in RR removed from 0 (Line of Synchronisation)? 

* To check whether the patterns are real by conducting a Cross-RQA with a shuffled/surrogate version of e.g. time series $y2$.
   + Function `rp_diagProfile()` can do a shuffled analysis, check the manual pages. 
   + Use `Nshuffle = 1` (or wait a long time)

> NOTE: If you generate surrogate timeseries, make sure the RR is the same for all surrogates. Try to keep the RR in the same range by using.


#### Answers Sine Waves {-}


* You have just created two sine(-like) waves. We’ll examine if and how they are coupled in a shared phase space. 
   + As a first step plot them.
   + Also plot the cirlce trace state space


```r
library(rio)
y1 <- sin(1:500*2*pi/67)
y2 <- sin(.01*(1:500*2*pi/67)^2)


# Time series
plot(cbind(ts(y1),ts(y2)))
```

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

```r
# State space
plot(y1,y2,type="l", pty="s",main = "Sine waves")
```

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-5-2.png)<!-- -->

* Find a common embedding delay and an embedding dimension (if you calculate an embedding dimension for each signal separately, as a rule of thumb use the highest embedding dimension you find in further analyses).


```r
# sines
params_y1 <- est_parameters(y1)
```

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

```r
params_y2 <- est_parameters(y2)
```

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-6-2.png)<!-- -->

> The diagnostic plot of the sine wave `y1` looks a bit odd. This is due to the fact that mutual information will discretize the time series, and the sine will  fill all bins with the same values, because it is perfectly repeating itself. We'll use emLag = 1, emDim = 2. When inh doubt, do not embed the time series... here we know it is a 2D 'system'.


```r
emLag <- 1
emDim <- 2

RM <- rp(y1=y1, y2=y2,emDim = emDim, emLag = emLag)

# Unthresholded Matrix
rp_plot(RM, plotDimensions = TRUE)
```

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

```r
# Thresholded Matrix, based on 5% RR
emRad <- est_radius(RM = RM, targetValue = .05)$Radius
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

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-7-2.png)<!-- -->

```r
# The diagonal profile
drp <- rp_diagProfile(RP, diagWin = 50, doPlot = FALSE)
```

```
## 
## Profile 1
```

```r
library(ggplot2)
ggplot(drp, aes(x = as.numeric_discrete(labels), y = RR, colour = variable)) + 
  geom_line() + theme_bw()
```

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-7-3.png)<!-- -->

```r
# Compare CRQA with one randomly shuffled series: broken...
# drpRND <- rp_diagProfile(RP, diagWin = 50, doShuffle = TRUE, y1 = y1, y2 = y2, Nshuffle = 19, doPlot = FALSE)
```


#### Answers Circle Tracing {-}

* Load data and plot the data.



```r
# Here are the circle trace data
xy <- import("https://raw.githubusercontent.com/FredHasselman/The-Complex-Systems-Approach-Book/master/assignments/assignment_data/RQA_circletrace/mouse_circle_xy.csv") 

circle_x <- xy$x[1:1000]
circle_y <- xy$y[1:1000]

# Time Series
plot(cbind(ts(circle_x),ts(circle_y)))
```

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

```r
# State Space
plot(circle_x,circle_y,type="l", pty="s",main = "Mouse Circle Trace")
```

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-8-2.png)<!-- -->

* Estimate parameters


```r
params_cx <- est_parameters(circle_x)
```

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

```r
params_cy <- est_parameters(circle_y)
```

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-9-2.png)<!-- -->

> The circle trace data can be embedded in 3 dimensions. This is not so strange, even though the circle was drawn in 2 dimensions, there are also speed/acceleration and accuracy constraints.


* Recurrence matrix and diagonal profile


```r
emLag <- median(params_cx$optimLag, params_cy$optimLag)
emDim <- max(params_cx$optimDim, params_cy$optimDim)

RM <- rp(y1=circle_x, y2=circle_y,emDim = emDim, emLag = emLag)

# Unthresholded Matrix
rp_plot(RM, plotDimensions = TRUE)
```

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

```r
# Thresholded Matrix, based on 5% RR
emRad <- est_radius(RM = RM, targetValue = .05)$Radius
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

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-10-2.png)<!-- -->

```r
# The diagonal profile
drp <- rp_diagProfile(RP, diagWin = 100, doPlot = FALSE)
```

```
## 
## Profile 1
```

```r
library(ggplot2)
ggplot(drp, aes(x = as.numeric_discrete(labels), y = RR, colour = variable)) + 
  geom_line() + theme_bw()
```

![](ASSIGNMENTS_D4_CRQA_files/figure-html/unnamed-chunk-10-3.png)<!-- -->


  

