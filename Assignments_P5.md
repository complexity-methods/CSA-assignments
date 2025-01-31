---
title: "DCS Assignments Session 7"
author: "Fred Hasselman, Maarten Wijnants & Merlijn Olthof"
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
    pandoc_args: ["--number-offset","6"]
    
---





# **Quick Links** {-}

* [Main Assignments Page](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/)

</br>
</br>




# **Potential Theory, Catastrophe Theory and Applied Synergetics: Dynamic Complexity**

Start by doing the **Applied Synergetics: Dynamic Complexity** assignments. The assignments on Potential Functions and Catastrophe Theory are extra.



## **Potential Theory** [EXTRA]


### The Phoneme Boundary Model {.tabset .tabset-fade .tabset-pills}

In this assignment we will look at the potential function used to study speech perception in these studies:

[Tuller, B., Case, P., Ding, M., & Kelso, J. A. (1994). The nonlinear dynamics of speech categorization. Journal of Experimental Psychology: Human perception and performance, 20(1), 3.](https://s3.amazonaws.com/academia.edu.documents/41486712/The_Nonlinear_Dynamics_of_Speech_Categor20160123-5343-1c1fpf2.pdf?AWSAccessKeyId=AKIAIWOWYYGZ2Y53UL3A&Expires=1518665892&Signature=B2jjfU7EbMTFATVYtiZxYAxw608%3D&response-content-disposition=inline%3B%20filename%3DThe_nonlinear_dynamics_of_speech_categor.pdf)

[Case, P., Tuller, B., Ding, M., & Kelso, J. S. (1995). Evaluation of a dynamical model of speech perception. Perception & Psychophysics, 57(7), 977-988.](https://link.springer.com/content/pdf/10.3758%2FBF03205457.pdf)

You do not have to read these articles to do the assignment, but they do provide a great introduction to the kind phenomena you can study empirically, guided by a formal model, without fitting model parameters to data. For example, the model predicts phenomena that are unexpected and inexplicable by other theories of speech perception (e.g. hysteresis), most prominent, if the boundary is approached and the same stimulus is presented a number of times, a switch in perception occurs as if the phoneme boundary is crossed. *Prospective prediction by simulation* is an excellent way to test the plausibility of a model.

#### Questions {-}

Have a look at [the spreadsheet in which the model is implemented](https://docs.google.com/spreadsheets/d/1L6mpHx9SewGGfjValMJ2CzQz3uzOx1PWNaNVnZ4zkew/edit?usp=sharing), copy it, and see how the function changes with values of parameter `k` (between $-1$ and $1$, be sure to check $0$. Note that this is not an iterative function, `V` is 'just' a function of `x`.

* Create the function in `R`
    + remember, no iteration needed!
    + plot the shape for different values of `k`, use $-1,\ -0.5,\ 0 0.5,\ 1$

#### Answers {-}


```r
# Variable k
ks = c(-1,-.5,0,.5,1)
x = seq(-2,2,by=.1)
# the function
V <- function(x, k){return(k*x -.5*x^2 + .25*x^4)}

# Plot x against V
op<-par(mfrow=c(1,5))
for(k in ks){
  plot(x,V(x,k),type="l",xlim=c(-2,2),ylim=c(-1.5,2),frame.plot =FALSE,lwd=2)
  abline(h=0,v=0,col="grey")
  }
```

![](Assignments_P5_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

```r
par(op)
```


### A model of risky choice

Have a look at the paper below, it uses a potential function to model non-linear dynamics in decision making and fits model parameters based on experimental data:

[van Rooij, M. M., Favela, L. H., Malone, M., & Richardson, M. J. (2013). Modeling the dynamics of risky choice. Ecological Psychology, 25(3), 293-303.](http://www.tandfonline.com/doi/abs/10.1080/10407413.2013.810502)

See e.g. p 299 and beyond...


More details [in her PhD thesis](https://etd.ohiolink.edu/!etd.send_file?accession=ucin1382951052&disposition=inline).


## ***Catastrophe Theory** [EXTRA]

* These assignments use nonlinear regression andis written to be completed in `SPSS`, but you can try to use the nonlinear regression functions of 'R' as well.
    - Look at the solutions of the *Extra* [Basic Time Series Analysis](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/ASSIGNMENTS_P2.html#2_fitting_parameters_of_analytic_solutions_[extra]) assignments.
* You can also use the R-package `cusp`, or [GEMCAT](https://www.researchgate.net/publication/228299348_An_algorithm_for_estimating_multivariate_catastrophe_models_GEMCAT_II) available as a [Windows  executable](https://www.degruyter.com/view/j/snde.2000.4.3/snde.2000.4.3.1062/snde1062_supplementary_0.zip)


### Fitting the cusp catastrophe in `SPSS` {.tabset .tabset-fade .tabset-pills}

In the file [Cusp Attitude.sav](https://github.com/FredHasselman/DCS/raw/master/assignmentData/Potential_Catastrophe/Cusp%20attitude.sav), you can find data from a (simulated) experiment. Assume the experiment tried to measure the effects of explicit predisposition and [affective conditioning](http://onlinelibrary.wiley.com/doi/10.1002/mar.4220110207/abstract) on attitudes towards Complexity Science, measured in a sample of psychology students using a specially designed Implicit Attitude Test (IAT).


#### Questions {-}

1. Look at a Histogram of the difference score (post-pre) $dZY$. This should look quite normal (pun intended).
   - Perform a regular linear regression analysis predicting $dZY$ (Change in Attitude) from $\alpha$ (Predisposition). 
   - Are you happy with the $R^2$?
2. Look for Catastrophe Flags: Bimodality. Examine what the data look like if we split them across the conditions.
  - Use $\beta$ (Conditioning) as a Split File Variable (`Data >> Split File`). And again, make a histogram of $dZY$.
  - Try to describe what you see in terms of the experiment.
  -	Turn Split File off. Make a Scatterplot of $dZY$ (x-axis) and $\alpha$ (y-axis). Here you see the bimodality flag in a more dramatic fashion. 
   - Can you see another flag?
3. Perhaps we should look at a cusp Catastrophe model:
   - Go to `Analyse >> Regression >> Nonlinear` (also see [Basic Time Series Analysis](#bta)). First we need to tell SPSS which parameters we want to use, press `Parameters.` 
    - Now you can fill in the following: 
        + `Intercept` (Starting value $0.01$)
        + `B1` through `B4` (Starting value $0.01$)
        + Press `Continue` and use $dZY$ as the dependent. 
    - Now we build the model in `Model Expression`, it should say this: 
    ```
    Intercept + B1 * Beta * ZY1 + B2 * Alpha + B3 * ZY1 ** 2 + B4 * ZY1 ** 3
    ```
    - Run! And have a look at $R^2$.
4. The model can also be fitted with linear regression in SPSS, but we need to make some extra (nonlinear) variables using `COMPUTE`:

```
BetaZY1 = Beta*ZY1	*(Bifurcation, splitting parameter).
ZY1_2 = ZY1 ** 2	*(ZY1 Squared).
ZY1_3 = ZY1 ** 3	*(ZY1 Cubed).
```
  - Create a linear regression model with $dZY$ as dependent and $Alpha$, $BetaZY1$ and $ZY1_2$ en $ZY1_3$ as predictors. 
  - Run! The parameter estimates should be the same.
5. Finally try to can make a 3D-scatterplot with a smoother surface to have look at the response surface.
  - HINT: this is a lot easier in `R` or `Matlab` perhaps you can export your `SPSS` solution.

6. How to evaluate a fit? 
   - Check the last slides of lecture 8 in which the technique is summarised. 
   - The cusp has to outperform the `pre-post` model.

#### Answers {-}

Some syntax (figures) might fail on the newest versions of SPSS.

```
GRAPH
  /HISTOGRAM=dZY .

*Linear regression.

REGRESSION
  /MISSING LISTWISE
  /STATISTICS COEFF OUTS R ANOVA
  /CRITERIA=PIN(.05) POUT(.10)
  /NOORIGIN
  /DEPENDENT dZY
  /METHOD=ENTER Alpha.

*Flags 1.

SORT CASES BY Beta .
SPLIT FILE
  SEPARATE BY Beta .

GRAPH
  /HISTOGRAM=dZY .

SPLIT FILE
  OFF.

*Flags 2.

GRAPH
  /SCATTERPLOT(BIVAR)=dZY WITH Alpha
  /MISSING=LISTWISE .

*Cusp with nonlinear regression.

MODEL PROGRAM Intercept=0.01 B1=0.01 B2=0.01 B3=0.01 B4=0.01 .
COMPUTE PRED_ = Intercept + B1 * Beta * ZY1 + B2 * Alpha + B3 * ZY1 ** 2 + B4 * ZY1 ** 3.
NLR dZY
  /PRED PRED_
  /CRITERIA SSCONVERGENCE 1E-8 PCON 1E-8 .

*Cusp with linear regression.

COMPUTE BetaZY1 = Beta*ZY1 .
COMPUTE ZY1_2 = ZY1 ** 2 .
COMPUTE ZY1_3 = ZY1 ** 3 .
EXECUTE .

REGRESSION
  /MISSING LISTWISE
  /STATISTICS COEFF OUTS R ANOVA
  /CRITERIA=PIN(.05) POUT(.10)
  /NOORIGIN
  /DEPENDENT dZY
  /METHOD=ENTER Alpha BetaZY1 ZY1_2 ZY1_3  .

*3-D scatter.

IGRAPH /VIEWNAME='Scatterplot' /X1 = VAR(Beta) TYPE = SCALE /Y = VAR(dZY) TYPE = SCALE /X2 = VAR(Alpha) TYPE = SCALE
 /COORDINATE = THREE  /FITLINE METHOD = LLR NORMAL BANDWIDTH = FAST X1MULTIPLIER = 1.00  X2MULTIPLIER = 1.00 LINE = TOTAL
  SPIKE=OFF /X1LENGTH=3.0 /YLENGTH=3.0 /X2LENGTH=3.0 /CHARTLOOK='D:\Program Files\SPSS\Looks\dots.clo' /SCATTER COINCIDENT =
  NONE.
EXE.
```

Download this [SPSS syntax from GitHub](https://raw.githubusercontent.com/FredHasselman/DCS/master/assignmentData/Potential_Catastrophe/Cusp%20attitude%20solution.SPS)



### The `cusp` package in `R`

Use this tutorial paper to fit the cusp in 'R' according to a maximum likelihood criterion.

[Grasman, R. P. P. P., Van der Maas, H. L. J., & Wagenmakers, E. (2007). Fitting the Cusp Catastrophe in R: A cusp-Package.](https://cran.r-project.org/web/packages/cusp/vignettes/Cusp-JSS.pdf)

1. Start R and install package `cusp` 

2. Work through the *Example I* (attitude data) in the paper (*Section 4: Examples*, p. 12).

3. *Example II* in the same section is also interesting, but is based on simulated data.
    * Try to think of an application to psychological / behavioural science.


# **Applied Synergetics: Dynamic Complexity**


## *Early Warning Signals in a Clinical Case Study*

Dynamic assessment (e.g. ecological momentary assesment) is increasingly used in the mental health field. Process monitoring with dynamic assessment can empower patients and benefit self-insight (van Os et al., 2017). Additionally, ecological momentary assesment (EMA) time series can be analyzed and the results can be used to inform the treatment process. 
In this assignment, we will use complex systems theory and methodology to perform a quantitative case study for a real patient. 

### First Steps {.tabset .tabset-fade .tabset-pills}

You will analyze EMA data from a patient who received impatient psychotherapy for mood disorders. The patient answered questions from the therapy process questionnaire on a daily basis for the full 101 days of his treatment. 
The learning objectives of the assignments are as follows:
1) You can interpret plots of raw EMA time series, as well as complexity resonance diagrams and critical instability diagrams of EMA time series.
2) You can describe what possible change processes in complex systems could explain the quantitative results from the case study. 


#### Questions {-}

a) Open R and load the data 'clinical.case.study.csv' to a dataframe called df. 
b) Look at the data frame. Where is time in this data? In the rows or the columns?
c) Add a time variable called 'days' to the dataframe. 
d) Factor 7 yields the scores on 'Impairment by Symptoms and Problems' for this patient on each day. Plot the time series. What do you see?


#### Answers {-}

a) The data file is on [Github](https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/blob/master/assignments/assignment_data/EarlyWarningSignals/clinical.case.study.csv)

```r
library(rio)
df <- import("https://raw.githubusercontent.com/FredHasselman/The-Complex-Systems-Approach-Book/master/assignments/assignment_data/EarlyWarningSignals/clinical.case.study.csv")
```

b) In the rows.

c) 

```r
df$days <- (1:nrow(df))
```
d) 

```r
plot(df$days, df$factor7, type='l')
```

![](Assignments_P5_files/figure-html/unnamed-chunk-5-1.png)<!-- -->


### Phase Transitions and Early-Warning Signals {.tabset .tabset-fade .tabset-pills}

Before we continue, read this short summary of what you (should) know about phase transitions and early-warning signals: 

\BeginKnitrBlock{rmdkennen}<div class="rmdkennen">Change from one attractor to another is called a phase transition. Phase transitions are preceded by a destabilization period in which the stability of the existing state decreases (as a consequence of increasing control parameters). During destabilization, the system loses its resilience to external influences leading to increased fluctuations and disorder in the systems behaviour (critical fluctuations) and an increased return time to the existing state after perturbation (critical slowing down). Destabilization ends abruptly when the system makes a phase transition towards a new stable state. Critical fluctuations and critical slowing down can therefore serve as early-warning signals (EWS) for phase transitions. </div>\EndKnitrBlock{rmdkennen}

#### Questions {-}

Answer the following questions, please think and discuss with your neighbours before turning to the answers. You can use the literature and what you've learned in the lecture:

a) Can you think of possible phase transitions in psychopathology? Give examples. 
b)What is the role of destabilization in the treatment of psychopathology?
c) Look at the plot of factor 7 for our patient. Can you visualize a possible phase transition?
d) Do you expect to find early-warning signals in the data of our patient? And where?

