---
title: "Time Series Analysis: Temporal Correlations and Fractal Scaling"
author: "Fred Hasselman & Maarten Wijnants"
date: "1/14/2018"
output: 
  html_document: 
    fig_caption: yes
    highlight: pygments
    keep_md: yes
    number_sections: no
    theme: spacelab
    toc: yes
    toc_float: true
    collapsed: false
    smooth_scroll: true
    code_folding: show

---



# **Basic Timeseries Analysis** 

In this course we will not discuss the type of linear time series models known as Autoregressive Models (e.g. AR, ARMA, ARiMA, ARfiMA) summarised on [this Wikipedia page on timeseries](https://en.wikipedia.org/wiki/Time_series#Models). We will in fact be discussing a lot of methods in a book the Wiki page refers to for *'Further references on nonlinear time series analysis'*: [**Nonlinear Time Series Analysis** by Kantz & Schreiber](https://www.cambridge.org/core/books/nonlinear-time-series-analysis/519783E4E8A2C3DCD4641E42765309C7). You do not need to buy the book, but it can be a helpful reference if you want to go beyond the formal level (= mathematics) used in this course. Some of the packages we use are based on the acompanying software [**TiSEAN**](https://www.pks.mpg.de/~tisean/Tisean_3.0.1/index.html) which is written in `C` and `Fortran` and can be called from the commandline (Windows / Linux).


## **Correlation functions**  

Correlation functions are intuitive tools for quantifiying the temporal structure in a time series. As you know, correlation can only quantify linear regularities between variables, which is why we discuss them here as `basic` tools for time series analysis. So what are the variables? In the simplest case, the variables between which we calculate a correlation are between a datapoint at time *t* and a data point that is seperated in time by some *lag*, for example, if you would calculate the correlation in a lag-1 return plot, you would have calculated the 1st value of the correlation function (actually, it is 2nd value, the 1st value is the correlation of time series with itself, the lag-0 correlation, which is of course $r = 1$)  

### ACF and PCF {.tabset .tabset-fade .tabset-pills}


You can do the analyses in SPSS or in `R`, but this analysis is very common so you'll find functions called `acf`, `pacf`and `ccf` in many other statistical software packages,


#### Questions (SPSS) {-}

* Download the file [`series.sav`](https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/blob/master/assignments/assignment_data/BasicTSA_arma/series.sav) from Github. 

It contains three time series `TS_1`, `TS_2` and `TS_3`. As a first step look at the mean and the standard deviation (`Analyze` >> `Descriptives`).  Suppose these were time series from three subjects in an experiment, what would you conclude based on the means and SD???s?  

* Let???s visualize these data. Go to `Forecasting` >> `Time Series` >> `Sequence Charts`. Check the box One chart per variable and move all the variables to Variables. Are they really the same?  

* Let???s look at the `ACF` and `PCF`
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


<!-- 4. You should have identified just one time series with autocorrelations: `TS_2`. Try to fit an `ARIMA(p,0,q)` model on this time series.  -->
<!--     - Go to `Analyze` >> `Forecasting` >> `Create Model`, and at `Method` (Expert modeler) choose `ARIMA`.  -->
<!--     - Look back at the `PACF` to identify which order (`p`) you need (last lag value at which the correlation is still significant). This lag value should go in the Autocorrelation p box.  -->
<!--     - Start with a Moving Average `q` of one. The time series variable `TS_2` is the `Dependent`.  -->
<!--     - You can check the statistical significance of the parameters in the output under `Statistics`, by checking the box `Parameter Estimates`.  -->
<!--     - This value for `p` is probably too high, because not all AR parameters are significant.  -->
<!--     - Run ARIMA again and decrease the number of AR parameters by leaving out the non-significant ones.   -->

<!-- 5. By default `SPSS` saves the predicted values and 95% confidence limits (check the data file). We can now check how well the prediction is: Go to `Graphs` >> `Legacy Dialogs` >> `Line.` Select `Multiple` and `Summaries of Separate Variables`. Now enter `TS_2`, `Fit_X`, `LCL_X` and `UCL_X` in `Lines Represent`. `X` should be the number of the last (best) model you fitted, probably 2. Enter `TIME` as the `Category Axis`.   -->

* In the simulation part of this course we have learned a very simple way to explore the dynamics of a system: The return plot. The time series is plotted against itself shifted by 1 step in time. 
    * Create return plots (use a Scatterplot) for the three time series. Tip: You can easily create a `t+1` version of the time series by using the LAG function in a `COMPUTE` statement. For instance: 
    
```
COMPUTE TS_1_lag1 = LAG(TS_1)
``` 
    * Are your conclusions about the time series the same as in 3. after interpreting these return plots? 

#### Answers (SPSS) {-}




#### Notes for `R` {-}

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

1. Copy the `url` associated with the **Download**  button on Github (right-clik).
2. The copied path should contain the word 'raw' somewhere in the url.
3. Call `rio::import(url)`


```r
library(rio)
series <- import("https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/raw/master/assignments/assignment_data/BasicTSA_arma/series.sav", setclass = "tbl_df")
```

You can use the functions in the `stats` package: `arima()`, `acf()` and `pacf()` (`Matlab` has functions that go by slightly different names, check the [Matlab Help pages](https://nl.mathworks.com/help/econ/autocorr.html)). 

There are many extensions to these linear models, check the [`CRAN Task View` on `Time Series Analysis`](https://cran.r-project.org/web/views/TimeSeries.html) to learn more (e.g. about package `zoo` and `forecast`).



### Relative Roughness of the Heart

> "We can take it to the end of the line
Your love is like a shadow on me all of the time (all of the time)
I don't know what to do and I'm always in the dark
We're living in a powder keg and giving off sparks"
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

### The recordings
These HBI???s were constructed from the R-R intervals in electrocardiogram (ECG) recordings, as defined in Figure \@ref(fig:RRf1).

<div class="figure" style="text-align: center">
<img src="images/RRfig1.png" alt="Definition of Heart Beat Periods." width="862" />
<p class="caption">Definition of Heart Beat Periods.</p>
</div>


 * One HBI series is a sample from a male adult, 62 years old (called *Jimmy*). Approximately two years before the recording, the subject has had a coronary artery bypass, as advised by his physician following a diagnosis of congestive heart failure. *Jimmy* used antiarrhythmic medicines at the time of measurement.

 * Another HBI series is a sample from a healthy male adult, 21 years old (called *Tommy*). This subject never reported any cardiac complaint. Tommy was playing the piano during the recording.

 * A third supposed HBI series is fictitious, and was never recorded from a human subject (let???s call this counterfeit *Dummy*).
Your challenge

The assignment is to scrutinise the data and find out which time series belongs to *Jimmy*, *Tommy*, and *Dummy* respectively. ^[The HBI intervals were truncated (not rounded) to a multiple of 10 ms (e.g., an interval of 0.457s is represented as 0.450s), and to 750 data points each. The means and standard deviations among the HBI series are approximately equidistant, which might complicate your challenge.]


### First inspection
The chances that you are an experienced cardiologist are slim. We therefore suggest you proceed your detective work as follows:

*	Construct a graphical representation of the time series, and inspect their dynamics visually ( plot your time series).
* Write down your first guesses about which time series belongs to which subject. Take your time for this visual inspection (i.e., which one looks more like a line than a plane, which one looks more 'smooth' than 'rough').
*	Next, explore some measures of central tendency and dispersion, etc.
*	Third, compute the Relative Roughness for each time series, use Equation \@ref(eq:RR)

\begin{equation}
RR = 2\left[1???\frac{\gamma_1(x_i)}{Var(x_i)}\right]
(\#eq:RR)
\end{equation}

The numerator in the formula stands for the `lag 1` autocovariance of the HBI time series $x_i$. The denominator stands for the (global) variance of $x_i$. Most statistics packages can calculate these variances, `R` and `Matlab` have built in functions. Alternatively, you can create the formula yourself.

*	Compare your (intuitive) visual inspection with these preliminary dynamic quantifications, and find out where each of the HIB series are positions on the ???colorful spectrum of noises??? (i.e., line them up with Figure \@ref(fig:RRf3)).

<div class="figure" style="text-align: center">
<img src="images/RRfig3.png" alt="Coloured Noise versus Relative Roughness" width="793" />
<p class="caption">Coloured Noise versus Relative Roughness</p>
</div>


### What do we know now, that we didn???t knew before?
Any updates on Jimmy???s, Tommy???s and Dummy???s health? You may start to wonder about the 'meaning' of these dynamics, and not find immediate answers.

Don???t worry; we???ll cover the interpretation over the next two weeks in further depth. Let???s focus the dynamics just a little further for now. It might give you some clues.

* Use the `randperm` function (in `Matlab` or in package  [`pracma`](http://www.inside-r.org/packages/cran/pracma) in `R`) to randomize the temporal ordering of the HBI series.
* Visualize the resulting time series to check whether they were randomized successfully
* Next estimate the Relative Roughness of the randomized series. Did the estimates change compared to your previous outcomes (if so, why)?
* Now suppose you would repeat what you did the previous, but instead of using shuffle you would integrate the fictitious HBI series (i.e., normalize, then use `x=cumsum(x))`. You can look up `cumsum` in `R` or `Matlab`???s Help documentation). Would you get an estimate of Relative Roughness that is approximately comparable with what you got in another HBI series? If so, why?


### Sample Entropy


* Calculate the Sample Entropy
    + Use your favourite function to estimate the sample entropy of the three time series. Use for instance a segment length m of 3 datapoints, and a tolerance range r of 1 standard deviation. What values do you observe?
    +Can you change the absolute SampEn outcomes by 'playing' with the m parameter? If so, how does the outcome change, and why?
( Can you change the absolute SampEn outcomes by 'playing' with the r parameter If so, how does the outcome change, and why?
    + Do changes in the relative SampEn outcome change the outcomes for the three time series relative to each other?

*	Extra: Go back to the assignment where you generated simulated time series from the logistic map.





<!-- # **Fluctuation and Disperion analyses I** {#fda1} -->

<!-- ```{block2, L5, type='rmdimportant'} -->
<!-- Before you begin, look at the notes for [Lecture 4](#lecture-4). -->
<!-- ``` -->

<!-- ## The Spectral Slope {#psd} -->

<!-- We can use the power spectrum to estimate a **self-affinity parameter**, or scaling exponent. -->

<!-- * Download `ts1.txt`, `ts2.txt`, `ts3.txt` [here](https://github.com/FredHasselman/DCS/tree/master/assignmentData/Fluctuation_PSDslope). If you use `R` and have package `rio` installed you can run this code. It loads the data into a `data.frame` object directly from `Github`. -->
<!-- ```{r, echo=TRUE, eval=FALSE, include=TRUE} -->
<!-- library(rio) -->
<!-- TS1 <- rio::import("https://raw.githubusercontent.com/FredHasselman/DCS/master/assignmentData/Fluctuation_PSDslope/ts1.txt") -->
<!-- TS2 <- rio::import("https://raw.githubusercontent.com/FredHasselman/DCS/master/assignmentData/Fluctuation_PSDslope/ts2.txt") -->
<!-- TS3 <- rio::import("https://raw.githubusercontent.com/FredHasselman/DCS/master/assignmentData/Fluctuation_PSDslope/ts3.txt") -->

<!-- # These objects are now data.frames with one column named V1. -->
<!-- # If you want to change the column names -->
<!-- colnames(TS1) <- "TS1" -->
<!-- colnames(TS2) <- "TS2" -->
<!-- colnames(TS3) <- "TS3" -->
<!-- ``` -->

<!-- * Plot the three 'raw' time series. -->

<!-- ### Basic data checks and preparations -->

<!-- For spectral analysis we need to check some data assumptions (see [notes on data preparation, Lecture 4](#data-considerations)). -->

<!-- #### Normalize {-} -->
<!-- 1. Are the lengths of the time series a power of 2? (Use `log2(length of var)` ) -->
<!--   + Computation of the frequency domain is greatly enhanced if data length is a power (of 2). -->
<!-- 2. Are the data normalized? (we will *not* remove datapoints outside 3SD) -->
<!--     + To normalize we have to subtract the mean from each value in the time series and divide it by the standard deviation, the function `scale()` can do this for you, but you could also use `mean()` and `sd()` to construct your own function. -->
<!-- 3. Plot the normalized time series. -->

<!-- #### Detrend {-} -->
<!-- Before a spectral analysis you should remove any linear trends (it cannot deal with nonstationary signals!) -->

<!-- 1. Detrend the normalized data (just the linear trend). -->
<!--     + This can be done using the function `pracma::detrend()`. -->
<!--     + Extra: Try to figure out how to detrend the data using `stats::lm()` or `stats::poly()` -->
<!-- 2. Plot the detrended data. -->

<!-- #### Get the log-log slope in Power Spectral Density {-} -->
<!-- The function `fd.psd()` will perform the spectral slope fitting procedure. -->

<!-- 1. Look at the manual pages to figure out how to call the function. The manual is on blackboard and [Github](https://github.com/FredHasselman/DCS/blob/master/functionLib/) -->
<!--     + Remember, we have already normalized and detrended the data. -->
<!--     + You can also look at the code itself by selecting the function name in`R` and pressing `F2` -->
<!-- 2. Calculate the spectral slopes for the three normalized and detrended time series. -->
<!--     + Call with `plot = TRUE` -->
<!--     + Compare the results... What is your conclusion? -->


<!-- ## DFA and SDA {#dfa} -->

<!-- * Use the functions `fd.dfa()` and `fd.sda()` to estimate the self-affinity parameter and Dimension of the series. -->
<!--     + Check what kind of data preparation is required for SDA and DFA in [notes on data preparation, Lecture 4](#data-considerations). -->
<!--     + Compare the results between the three different methods. -->

<!-- [| jump to solution |](#dfasol) -->

<!-- ## Heartbeat dynamics II {#hrv2} -->
<!-- In the [previous assignment](#relR), you were presented with three different time series of heartbeat intervals (HBI), and you analyzed them using a measure of Relative Roughness (RR; cf. Marmelat & Deligni??res, 2012). -->

<!-- A logical step is to unleash the full force of your new analytic toolbox onto the HBI series. -->

<!-- * Keep track of the outcomes of each time series for 4 different analyses (RR, PSD, SDA, DFA). -->
<!--     + Do the outcomes of the different methods converge on the continuum of blue, white and pink, to Brownian and black noise? That is, do they indicate the same type of temporal structure? -->
<!-- * As a final step, construct return plots for each time series and try to interpret what you observe, given the outcomes of the scaling parameter estimates. -->


<!-- ## Analysis of Deterministic Chaos {#chaos} -->

<!-- * Generate a chaotic timeseries (e.g. $r = 4$ ) of equal length as the time series used above (use the function `growth.ac( ..., type = "logistic")` in `nlRtsa_SOURCE`, see the [solutions of Lecture 1 and 2](#linear-and-logistic-growth)) -->
<!--     + This is in fact one of the series you analysed in a [previous assignment](#pacf). If you still have the results use them for the next part. -->
<!-- * Get all the scaling quantities for this series as well as the ACF and PACF and [some return plots](#the-return-plot) just like in the previous assignments. -->
<!--     + Compare the results to e.g. the heartbeat series. -->

<!-- [| jump to solution |](#chaossol) -->

<!-- # Fluctuation and Disperion analyses II {#fda2} -->

<!-- There were no assignments for this Lecture. -->

