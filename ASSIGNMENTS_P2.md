---
title: "DCS Assignments Session 3"
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
    pandoc_args: ["--number-offset","1"]
editor_options: 
  chunk_output_type: console
---





# **Quick Links** {-}

* [Main Assignments Page](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/)

<!-- * [Assignments Part 1A: Introduction to the mathematics of change](https://darwin.pwo.ru.nl/skunkworks/courseware/1718_DCS/assignments/ASSIGNMENTS_P1A.html) -->
<!-- * [Assignments Part 2: Time Series Analysis: Temporal Correlations and Fractal Scaling](https://darwin.pwo.ru.nl/skunkworks/courseware/1718_DCS/assignments/ASSIGNMENTS_P2.html) -->
<!-- * [Assignments Part 3: Quantifying Recurrences in State Space](https://darwin.pwo.ru.nl/skunkworks/courseware/1718_DCS/assignments/ASSIGNMENTS_P3.html) -->
<!-- * [Assignments Part 4: Complex Networks](https://darwin.pwo.ru.nl/skunkworks/courseware/1718_DCS/assignments/ASSIGNMENTS_P4.html) -->

<!-- </br> -->
<!-- </br> -->


# **Fitting Parameters of Analytic Solutions [EXTRA]**


## **Nonlinear Growth curves**


### Fitting the Logistic Growth model in SPSS {.tabset .tabset-fade .tabset-pills}

#### Questions {-}

Open the file [Growthregression.sav](https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/blob/master/assignments/assignment_data/BasicTSA_nonlinreg/GrowthRegression.sav), it contains two variables: `Time` and `Y(t)`. 

This is data from an iteration of the logistic growth differential equation you are familiar with by now, but let’s pretend it’s data from one subject measured on 100 occasions.

* Plot Y(t) against Time Recognize the shape?
* To get the growth parameter we’ll try to fit the solution of the logistic flow with SPSS non-linear regression
     + Select non-linear… from the `Analysis` >> `Regression` menu.
     + Here we can build the solution equation. We need three parameters:
            a. **Yzero**, the initial condition.
            b. *K*, the carrying capacity.
            c. *r*, the growth rate.
     + Fill these in where it says `parameters` give all parameters a starting value of  $0.01$

Take a good look at the analytic solution of the (stilized) logistic flow:

$$
Y(t)  =  \frac{K * Y_0}{Y_0 + \left(K-Y_{0}\right) * e^{-r*t} }
$$

Try to build this equation, the function for $e$ is called `EXP` in `SPSS` (`Function Group` >> `Arithmetic`)
Group terms by using parentheses as shown in the equation.

* If you think you have built the model correctly, click on `Save` choose `predicted values`. Then paste your syntax and run it!
    + Check the estimated parameter values.
    + Check $R^2$!!!

* Plot a line graph of both the original data and the predicted values. (Smile)

You can try do the same using a polynomial approach.

* A polynomial fishing expedition:
    + Create time-varying 'covariates' of $Y(t)$:
```
COMPUTE T1=Yt * Time.
COMPUTE T2=Yt * (Time ** 2). 
COMPUTE T3=Yt * (Time ** 3). 
COMPUTE T4=Yt * (Time ** 4). 
EXECUTE.
```
    + Use these variables as predictors of $Y(t)$ in a regular linear regression analysis. This is called a *polynomial regression*: Fitting combinations of curves of different shapes on the data.
    + Before you run the analysis: Click `Save` Choose `Predicted Values: Unstandardized`

\BeginKnitrBlock{rmdselfthink}<div class="rmdselfthink">Look at $R^2$. This is also almost 1!

Which model is better? 

Think about this: Based on the results of the linear regression what can you tell about the *growth rate*, the *carrying capacity* or the *initial condition*?</div>\EndKnitrBlock{rmdselfthink}

*	Create a line graph of $Y(t)$, plot the predicted values of the non-linear regression and the unstandardised predicted values of the linear polynomial regression against `time` in one figure.
    + Now you can see that the shape is approximated by the polynomials, but it is not quite the same. Is this really a model of a growth process as we could encounter it in nature?


#### Answers {-}

The solutions are also provided as an `SPSS` [syntax file](https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/blob/master/assignments/assignment_data/BasicTSA_nonlinreg/GrowthRegression_sol.sps) file.

Or copy the block below:
```
GRAPH
  /LINE(SIMPLE)=VALUE(Yt) BY Time.

* NonLinear Regression.
MODEL PROGRAM  Yzero=0.01 r=0.01 K=0.01.
COMPUTE  PRED_=K*Yzero/(Yzero + (K-Yzero) * EXP(-1*(r * Time))).
NLR Yt
  /PRED PRED_
  /SAVE PRED
  /CRITERIA SSCONVERGENCE 1E-8 PCON 1E-8.

GRAPH
  /LINE(MULTIPLE)=VALUE(Yt PRED_) BY Time.

COMPUTE T1=Yt * Time.
COMPUTE T2=Yt * (Time ** 2).
COMPUTE T3=Yt * (Time ** 3).
COMPUTE T4=Yt * (Time ** 4).
EXECUTE.

REGRESSION
  /MISSING LISTWISE
  /STATISTICS COEFF OUTS R ANOVA
  /CRITERIA=PIN(.05) POUT(.10)
  /NOORIGIN 
  /DEPENDENT Yt
  /METHOD=ENTER T1 T2 T3 T4
  /SAVE PRED.


GRAPH
  /LINE(MULTIPLE)=VALUE(Yt PRED_ PRE_1) BY Time.
```

* The point here is that the polynomial regression approach is "just" curve fitting ... adding components until a nice fit is found ... but what does component $Y_t^4$ represent? A quartic sub-process?
* Fitting the solution of the the logistic function will give us parameters we can interpret unambiguously: Carrying capacity, growth rate, starting value.


### Fitting the Logistic Growth model in R {.tabset .tabset-fade .tabset-pills}

There are several packages that can perform non-linear regression analysis, the function most resembling the approach used by `SPSS` is `nls` in the default `stats` package.  

The easiest way to do this is to first define your function (i.e., the solution) and then fit it using starting values for the parameters.


```r
library(rio)
df <- import("https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/raw/master/assignments/assignment_data/BasicTSA_nonlinreg/GrowthRegression.sav", setclass = "tbl_df")

# Create the function for the analytic solution
# Same as SPSS syntax: PRED_=K*Yzero/(Yzero + (K-Yzero) * EXP(-1*(r * Time))).
log.eq <- function(Yzero, r, K, Time) {
    K*Yzero/(Yzero + (K-Yzero) * exp(-1*(r * Time)))
}
```

There is one drawback and you can read about in the help pages:

> Warning. Do not use nls on artificial "zero-residual" data.

This means, "do not use it on data generated by a deterministic model which has no residual error", which is exactly what the time series in this assignment is, it is the output of the logistic flow.

So, this will give an error:

```r
# Fit this function ... gives an error
# The list after 'start' provides the initial values
m.log <- nls(Yt ~ log.eq(Yzero, r, K, Time), data = df, start = list(Yzero=.01, r=.01, K=1), trace = TRUE)
```



We'll provide you the answers right away, but be sure to figure out what is going on.



#### Answers{-}

It is possible to fit these ideal data using package `minpack.lm`, which contains function `nlsM`.


```r
library(minpack.lm)

m.log <- nlsLM(Yt ~ log.eq(Yzero, r, K, Time), data = df, start = list(Yzero = .01, r=.01, K=0.1))

summary(m.log)
```

```
## 
## Formula: Yt ~ log.eq(Yzero, r, K, Time)
## 
## Parameters:
##        Estimate Std. Error t value Pr(>|t|)    
## Yzero 7.055e-03  8.983e-05   78.53   <2e-16 ***
## r     1.494e-01  3.878e-04  385.18   <2e-16 ***
## K     1.002e+00  4.376e-04 2289.42   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.002865 on 97 degrees of freedom
## 
## Number of iterations to convergence: 21 
## Achieved convergence tolerance: 1.49e-08
```

In order to look at the model prediction, we use `predict()` which is defined for almost all model fitting functions in `R`

```r
Ypred <- predict(m.log)

plot(ts(df$Yt), col="gray40", lwd=5, ylab = ("Yt | Ypred"))
lines(Ypred, col="gray80", lwd=2, lty=2)
```

![](ASSIGNMENTS_P2_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

Then we do a polynomial regression using `lm`:

```r
# Mimic the SPSS syntax
attach(df)
  df$T1 <- Yt * Time
  df$T2 <- Yt * (Time^2) 
  df$T3 <- Yt * (Time^3) 
  df$T4 <- Yt * (Time^4)
detach(df)

m.poly <- lm(Yt ~ T1 + T2 + T3 + T4, data=df)
summary(m.poly)
```

```
## 
## Call:
## lm(formula = Yt ~ T1 + T2 + T3 + T4, data = df)
## 
## Residuals:
##        Min         1Q     Median         3Q        Max 
## -0.0117491 -0.0046800 -0.0000683  0.0045719  0.0112732 
## 
## Coefficients:
##               Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  2.113e-02  1.258e-03   16.80   <2e-16 ***
## T1           6.366e-02  7.169e-04   88.80   <2e-16 ***
## T2          -1.497e-03  3.100e-05  -48.28   <2e-16 ***
## T3           1.510e-05  4.425e-07   34.12   <2e-16 ***
## T4          -5.529e-08  2.055e-09  -26.90   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.005506 on 95 degrees of freedom
## Multiple R-squared:  0.9998,	Adjusted R-squared:  0.9998 
## F-statistic: 1.264e+05 on 4 and 95 DF,  p-value: < 2.2e-16
```

Then, predict and plot!

```r
Ypoly <- predict(m.poly)

plot(ts(Ypoly), col="blue1", lwd=2, ylab = ("Ypoly (blue) | Ypred (red)"))
lines(Ypred, col="red1", lwd=2)
```

![](ASSIGNMENTS_P2_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

SPSS computes an $r^2$ value for non-linear regression models, which doesn't make a lot of sense if you think about it. Here we van just compare the residual errors:

* Polynomial regression: $0.005506$
* Analytic solution: $0.002865$

Slightly less residual error for the analytic solution, using less parameters to fit the model (3 vs. 5). **More important:**, the parameters of the analytic solution have a direct interpretation in terms of growth processes.



# **Basic Timeseries Analysis** 

In this course we will not discuss the type of linear time series models known as Autoregressive Models (e.g. AR, ARMA, ARiMA, ARfiMA) summarised on [this Wikipedia page on timeseries](https://en.wikipedia.org/wiki/Time_series#Models). We will in fact be discussing a lot of methods in a book the Wiki page refers to for *'Further references on nonlinear time series analysis'*: [**Nonlinear Time Series Analysis** by Kantz & Schreiber](https://www.cambridge.org/core/books/nonlinear-time-series-analysis/519783E4E8A2C3DCD4641E42765309C7). You do not need to buy the book, but it can be a helpful reference if you want to go beyond the formal level (= mathematics) used in this course. Some of the packages we use are based on the companying software [**TiSEAN**](https://www.pks.mpg.de/~tisean/Tisean_3.0.1/index.html) which is written in `C` and `Fortran` and can be called from the command line (Windows / Linux).


## **Correlation functions**  

Correlation functions are intuitive tools for quantifying the temporal structure in a time series. As you know, correlation can only quantify linear regularities between variables, which is why we discuss them here as `basic` tools for time series analysis. So what are the variables? In the simplest case, the variables between which we calculate a correlation are between a data point at time *t* and a data point that is separated in time by some *lag*, for example, if you would calculate the correlation in a lag-1 return plot, you would have calculated the 1st value of the correlation function (actually, it is 2nd value, the 1st value is the correlation of time series with itself, the lag-0 correlation, which is of course $r = 1$)  

### ACF and PCF {.tabset .tabset-fade .tabset-pills}


You can do the analyses in SPSS or in `R`, but this analysis is very common so you'll find functions called `acf`, `pacf`and `ccf` in many other statistical software packages, In package `casnet` you can use the function `plotRED_acf()` (plot REDundancies).


#### Questions (SPSS/R) {-}

* Download the file [`series.sav`](https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/blob/master/assignments/assignment_data/BasicTSA_arma/series.sav) from Github. 

It contains three time series `TS_1`, `TS_2` and `TS_3`. As a first step look at the mean and the standard deviation (`Analyze` >> `Descriptives`).  Suppose these were time series from three subjects in an experiment, what would you conclude based on the means and SD’s?  

* Let’s visualize these data. Go to `Forecasting` >> `Time Series` >> `Sequence Charts`. Check the box One chart per variable and move all the variables to Variables. Are they really the same?  

* Let’s look at the `ACF` and `PCF`
    + Go to `Analyze` >> `Forecasting` >> `Autocorrelations`. 
    + Enter all the variables and make sure both *Autocorrelations* (ACF) and *Partial autocorrelations* (PACF) boxes are checked. Click `Options`, and change the `Maximum Number of Lags` to `30`. 
    + Use the table to characterize the time series:  


|                    SHAPE                | INDICATED MODEL |
|-----------------------------------------|-------------------------------------------------------------------------------------------------|
|       Exponential, decaying to zero     | Autoregressive model. Use the partial autocorrelation plot to identify the order of the autoregressive model|
| Alternating positive and negative, decaying to zero  | Autoregressive model. Use the partial autocorrelation plot to help identify the order.|
| One or more spikes, rest are essentially zero | Moving average model, order identified by where plot becomes zero. |
| Decay, starting after a few lags | Mixed autoregressive and moving average model.|
All zero or close to zero  | Data is essentially random.|
| High values at fixed intervals | Include seasonal autoregressive term. |
| No decay to zero  | Series is not stationary. |


* In the simulation part of this course we have learned a very simple way to explore the dynamics of a system: The return plot. The time series is plotted against itself shifted by 1 step in time. 

* Create return plots (use a Scatterplot) for the three time series. Tip: You can easily create a `t+1` version of the time series by using the LAG function in a `COMPUTE` statement. For instance:

```
COMPUTE TS_1_lag1 = LAG(TS_1)
```
    
* Are your conclusions about the time series based on interpreting these return plots the same as based on the `acf` and `pacf`?
     
     
#### Answers (SPSS) {-}

If you run this syntax in `SPSS` you'll get the correct output.

```
DESCRIPTIVES
  VARIABLES=TS_1 TS_2 TS_3
  /STATISTICS=MEAN STDDEV MIN MAX .

*Sequence Charts .
TSPLOT VARIABLES= TS_1
  /NOLOG
  /FORMAT NOFILL REFERENCE.
TSPLOT VARIABLES= TS_2
  /NOLOG
  /FORMAT NOFILL REFERENCE.
TSPLOT VARIABLES= TS_3
  /NOLOG
  /FORMAT NOFILL REFERENCE.

*ACF and PCF.

ACF
  VARIABLES= TS_1 TS_2 TS_3
  /NOLOG
  /MXAUTO 30
  /SERROR=IND
  /PACF.


*Return plots.

COMPUTE TS_1_lag1 = LAG(TS_1) .
COMPUTE TS_2_lag1 = LAG(TS_2) .
COMPUTE TS_3_lag1 = LAG(TS_3) .
EXECUTE .


IGRAPH /VIEWNAME='Scatterplot' /X1 = VAR(TS_1_lag1) TYPE = SCALE /Y =
  VAR(TS_1) TYPE = SCALE /COORDINATE = VERTICAL  /X1LENGTH=3.0 /YLENGTH=3.0
  /X2LENGTH=3.0 /CHARTLOOK='NONE' /SCATTER COINCIDENT = NONE.
EXE.

IGRAPH /VIEWNAME='Scatterplot' /X1 = VAR(TS_2_lag1) TYPE = SCALE /Y =
  VAR(TS_2) TYPE = SCALE /COORDINATE = VERTICAL  /X1LENGTH=3.0 /YLENGTH=3.0
  /X2LENGTH=3.0 /CHARTLOOK='NONE' /SCATTER COINCIDENT = NONE.
EXE.

IGRAPH /VIEWNAME='Scatterplot' /X1 = VAR(TS_3_lag1) TYPE = SCALE /Y =
  VAR(TS_3) TYPE = SCALE /COORDINATE = VERTICAL  /X1LENGTH=3.0 /YLENGTH=3.0
  /X2LENGTH=3.0 /CHARTLOOK='NONE' /SCATTER COINCIDENT = NONE.
EXE.

```


#### Notes for `R` {-}

If you want to use `R`, just go through the questions and ignore the `SPSS` specific comments. Here are some tips:

**Importing data in `R`**

By downloading:

1. Follow the link, e.g. for [`series.sav`](https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/blob/master/assignments/assignment_data/BasicTSA_arma/series.sav).
2. On the Github page, find a button marked **Download** (or **Raw** for textfiles).
3. Download the file
4. Load it into `R`


```r
library(rio)
series <- import("series.sav", setclass = "tbl_df")
```


By importing from Github:

1. Copy the `url` associated with the **Download**  button on Github (right-clink).
2. The copied path should contain the word 'raw' somewhere in the url.
3. Call `rio::import(url)`

```r
library(rio)
series <- import("https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/raw/master/assignments/assignment_data/BasicTSA_arma/series.sav", setclass = "tbl_df")
```

You can use the functions in the `stats` package: `arima()`, `acf()` and `pacf()`. In package `casnet` you can use the function `plotRED_acf()` (plot REDundancies). `Matlab` has functions that go by slightly different names, check the [Matlab Help pages](https://nl.mathworks.com/help/econ/autocorr.html). 

There are many extensions to these linear models, check the [`CRAN Task View` on `Time Series Analysis`](https://cran.r-project.org/web/views/TimeSeries.html) to learn more (e.g. about package `zoo` and `forecast`).


```r
library(casnet)
library(ggplot2)

plotRED_acf(y = series$TS_1,Lmax = 50, returnCorFun = FALSE)
```

![](ASSIGNMENTS_P2_files/figure-html/unnamed-chunk-11-1.png)<!-- -->![](ASSIGNMENTS_P2_files/figure-html/unnamed-chunk-11-2.png)<!-- -->

```r
plotRED_acf(y = series$TS_2,Lmax = 50, returnCorFun = FALSE)
```

![](ASSIGNMENTS_P2_files/figure-html/unnamed-chunk-11-3.png)<!-- -->![](ASSIGNMENTS_P2_files/figure-html/unnamed-chunk-11-4.png)<!-- -->

```r
plotRED_acf(y = series$TS_3,Lmax = 50, returnCorFun = FALSE)
```

![](ASSIGNMENTS_P2_files/figure-html/unnamed-chunk-11-5.png)<!-- -->![](ASSIGNMENTS_P2_files/figure-html/unnamed-chunk-11-6.png)<!-- -->



### Relative Roughness of the Heart {.tabset .tabset-fade .tabset-pills}

> "We can take it to the end of the *line*     
>  Your love is like a shadow on me all of the *time*     
>  I don't know what to do and I'm always in the dark     
>  We're living in a powder keg and giving off *sparks*"     
>
> --- Bonnie Tyler/James R. Steinman, Total Eclipse of the Heart


Download three different time series of heartbeat intervals (HBI) [here](https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/tree/master/assignments/assignment_data/RelativeRoughness). If you use `R` and have package `rio` installed you can run this code and the load the data into a `data.frame` directly from `Github`.


```r
library(rio)
TS1 <- rio::import("https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/raw/master/assignments/assignment_data/RelativeRoughness/TS1.xlsx", col_names=FALSE)
TS2 <- rio::import("https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/raw/master/assignments/assignment_data/RelativeRoughness/TS2.xlsx", col_names=FALSE)
TS3 <- rio::import("https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/raw/master/assignments/assignment_data/RelativeRoughness/TS3.xlsx", col_names=FALSE)
```

The Excel files did not have any column names, so let's create them in the `data.frame`

```r
colnames(TS1) <- "TS1"
colnames(TS2) <- "TS2"
colnames(TS3) <- "TS3"
```


**The recordings**
These HBI’s were constructed from the[] R-R inter-beat intervals](https://www.physionet.org/tutorials/hrv/) in electrocardiogram (ECG) recordings, as defined in Figure \@ref(fig:RRf1).

<div class="figure" style="text-align: center">
<img src="images/RRfig1.png" alt="Definition of Heart Beat Periods." width="862" />
<p class="caption">(\#fig:RRf1)Definition of Heart Beat Periods.</p>
</div>


 * One HBI series is a sample from a male adult, 62 years old (called *Jimmy*). Approximately two years before the recording, the subject has had a coronary artery bypass, as advised by his physician following a diagnosis of congestive heart failure. *Jimmy* used anti-arrhythmic medicines at the time of measurement.

 * Another HBI series is a sample from a healthy male adult, 21 years old (called *Tommy*). This subject never reported any cardiac complaint. Tommy was playing the piano during the recording.

 * A third supposed HBI series is fictitious, and was never recorded from a human subject (let’s call this counterfeit *Dummy*).


#### Questions {-}

The assignment is to scrutinise the data and find out which time series belongs to *Jimmy*, *Tommy*, and *Dummy* respectively. ^[The HBI intervals were truncated (not rounded) to a multiple of 10 ms (e.g., an interval of 0.457s is represented as 0.450s), and to 750 data points each. The means and standard deviations among the HBI series are approximately equidistant, which might complicate your challenge.]

The chances that you are an experienced cardiologist are slim. We therefore suggest you proceed your detective work as follows:

*	Construct a graphical representation of the time series, and inspect their dynamics visually ( plot your time series).
* Write down your first guesses about which time series belongs to which subject. Take your time for this visual inspection (i.e., which one looks more like a line than a plane, which one looks more 'smooth' than 'rough').
*	Next, explore some measures of central tendency and dispersion, etc.
*	Third, compute the Relative Roughness for each time series, use Equation \@ref(eq:RR)

\begin{equation}
RR = 2\left[1−\frac{\gamma_1(x_i)}{Var(x_i)}\right]
(\#eq:RR)
\end{equation}

The numerator in the formula stands for the `lag 1` auto-covariance of the HBI time series $x_i$. The denominator stands for the (global) variance of $x_i$. Most statistics packages can calculate these variances, `R` and `Matlab` have built in functions. Alternatively, you can create the formula yourself.

*	Compare your (intuitive) visual inspection with these preliminary dynamic quantifications, and find out where each of the HIB series are positions on the ‘colourful spectrum of noises’ (i.e., line them up with Figure \@ref(fig:RRf3)).

<div class="figure" style="text-align: center">
<img src="images/RRfig3.png" alt="Coloured Noise versus Relative Roughness" width="793" />
<p class="caption">(\#fig:RRf3)Coloured Noise versus Relative Roughness</p>
</div>


**What do we know now, that we didn’t knew before?**
Any updates on Jimmy’s, Tommy’s and Dummy’s health? You may start to wonder about the 'meaning' of these dynamics, and not find immediate answers.

Don’t worry; we’ll cover the interpretation over the next two weeks in further depth. Let’s focus the dynamics just a little further for now. It might give you some clues.

* Use the `randperm` function (in `Matlab` or in package  [`pracma`](http://www.inside-r.org/packages/cran/pracma) in `R`) to randomize the temporal ordering of the HBI series. Or try to use the function `sample()`
* Visualize the resulting time series to check whether they were randomized successfully
* Next estimate the Relative Roughness of the randomized series. Did the estimates change compared to your previous outcomes (if so, why)?
* Now suppose you would repeat what you did the previous, but instead of using shuffle you would integrate the fictitious HBI series (i.e., normalize, then use `x=cumsum(x))`. You can look up `cumsum` in `R` or `Matlab`’s Help documentation). Would you get an estimate of Relative Roughness that is approximately comparable with what you got in another HBI series? If so, why?


#### Answers {-}


```r
library(rio)
TS1 <- rio::import("https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/raw/master/assignments/assignment_data/RelativeRoughness/TS1.xlsx", col_names=FALSE)
```

```
## New names:
## * `` -> `..1`
```

```r
TS2 <- rio::import("https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/raw/master/assignments/assignment_data/RelativeRoughness/TS2.xlsx", col_names=FALSE)
```

```
## New names:
## * `` -> `..1`
```

```r
TS3 <- rio::import("https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/raw/master/assignments/assignment_data/RelativeRoughness/TS3.xlsx", col_names=FALSE)
```

```
## New names:
## * `` -> `..1`
```

The Excel files did not have any column names, so let's create them in the `data.frame`

```r
colnames(TS1) <- "TS1"
colnames(TS2) <- "TS2"
colnames(TS3) <- "TS3"
```


```r
# Create a function for RR
RR <- function(ts){
  # lag.max = n gives autocovariance of lags 0 ... n,
  VAR  <- acf(ts, lag.max = 1, type = 'covariance', plot=FALSE)
  # RR formula
  RelR   <- 2*(1-VAR$acf[2] / VAR$acf[1])
  # Add some attributes to the output
  attributes(RelR) <- list(localAutoCoVariance = VAR$acf[2], globalAutoCoVariance = VAR$acf[1])
  return(RelR)
}

# Look at the results
for(ts in list(TS1,TS2,TS3)){
  relR <- RR(ts[,1])
  cat(paste0(colnames(ts),": RR = ",round(relR,digits=3), " = 2*(1-",
         round(attributes(relR)$localAutoCoVariance, digits = 4),"/",
         round(attributes(relR)$globalAutoCoVariance,digits = 4),")\n"))
  }
```

```
## TS1: RR = 0.485 = 2*(1-0.0016/0.0021)
## TS2: RR = 0.118 = 2*(1-0.0018/0.0019)
## TS3: RR = 2.052 = 2*(1--1e-04/0.002)
```

```r
# You can also use function fd_RR() from casnet
fd_RR
```

```
## function (y) 
## {
##     VAR <- stats::acf(y, lag.max = 1, type = "covariance", plot = FALSE)
##     RelR <- 2 * (1 - VAR$acf[2]/VAR$acf[1])
##     attributes(RelR) <- list(localAutoCoVariance = VAR$acf[2], 
##         globalAutoCoVariance = VAR$acf[1])
##     return(RelR)
## }
## <bytecode: 0x7fc0a1dbce78>
## <environment: namespace:casnet>
```

Use Figure \@ref(fig:RRf3) to lookup which value of $RR$ corresponds to which type of dynamics:

**TS1**: Pink noise
**TS2**: Brownian noise
**TS3**: White noise



**Randomize**

To randomize the data you may use the function `sample` (which is easier than `randperm`)


```r
library(pracma)
```

```
## 
## Attaching package: 'pracma'
```

```
## The following object is masked from 'package:casnet':
## 
##     repmat
```

```r
# randperm()
TS1Random <- TS1$TS1[randperm(length(TS1$TS1))]

# sample()
TS1Random <- sample(TS1$TS1, length(TS1$TS1))
TS2Random <- sample(TS2$TS2, length(TS2$TS2))
TS3Random <- sample(TS3$TS3, length(TS3$TS3))


plot.ts(TS1Random)
lines(ts(TS1$TS1),col="red3")
```

![](ASSIGNMENTS_P2_files/figure-html/unnamed-chunk-17-1.png)<!-- -->

If you repeat this for TS2 and TS3 and compute the Relative Roughness of each randomized time series, the outcomes should be around 2, white noise! This makes sense, you destroyed all the correlations in the data by removing the temporal order with which values were observed.


```r
library(casnet)
cat("TS1random\n")
```

```
## TS1random
```

```r
cat(fd_RR(TS1Random))
```

```
## 1.898066
```

```r
cat("\nTS2random\n")
```

```
## 
## TS2random
```

```r
cat(fd_RR(TS2Random))
```

```
## 2.043166
```

```r
cat("\nTS3random\n")
```

```
## 
## TS3random
```

```r
cat(fd_RR(TS3Random))
```

```
## 1.962415
```

**Integrate**

Normalize the white noise time series

```r
TS3Norm <- scale(TS3$TS3)
```

Now integrate it, which just means, 'take the cumulative sum'.

```r
TS3Int <- cumsum(TS3Norm)
plot.ts(TS3Int)
lines(ts(TS3Norm),col="red3")
```

![](ASSIGNMENTS_P2_files/figure-html/unnamed-chunk-20-1.png)<!-- -->

If you compute the Relative Roughness of the integrated time series, the outcome should be close to 0, Brownian noise.

```r
cat("\nTS3Int\n")
```

```
## 
## TS3Int
```

```r
cat(RR(TS3Int))
```

```
## 0.02783704
```


### Sample Entropy  {.tabset .tabset-fade .tabset-pills}

Use the `sample_entropy()` function in package `pracma`.

#### Questions {-}

* Calculate the Sample Entropy of the two sets of three time series you now have.
    + Use your favourite function to estimate the sample entropy of the three time series. Use for instance a segment length `edim` of 3 data points, and a tolerance range `r` of 1 * the standard deviation of the series. What values do you observe?
    + Can you change the absolute SampEn outcomes by 'playing' with the m parameter? If so, how does the outcome change, and why?
    + Can you change the absolute SampEn outcomes by 'playing' with the r parameter If so, how does the outcome change, and why?
    + Do changes in the relative SampEn outcome change the outcomes for the three time series relative to each other?

*	Extra: Go back to the assignment where you generated simulated time series from the logistic map.


#### Answers {-}

Change some of the parameters.


```r
library(rio)
library(pracma)

# ACF assignment data `series`
cat(paste0("\nseries.TS1 m=3\n",sample_entropy(series$TS_1, edim = 3, r = sd(series$TS_1))))
## 
## series.TS1 m=3
## 0.652582760511648
cat(paste0("\nseries.TS2 m=3\n",sample_entropy(series$TS_2, edim = 3, r = sd(series$TS_2))))
## 
## series.TS2 m=3
## 0.195888185092471
cat(paste0("\nseries.TS3 m=3\n",sample_entropy(series$TS_3, edim = 3, r = sd(series$TS_3))))
## 
## series.TS3 m=3
## 0.528115883767288


# ACF assignment data `series`
cat(paste0("\nseries.TS1 m=6\n",sample_entropy(series$TS_1, edim = 6, r = sd(series$TS_1))))
## 
## series.TS1 m=6
## 0.676004479826967
cat(paste0("\nseries.TS2 m=6\n",sample_entropy(series$TS_2, edim = 6, r = sd(series$TS_2))))
## 
## series.TS2 m=6
## 0.167801266934409
cat(paste0("\nseries.TS3 m=6\n",sample_entropy(series$TS_3, edim = 6, r = sd(series$TS_3))))
## 
## series.TS3 m=6
## 0.527677852462416


# ACF assignment data `series`
cat(paste0("\nseries.TS1 m=6, r=.5\n",sample_entropy(series$TS_1, edim = 3, r = .5*sd(series$TS_1))))
## 
## series.TS1 m=6, r=.5
## 1.26945692316692
cat(paste0("\nseries.TS2 m=6, r=.5\n",sample_entropy(series$TS_2, edim = 3, r = .5*sd(series$TS_2))))
## 
## series.TS2 m=6, r=.5
## 0.641584864066123
cat(paste0("\nseries.TS3 m=6, r=.5\n",sample_entropy(series$TS_3, edim = 3, r = .5*sd(series$TS_3))))
## 
## series.TS3 m=6, r=.5
## 0.5894301000077
```

The change of `m` keeps the relative order, change of `r` for the same `m` does not.

**Values of other time series**


```r

# RR assignment data `TS1,TS2,TS3`
cat(paste0("\nTS1\n",sample_entropy(TS1$TS1, edim = 3, r = sd(TS1$TS1))))
## 
## TS1
## 0.260297595725622
cat(paste0("TS1Random\n",sample_entropy(TS1Random, edim = 3, r = sd(TS1Random))))
## TS1Random
## 0.612371128537759

cat(paste0("\nTS2\n",sample_entropy(TS2$TS2, edim = 3, r = sd(TS2$TS2))))
## 
## TS2
## 0.0477314028211898
cat(paste0("TS2Random\n",sample_entropy(TS2Random, edim = 3, r = sd(TS2Random))))
## TS2Random
## 0.591133993373944

cat(paste0("\nTS3\n",sample_entropy(TS3$TS3, edim = 3, r = sd(TS3$TS3))))
## 
## TS3
## 0.612955347930071
cat(paste0("TS3Int\n",sample_entropy(TS3Norm, edim = 3, r = sd(TS3Norm))))
## TS3Int
## 0.612955347930071


# Logistic map
library(casnet)
cat("\nLogistic map\nr=2.9\n")
## 
## Logistic map
## r=2.9
y1<-growth_ac(r = 2.9,type="logistic")
sample_entropy(y1, edim = 3, r = sd(y1))
## [1] 0.0002390343
cat("\nLogistic map\nr=4\n")
## 
## Logistic map
## r=4
y2<-growth_ac(r = 4,type="logistic")
sample_entropy(y2, edim = 3, r = sd(y2))
## [1] 0.5293149
```