#### Answers {-}

a) Onset of pathology (e.g. psychosis), relapse in addiction, suicide attempts, sudden gains and losses, etc.
b) Destabilization is most of the time a good thing in treatment: the goal of treatment is to change (destabilize) the existing state.
During destabilization, the patient is increasingly sensitive to external influences (including treatment). Destabilization can be a target period for intervention.
c) There is a sudden gain (abrupt and persisting decrease in symptoms) somewhere around day 40.
d) Before the sudden gain

### Phase transitions in symptom severity {.tabset .tabset-fade .tabset-pills}

In the last question, you might have hypothesized the occurrence of a phase transition in the impairment by symptoms and problems of our patient (sudden gain). Let's try a method to quantify level shifts, including sudden gains, in time series data.  

#### Questions {-}

a)	Analyze the time series of factor 7 using the function `ts_levels()` in casnet. Look at the manual pages to get an idea what is going. Use this code: `casnet::ts_levels(df$factor7, minLevelChange=1, minLevelDuration = 7, changeSensitivity = 0.01, maxLevels=30, method="anova")`.
b) Look at the `summary()` of the `tree` field in the output object.
c) Plot the results in the `pred` field of the output object (`x` is `time`, `y` is the original series, `p` is the predicted level of the original series.
d) Describe verbally the change trajectory in the symptom severity of our patient. 

