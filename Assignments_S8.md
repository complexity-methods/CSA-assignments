---
title: "DCS Assignments Session 8"
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
    pandoc_args: ["--number-offset","7"]
    
---





# **Quick Links** {-}

* [Main Assignments Page](https://darwin.pwo.ru.nl/skunkworks/courseware/1819_DCS/assignments/)

</br>
</br>


# **Complex Networks**

To complete these assignments you need these packages:

```r
library(igraph)
library(qgraph)
library(casnet) # 
# devtools::install_github("FredHasselman/casnet")
```


* There are many frameworks for analysing and plotting graphs and they are implemented on different platforms often in more-or-less the same way. We'll use the `igraph` framework: https://igraph.org for `R`, but there many more options, see e.g. https://github.com/briatte/awesome-network-analysis#r
* The manual pages of package `igraph` in `R` provide a lot of information, but not everything the package can do. One thing that is missing are  the parameters available to customise the graphs for plotting. If you can't find something in the `R` manual, have a look at the online documentation: https://igraph.org/r/doc/plot.common.html
* Package `qgraph` was developped to fit and analyse the so-called `Gaussian Graph Model`, e.g in the symptom network studies. It is built on the `igraph` framework and you can transform `qgraph` output to an `igraph` object using the function `qgraph::as.igraph.qgraph()`
* The functions in package `casnet` developped to create, analyse and plot **recurrencde networks** are also based on the `igraph` framework. 


## Creating, plotting and describing simple graphs {.tabset .tabset-fade .tabset-pills}

We'll start using `igraph` and create some simple graphs. 

To create a small ring graph and plot it run the code below.


```r
gR <- graph.ring(20)

# This will display the igraph structure
gR
```

```
## IGRAPH e270668 U--- 20 20 -- Ring graph
## + attr: name (g/c), mutual (g/l), circular (g/l)
## + edges from e270668:
##  [1]  1-- 2  2-- 3  3-- 4  4-- 5  5-- 6  6-- 7  7-- 8  8-- 9  9--10 10--11
## [11] 11--12 12--13 13--14 14--15 15--16 16--17 17--18 18--19 19--20  1--20
```

```r
# To plot, simply call:
plot(gR)
```

![](Assignments_S8_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

You can request to represent the graph as a so-called *edge-list* which just lists the edges in a graph by reporting the nodes the edge connects *from* - *to*


```r
get.edgelist(gR)
```

```
##       [,1] [,2]
##  [1,]    1    2
##  [2,]    2    3
##  [3,]    3    4
##  [4,]    4    5
##  [5,]    5    6
##  [6,]    6    7
##  [7,]    7    8
##  [8,]    8    9
##  [9,]    9   10
## [10,]   10   11
## [11,]   11   12
## [12,]   12   13
## [13,]   13   14
## [14,]   14   15
## [15,]   15   16
## [16,]   16   17
## [17,]   17   18
## [18,]   18   19
## [19,]   19   20
## [20,]    1   20
```

Other useful functions are `E()` and `V()` for manipulating attributes of *Edges* (connections) and *Vertices* (nodes). See  https://igraph.org/r/doc/plot.common.html for all available attributes.


```r
# List vertices
V(gR) 
```

```
## + 20/20 vertices, from e270668:
##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20
```

```r
# List edges
E(gR)
```

```
## + 20/20 edges from e270668:
##  [1]  1-- 2  2-- 3  3-- 4  4-- 5  5-- 6  6-- 7  7-- 8  8-- 9  9--10 10--11
## [11] 11--12 12--13 13--14 14--15 15--16 16--17 17--18 18--19 19--20  1--20
```

```r
# Set vertex color
V(gR)$color <- "red3"
# set vertex label
V(gR)$label.color <- "white"
# Set vertex shape
V(gR)$shape <- "square"
# Set edge color
E(gR)$color <- "steelblue"
# Set efge size
E(gR)$width <- 4

# Plot
plot(gR)
```

![](Assignments_S8_files/figure-html/unnamed-chunk-5-1.png)<!-- -->


### Questions {-}

* What will be the degree of each node in the ring graph?
    - To check your answer, look in the manual pages of the `igraph` package for a function that will get you the degree of each node
    - Also, get the average path length.
* Create a "small world" graph like the one descibed by [Watts & Strogatz (1998)](https://www.nature.com/articles/30918) and plot it, using the `igraph` function `watts.strogatz.game()`. 
     - Use dimension `dim = 1`, number of nodes `size = 20`, neighbourhood `nei = 5`, and connection probability `p = .05`
     - Get the degree, average path length and transitivity of this graph.
     - `qgraph` has a function called `smallworldness()` that allows you to calculate the so-called *Small Worls Index* [(Humphries & Gurney, 2008)](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0002051). Calulate the smallwordlness of the graph, a value `> 1` implies a small world network. 
     - 
* Create a directed scale-free graph using `igraph` function `barabasi.game()` with 100 nodes `n=100`.
     - Get the degree, average path length and transitivity
     - There should be a power-law relation between the number og nodes that have a specific degree... (but this is a very small network)


### Answers {-}



```r
# Ring graph degree
degree(gR)
```

```
##  [1] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2
```

```r
# avergage path length
average.path.length(gR)
```

```
## [1] 5.263158
```




```r
# Small world network
gSW <- igraph::watts.strogatz.game(dim = 1, size = 20, nei = 5, p =0.05)

# Plot
plot(gSW)
```

![](Assignments_S8_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

```r
# Degree
cbind(node = V(gSW), connections = degree(gSW))
```

```
##       node connections
##  [1,]    1          11
##  [2,]    2          11
##  [3,]    3          10
##  [4,]    4          11
##  [5,]    5           9
##  [6,]    6          10
##  [7,]    7          10
##  [8,]    8          11
##  [9,]    9          10
## [10,]   10          13
## [11,]   11          10
## [12,]   12          10
## [13,]   13           9
## [14,]   14           9
## [15,]   15          10
## [16,]   16           8
## [17,]   17           9
## [18,]   18           9
## [19,]   19          11
## [20,]   20           9
```

```r
# Average path length
igraph::average.path.length(gSW)
```

```
## [1] 1.473684
```

```r
# Transitivity
igraph::transitivity(gSW)
```

```
## [1] 0.5986842
```

```r
# Complute the Small World Index, aka 'smallworldness'
qgraph::smallworldness(gSW)
```

```
##       smallworldness         trans_target averagelength_target 
##            1.2308818            0.5986842            1.4736842 
##          trans_rnd_M         trans_rnd_lo         trans_rnd_up 
##            0.4863882            0.4473520            0.5230428 
##  averagelength_rnd_M averagelength_rnd_lo averagelength_rnd_up 
##            1.4736895            1.4736842            1.4736842
```



```r
# Scale-free network
gSF <- barabasi.game(100)

## Change some aesthetics
# No numbers in nodes
V(gSF)$name <- ""
# Change node size
V(gSF)$size = 5
# Change Edge size
E(gSF)$arrow.size = .5
E(gSF)$arrow.width = .5


# Plot
plot(gSF)
```

![](Assignments_S8_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

```r
# Degree
rbind(node = V(gSF), connections = degree(gSF))
```

```
##                                                                           
## node        1  2 3 4  5 6 7 8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23
## connections 7 11 3 9 10 6 4 1 15  8  1  2  1  1  6  3  3  2  1  2  1  2  2
##                                                                           
## node        24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44
## connections  4  1  2  1  1  1  1  2  1  2  1  3  1  1  2  1  1  2  1  1  1
##                                                                           
## node        45 46 47 48 49 50 51 52 53 54 55 56 57 58 59 60 61 62 63 64 65
## connections  1  3  1  1  2  1  1  1  1  1  1  1  1  1  1  1  1  1  1  2  2
##                                                                           
## node        66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86
## connections  2  1  1  1  1  1  1  1  1  1  2  1  1  1  1  1  1  1  1  1  1
##                                                       
## node        87 88 89 90 91 92 93 94 95 96 97 98 99 100
## connections  2  2  1  1  1  2  1  1  1  1  1  1  1   1
```

```r
# Average path length
average.path.length(gSF)
```

```
## [1] 2.743523
```

```r
# Transitivity
transitivity(gSF)
```

```
## [1] 0
```

```r
## Power-law?
# Get degree distribution
dist <- degree_distribution(gSF)
# Note nonzero elements for log trasnsform
ID <- dist>0
# Get an index for node groups
nodes <- seq_along(dist)

# Fit a line in log-log space
lf  <- lm(log(dist[ID])~log(nodes[ID]))
lfit <- predict(lf)

# Plot it!
plot(nodes,dist,pch=16,log = "xy")
```

```
## Warning in xy.coords(x, y, xlabel, ylabel, log): 5 y values <= 0 omitted
## from logarithmic plot
```

```r
lines(x = nodes[ID],y = exp(lfit), col="red")
```

![](Assignments_S8_files/figure-html/unnamed-chunk-8-2.png)<!-- -->

  
### Small World Index Demo {-}

* Select and run all the code below, this will display three graphs with different topology:
    + Many 'local' connections, only between neighbouring nodes.
    + A few nodes with many connections, many nodes with a few connections
    + Relatively many nodes with many connections, a few nodes with just a few connections
* The average number of neighbours of the first order (*FON*) for each graph is the same (`7`)
* The differences between the connectivity are adequately captured by the Small World Index
  
The Small World Index is calculated by comparing the average path length and transitiviy of randomly connected graphs, it is similar to a surrogsate test. If you look at the code of function `SWtestE()` you can an idea how it is done.


```r
# Initialize
set.seed(660)
layout1=layout.kamada.kawai
k=3
n=50

# Setup plots
op <- par(mfrow=c(1,3),mar= c(5,2,2,2))

# Strogatz rewiring probability = .00001
p   <- 0.00001
p1  <- plotNET_SW(n=n,k=k,p=p,doPlot = FALSE)
p11 <- plot(p1, main="p = 0.00001",
            layout=layout1,
            xlab=paste("FON = ",round(mean(neighborhood.size(p1,order=1)),digits=1), "\nSWI = ", round(SWtestE(p1,N=100)$valuesAV$SWI,digits=2)))

# Strogatz rewiring probability = .01
p   <- 0.01
p2  <- plotNET_SW(n=n,k=k,p=p,doPlot = FALSE)
p22 <- plot(p2,main="p = 0.01",layout=layout1,
            xlab=paste("FON = ",round(mean(neighborhood.size(p2,order=1)),digits=1),"\nSWI = ", round(SWtestE(p2,N=100)$valuesAV$SWI,digits=2)))
# Strogatz rewiring probability = 1
p   <- 1
p3  <- plotNET_SW(n=n,k=k,p=p,doPlot = FALSE)
p33 <- plot(p3,main="p = 1",layout=layout1,xlab=paste("FON = ",round(mean(neighborhood.size(p3,order=1)),digits=1),"\nSWI = ", round(SWtestE(p3,N=100)$valuesAV$SWI,digits=2)))
```

![](Assignments_S8_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

```r
# restore graphics parameters
par(op)
```


## Social Networks 

The package `igraph` contains data on a Social network of friendships between 34 members of a karate club at a US university in the 1970s.

> See W. W. Zachary, An information flow model for conflict and fission in small groups, *Journal of Anthropological Research 33*, 452-473 (1977).


* Get a graph of the **community structure** within the social network. 
    - There are many procedures for finding communities, or, subgraphs, in graphs, try to understand how the`walktrap` procedure finds them.
    - Check the manual pages to figure out what kind of information the functions `sizes`, `communities`, `membership` and `modularity` provide
    


```r
# Community membership
karate <- graph.famous("Zachary")
wc     <- cluster_walktrap(karate)
plot(wc, karate)
```

![](Assignments_S8_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

```r
# Measures
modularity(wc)
```

```
## [1] 0.3532216
```

```r
sizes(wc)
```

```
## Community sizes
## 1 2 3 4 5 
## 9 7 9 4 5
```

```r
communities(wc)
```

```
## $`1`
## [1]  1  2  4  8 12 13 18 20 22
## 
## $`2`
## [1]  3  9 10 14 29 31 32
## 
## $`3`
## [1] 15 16 19 21 23 27 30 33 34
## 
## $`4`
## [1] 24 25 26 28
## 
## $`5`
## [1]  5  6  7 11 17
```

```r
cbind(node = V(karate) , community = membership(wc))
```

```
##       node community
##  [1,]    1         1
##  [2,]    2         1
##  [3,]    3         2
##  [4,]    4         1
##  [5,]    5         5
##  [6,]    6         5
##  [7,]    7         5
##  [8,]    8         1
##  [9,]    9         2
## [10,]   10         2
## [11,]   11         5
## [12,]   12         1
## [13,]   13         1
## [14,]   14         2
## [15,]   15         3
## [16,]   16         3
## [17,]   17         5
## [18,]   18         1
## [19,]   19         3
## [20,]   20         1
## [21,]   21         3
## [22,]   22         1
## [23,]   23         3
## [24,]   24         4
## [25,]   25         4
## [26,]   26         4
## [27,]   27         3
## [28,]   28         4
## [29,]   29         2
## [30,]   30         3
## [31,]   31         2
## [32,]   32         2
## [33,]   33         3
## [34,]   34         3
```

```r
# Is the structure a hierarchy?
if(is_hierarchical(wc)){
  plot(as.dendrogram(wc))
}
```

![](Assignments_S8_files/figure-html/unnamed-chunk-10-2.png)<!-- -->


* It is also possible to get the adjacency matrix of a graph
    - What do the columns, rows and 0s and 1s stand for?
    - Looks a bit like a recurrence matrix, except, the rows and columns don't represent an (embedded) time series 


```r
(am <-get.adjacency(karate))
```

```
## 34 x 34 sparse Matrix of class "dgCMatrix"
##                                                                          
##  [1,] . 1 1 1 1 1 1 1 1 . 1 1 1 1 . . . 1 . 1 . 1 . . . . . . . . . 1 . .
##  [2,] 1 . 1 1 . . . 1 . . . . . 1 . . . 1 . 1 . 1 . . . . . . . . 1 . . .
##  [3,] 1 1 . 1 . . . 1 1 1 . . . 1 . . . . . . . . . . . . . 1 1 . . . 1 .
##  [4,] 1 1 1 . . . . 1 . . . . 1 1 . . . . . . . . . . . . . . . . . . . .
##  [5,] 1 . . . . . 1 . . . 1 . . . . . . . . . . . . . . . . . . . . . . .
##  [6,] 1 . . . . . 1 . . . 1 . . . . . 1 . . . . . . . . . . . . . . . . .
##  [7,] 1 . . . 1 1 . . . . . . . . . . 1 . . . . . . . . . . . . . . . . .
##  [8,] 1 1 1 1 . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
##  [9,] 1 . 1 . . . . . . . . . . . . . . . . . . . . . . . . . . . 1 . 1 1
## [10,] . . 1 . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 1
## [11,] 1 . . . 1 1 . . . . . . . . . . . . . . . . . . . . . . . . . . . .
## [12,] 1 . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
## [13,] 1 . . 1 . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
## [14,] 1 1 1 1 . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 1
## [15,] . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 1 1
## [16,] . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 1 1
## [17,] . . . . . 1 1 . . . . . . . . . . . . . . . . . . . . . . . . . . .
## [18,] 1 1 . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
## [19,] . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 1 1
## [20,] 1 1 . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 1
## [21,] . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 1 1
## [22,] 1 1 . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
## [23,] . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 1 1
## [24,] . . . . . . . . . . . . . . . . . . . . . . . . . 1 . 1 . 1 . . 1 1
## [25,] . . . . . . . . . . . . . . . . . . . . . . . . . 1 . 1 . . . 1 . .
## [26,] . . . . . . . . . . . . . . . . . . . . . . . 1 1 . . . . . . 1 . .
## [27,] . . . . . . . . . . . . . . . . . . . . . . . . . . . . . 1 . . . 1
## [28,] . . 1 . . . . . . . . . . . . . . . . . . . . 1 1 . . . . . . . . 1
## [29,] . . 1 . . . . . . . . . . . . . . . . . . . . . . . . . . . . 1 . 1
## [30,] . . . . . . . . . . . . . . . . . . . . . . . 1 . . 1 . . . . . 1 1
## [31,] . 1 . . . . . . 1 . . . . . . . . . . . . . . . . . . . . . . . 1 1
## [32,] 1 . . . . . . . . . . . . . . . . . . . . . . . 1 1 . . 1 . . . 1 1
## [33,] . . 1 . . . . . 1 . . . . . 1 1 . . 1 . 1 . 1 1 . . . . . 1 1 1 . 1
## [34,] . . . . . . . . 1 1 . . . 1 1 1 . . 1 1 1 . 1 1 . . 1 1 1 1 1 1 1 .
```

```r
rp_plot(am)
```

![](Assignments_S8_files/figure-html/unnamed-chunk-11-1.png)<!-- -->


## Recurrence Networks {.tabset .tabset-fade .tabset-pills}

Recurrence networks are graphs created from a recurrence matrix. This means the nodes of the graph represent time points and the connections between nodes represent a recurrence relation betwen the values observed at those time points. That is, often the matrix represents recurrences in a reconstructed state space, the values are coordinates and therefore we would say the edges of a recurrence network represent a temporal relation between recurring states. The ultimate reference for learning everything about recurrence networks is:

> [Zou, Y., Donner, R. V., Marwan, N., Donges, J. F., & Kurths, J. (2018). Complex network approaches to nonlinear time series analysis. Physics Reports](https://www.sciencedirect.com/science/article/abs/pii/S037015731830276X?via%3Dihub)


Package `casnet` has some functions to create recurrence networks, they are similar to the functions used for CRQA:
* `rn()` is very similar to `rp()`, it will create a matrix based on embedding parameters. One difference is the option to create a weighted matrix. This is a matrix in which non-recurring values are set to 0, but the recurring values are not replaced by a 1, the distance value is retained and acts as an edge-weight
* `rn_plot()` will produce the same as `rp_plot()`

We can turn the recurrence matrix into an adjecency matrix, an `igraph` object. This means we can use all the `igraph` functions to calculate network measures.


```r
# Reload the data we used earlier
series <- rio::import("https://github.com/FredHasselman/The-Complex-Systems-Approach-Book/raw/master/assignments/assignment_data/BasicTSA_arma/series.sav", setclass = "tbl_df")

# Lets use a shorter dataset to speed things up
series <- series[1:500,]
```

We'll analyse the three time series as a recurrence network:
* Compare the results, look at the SMall World Index and other measures
   - Remember: TS_1 was white noise, TS_2 was a sine with added noise, TS_3 was the logistic map in the chaotic regime.
* Note that some of the RQA measures can be exactly calculated from the measures of the network representation.
   - Try to understand why the Recurrence is reprented as the *degree centrality() of the network (`igraph::centr_degree()`)


### TS 1 {-}


```r
#----------------------
# Adjacency matrix TS_1
#----------------------
plot(ts(series$TS_1))

# By passing emRad = NA, a radius will be calculated
RN1 <- rn(y1 = series$TS_1, emDim = 1, emLag = 1, emRad = NA)
```

```
## 
## Auto-recurrence: Setting diagonal to (1 + max. distance) for analyses
## 
## Searching for a radius that will yield 0.05 for RR
```

```r
rn_plot(RN1)
```

![](Assignments_S8_files/figure-html/unnamed-chunk-13-1.png)<!-- -->![](Assignments_S8_files/figure-html/unnamed-chunk-13-2.png)<!-- -->

```r
# Get RQA measures
rqa1 <- crqa_rp(RN1)
knitr::kable(rqa1,digits = 2)
```



 emRad    RP_N     RR   SING_N   SING_rate   DIV_dl   REP_av   ANI   N_dl   N_dlp    DET   MEAN_dl   MAX_dl   ENT_dl   ENTrel_dl   CoV_dl   N_vl   N_vlp   LAM_vl   TT_vl   MAX_vl   ENT_vl   ENTrel_vl   CoV_vl   REP_vl   N_hlp   N_hl   LAM_hl   TT_hl   MAX_hl   ENT_hl   ENTrel_hl   CoV_hl   REP_hl
------  ------  -----  -------  ----------  -------  -------  ----  -----  ------  -----  --------  -------  -------  ----------  -------  -----  ------  -------  ------  -------  -------  ----------  -------  -------  ------  -----  -------  ------  -------  -------  ----------  -------  -------
  0.03   12476   0.05    11350        0.91     0.33     1.43     1    552    1126   0.09      2.04        3     0.17        0.03      0.1    785    1606     0.13    2.05        3     0.19        0.03      0.1     1.43    1606    785     0.13    2.05        3     0.19        0.03      0.1     1.43

```r
g1 <- graph_from_adjacency_matrix(RN1, mode="undirected", diag = FALSE)
g1 <- plotNET_prep(g1)
```

![](Assignments_S8_files/figure-html/unnamed-chunk-13-3.png)<!-- -->

```r
# Network measures
average.path.length(g1)
```

```
## [1] 11.86449
```

```r
transitivity(g1)
```

```
## [1] 0.7418716
```

```r
smallworldness(g1)
```

```
##       smallworldness         trans_target averagelength_target 
##           1.64336356           0.74187162          82.84467335 
##          trans_rnd_M         trans_rnd_lo         trans_rnd_up 
##           0.06639828           0.06395321           0.06871825 
##  averagelength_rnd_M averagelength_rnd_lo averagelength_rnd_up 
##          12.18502220          12.17588725          12.18732329
```

```r
recs1 <- centr_degree(g1)

(RP_N <- sum(recs1$res))
```

```
## [1] 12476
```

```r
rqa1$RP_N
```

```
## [1] 12476
```

```r
RP_N / recs1$theoretical_max
```

```
## [1] 0.05000401
```

```r
rqa1$RR
```

```
## [1] 0.05000401
```

### TS 2 {-}


```r
#----------------------
# Adjacency matrix TS_2
#----------------------
plot(ts(series$TS_2))

RN2 <- rn(y1 = series$TS_2, emDim = 1, emLag = 1, emRad = NA)
```

```
## 
## Auto-recurrence: Setting diagonal to (1 + max. distance) for analyses
## 
## Searching for a radius that will yield 0.05 for RR
```

```r
rn_plot(RN2)
```

![](Assignments_S8_files/figure-html/unnamed-chunk-14-1.png)<!-- -->![](Assignments_S8_files/figure-html/unnamed-chunk-14-2.png)<!-- -->

```r
# Get RQA measures
rqa2 <- crqa_rp(RN2)
knitr::kable(rqa2,digits = 2)
```



 emRad    RP_N     RR   SING_N   SING_rate   DIV_dl   REP_av   ANI   N_dl   N_dlp    DET   MEAN_dl   MAX_dl   ENT_dl   ENTrel_dl   CoV_dl   N_vl   N_vlp   LAM_vl   TT_vl   MAX_vl   ENT_vl   ENTrel_vl   CoV_vl   REP_vl   N_hlp   N_hl   LAM_hl   TT_hl   MAX_hl   ENT_hl   ENTrel_hl   CoV_hl   REP_hl
------  ------  -----  -------  ----------  -------  -------  ----  -----  ------  -----  --------  -------  -------  ----------  -------  -----  ------  -------  ------  -------  -------  ----------  -------  -------  ------  -----  -------  ------  -------  -------  ----------  -------  -------
  0.03   12476   0.05    10288        0.82     0.25     1.36     1   1042    2188   0.18       2.1        4     0.33        0.05     0.15   1347    2985     0.24    2.22        4     0.56        0.09     0.21     1.36    2985   1347     0.24    2.22        4     0.56        0.09     0.21     1.36

```r
g2 <- graph_from_adjacency_matrix(RN2, mode="undirected", diag = FALSE)
g2 <- plotNET_prep(g2)
```

![](Assignments_S8_files/figure-html/unnamed-chunk-14-3.png)<!-- -->

```r
# Network measures
average.path.length(g2)
```

```
## [1] 13.8839
```

```r
transitivity(g2)
```

```
## [1] 0.7502262
```

```r
smallworldness(g2)
```

```
##       smallworldness         trans_target averagelength_target 
##           2.20345610           0.75022617          13.88390381 
##          trans_rnd_M         trans_rnd_lo         trans_rnd_up 
##           0.05479241           0.05252589           0.05717855 
##  averagelength_rnd_M averagelength_rnd_lo averagelength_rnd_up 
##           2.23431406           2.23123836           2.23762725
```

```r
recs2 <- centr_degree(g2)

(RP_N <- sum(recs2$res))
```

```
## [1] 12476
```

```r
rqa2$RP_N
```

```
## [1] 12476
```

```r
RP_N / recs2$theoretical_max
```

```
## [1] 0.05000401
```

```r
rqa2$RR
```

```
## [1] 0.05000401
```

### TS 3 {-}


```r
#----------------------
# Adjacency matrix TS_3
#----------------------
plot(ts(series$TS_3))

RN3 <- rn(y1 = series$TS_3, emDim = 1, emLag = 1, emRad = NA)
```

```
## 
## Auto-recurrence: Setting diagonal to (1 + max. distance) for analyses
## 
## Searching for a radius that will yield 0.05 for RR
```

```r
rn_plot(RN3)
```

![](Assignments_S8_files/figure-html/unnamed-chunk-15-1.png)<!-- -->![](Assignments_S8_files/figure-html/unnamed-chunk-15-2.png)<!-- -->

```r
# Get RQA measures
rqa3 <- crqa_rp(RN3)
knitr::kable(rqa3,digits = 2)
```



 emRad    RP_N     RR   SING_N   SING_rate   DIV_dl   REP_av   ANI   N_dl   N_dlp    DET   MEAN_dl   MAX_dl   ENT_dl   ENTrel_dl   CoV_dl   N_vl   N_vlp   LAM_vl   TT_vl   MAX_vl   ENT_vl   ENTrel_vl   CoV_vl   REP_vl   N_hlp   N_hl   LAM_hl   TT_hl   MAX_hl   ENT_hl   ENTrel_hl   CoV_hl   REP_hl
------  ------  -----  -------  ----------  -------  -------  ----  -----  ------  -----  --------  -------  -------  ----------  -------  -----  ------  -------  ------  -------  -------  ----------  -------  -------  ------  -----  -------  ------  -------  -------  ----------  -------  -------
  0.02   12476   0.05     4468        0.36     0.06     0.17     1   2684    8008   0.64      2.98       17     1.37        0.22     0.48    580    1372     0.11    2.37        5     0.72        0.12     0.34     0.17    1372    580     0.11    2.37        5     0.72        0.12     0.34     0.17

```r
g3 <- graph_from_adjacency_matrix(RN3, mode="undirected", diag = FALSE)
g3 <- plotNET_prep(g3)
```

![](Assignments_S8_files/figure-html/unnamed-chunk-15-3.png)<!-- -->

```r
average.path.length(g3)
```

```
## [1] 10.41051
```

```r
transitivity(g3)
```

```
## [1] 0.8373556
```

```r
smallworldness(g3)
```

```
##       smallworldness         trans_target averagelength_target 
##           0.08646152           0.83735561         248.09786774 
##          trans_rnd_M         trans_rnd_lo         trans_rnd_up 
##           0.08798658           0.08493937           0.09128964 
##  averagelength_rnd_M averagelength_rnd_lo averagelength_rnd_up 
##           2.25399207           2.24682914           2.26112309
```

```r
recs3 <- centr_degree(g3)

(RP_N <- sum(recs3$res))
```

```
## [1] 12476
```

```r
rqa3$RP_N
```

```
## [1] 12476
```

```r
RP_N / recs3$theoretical_max
```

```
## [1] 0.05000401
```

```r
rqa3$RR
```

```
## [1] 0.05000401
```





## Symptom Networks: Package `qgraph`

Learn about the functionality of the `qgraph` frm its author Sacha Epskamp.

Try running the examples, e.g. of the `Big 5`: 

[http://sachaepskamp.com/qgraph/examples](http://sachaepskamp.com/qgraph/examples)


### `qgraph` tutorials / blog

Great site by Eiko Fried:
[http://psych-networks.com ](http://psych-networks.com )




<!-- ## Early Warnings -->

<!-- Have a look at the site [Early Warning Systems](http://www.early-warning-signals.org/home/) -->

<!-- There is an accompanying [`R` package `earlywarnings`](https://cran.r-project.org/web/packages/earlywarnings/index.html) -->