#### Answers {-}

a) This method uses regression trees to quantify segments of the time series. This is OK for descriptive purposes. Ideally, we would classify a phase transition in a qualitative manner (remember, it is a qualitative shift) but the ts_levels function is very useful to identify discontinous shifts in time series data. 


```r
library(casnet)
library(ggplot2)
library(tidyr)
lvl <- ts_levels(df$factor7, minDataSplit = 12, minLevelDuration = 7, changeSensitivity = 0.05, maxLevels=30, method="anova") 
```

b) Look for `primary splits`


```r
summary(lvl$tree)
```

```
## Call:
## rpart::rpart(formula = y ~ x, data = dfs, method = method, control = list(minsplit = minDataSplit, 
##     minbucket = minLevelDuration, maxdepth = maxLevels, cp = changeSensitivity))
##   n= 101 
## 
##          CP nsplit rel error    xerror      xstd
## 1 0.4399209      0 1.0000000 1.0173104 0.1485233
## 2 0.0500000      1 0.5600791 0.6173724 0.1190736
## 
## Variable importance
##   x 
## 100 
## 
## Node number 1: 101 observations,    complexity param=0.4399209
##   mean=6.930683e-11, MSE=0.4817495 
##   left son=2 (62 obs) right son=3 (39 obs)
##   Primary splits:
##       x < 39.5 to the right, improve=0.4399209, (0 missing)
## 
## Node number 2: 62 observations
##   mean=-0.3651189, MSE=0.2190611 
## 
## Node number 3: 39 observations
##   mean=0.5804455, MSE=0.350508
```

c)

```r
# ggplot likes the long data format
lvl_long <- lvl$pred %>% 
  gather(key=variable, value=value, -x)

ggplot(lvl_long, aes(x=x, y=value, colour=variable)) + geom_line() + theme_bw()
```

![](Assignments_P5_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

d) Flutucations, shift, fluctuations.

### Early Warning Signals {.tabset .tabset-fade .tabset-pills}

Now it is time to analyze the dynamic complexity of the ES time series and see if we can find early-warning signals. 

#### Questions {-}

a) Some questions in the data where answered on a scale from 0 to 100, others from 0 to 6. We must rescale all questions that are 0-100 to the scale 0-6 before we can analyze EWS. If you like, you can try to write a for loop for this. You can also look at the first answer.  
b) Analyze the dynamic complexity of the ES time series using the function dyn_comp(), ask for a plot.
c) The function returns a list, what is in the list?
d) Look at the complexity resonance diagram, do you see early-warning signals for the sudden gain?
e) Analyze the presence of critical instabilities using the function crit_in(), set doPlot=TRUE
f) Look at the critical instability diagram, do you see early-warning signals for the sudden gain?
g) Plot the time series of the average dynamic complexity of all items 
h) Plot the time series of factor 7 and the time series of the average dynamic complexity in one plot
i) You get a warning that there are NA values. Why are there NA's?

#### Answers {-}

a) rescale predictors to max=6

```r
for(j in (9:50)){
  if(max(df[,j])>6){
    df[,j] <- (df[,j]/100)*6
  }
}
```
b) 

```r
dc.case <- dyn_comp(df, win=7, col_first=9, col_last=50, scale_min=0, scale_max=6, doPlot = TRUE)
```

![](Assignments_P5_files/figure-html/unnamed-chunk-11-1.png)<!-- -->![](Assignments_P5_files/figure-html/unnamed-chunk-11-2.png)<!-- -->
c) dataframe + plot object
d) 

```r
dc.case$plot.dc
```

![](Assignments_P5_files/figure-html/unnamed-chunk-12-1.png)<!-- -->
e) 

```r
ci.case <- crit_in(dc.case$df.comp, 7)
```

![](Assignments_P5_files/figure-html/unnamed-chunk-13-1.png)<!-- -->![](Assignments_P5_files/figure-html/unnamed-chunk-13-2.png)<!-- -->
f) 

```r
ci.case$plot.ci
```

![](Assignments_P5_files/figure-html/unnamed-chunk-14-1.png)<!-- -->
g) 

```r
dc.mean <- rowMeans(as.matrix(dc.case$df.comp))
plot(dc.mean, type='l')
```

![](Assignments_P5_files/figure-html/unnamed-chunk-15-1.png)<!-- -->
h) 

```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(ggplot2)

# Create a long dataset for ggplot
# Here we use the syntax from the `tidyverse` set of packages to create a 
df %>% 
  mutate(MeanDC=ts_standardise(dc.mean)) %>% 
  select(days,factor7,MeanDC) %>% 
  gather(key=variable,value=value,-days) %>%
  ggplot(aes(x=days, y=value, colour=variable)) + geom_line() + theme_bw()
```

```
## Warning: Removed 6 rows containing missing values (geom_path).
```

![](Assignments_P5_files/figure-html/unnamed-chunk-16-1.png)<!-- -->
i) Because dynamic complexity is analyzed over a backwards window. The first value is at day 7.



