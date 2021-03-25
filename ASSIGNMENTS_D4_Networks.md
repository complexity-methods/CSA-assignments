---
title: "DCS Assignments Day 4"
author: "Fred Hasselman"
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
    pandoc_args: ["--number-offset","3"]
    
---






# **Complex Networks**

To complete these assignments you need these packages:

```r
library(igraph)
library(qgraph)
library(casnet) 
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
## IGRAPH 35bfcbe U--- 20 20 -- Ring graph
## + attr: name (g/c), mutual (g/l), circular (g/l)
## + edges from 35bfcbe:
##  [1]  1-- 2  2-- 3  3-- 4  4-- 5  5-- 6  6-- 7  7-- 8  8-- 9  9--10 10--11
## [11] 11--12 12--13 13--14 14--15 15--16 16--17 17--18 18--19 19--20  1--20
```

```r
# To plot, simply call:
plot(gR)
```

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

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
## + 20/20 vertices, from 35bfcbe:
##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20
```

```r
# List edges
E(gR)
```

```
## + 20/20 edges from 35bfcbe:
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

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-5-1.png)<!-- -->


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

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

```r
# Degree
cbind(node = V(gSW), connections = degree(gSW))
```

```
##       node connections
##  [1,]    1          10
##  [2,]    2          10
##  [3,]    3          10
##  [4,]    4          10
##  [5,]    5           9
##  [6,]    6          10
##  [7,]    7          12
##  [8,]    8          10
##  [9,]    9          10
## [10,]   10          11
## [11,]   11          11
## [12,]   12          12
## [13,]   13          10
## [14,]   14           9
## [15,]   15          10
## [16,]   16          10
## [17,]   17          10
## [18,]   18           7
## [19,]   19          10
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
## [1] 0.6026345
```

```r
# Complute the Small World Index, aka 'smallworldness'
qgraph::smallworldness(gSW)
```

```
##       smallworldness         trans_target averagelength_target 
##            1.2435897            0.6026345            1.4736842 
##          trans_rnd_M         trans_rnd_lo         trans_rnd_up 
##            0.4846169            0.4445664            0.5203074 
##  averagelength_rnd_M averagelength_rnd_lo averagelength_rnd_up 
##            1.4737579            1.4736842            1.4789474
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

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

```r
# Degree
rbind(node = V(gSF), connections = degree(gSF))
```

```
##                                                                          
## node         1 2 3 4  5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23
## connections 12 4 3 1 10 5 7 1 4  5  1  5  1  3  2  3  6  5  1  1  1  2  6
##                                                                           
## node        24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44
## connections  2  2  1  4  2  1  2  2  3  3  2  1  1  1  1  2  3  2  1  2  1
##                                                                           
## node        45 46 47 48 49 50 51 52 53 54 55 56 57 58 59 60 61 62 63 64 65
## connections  1  1  2  2  3  1  1  4  1  1  1  1  1  1  1  1  2  1  1  2  1
##                                                                           
## node        66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86
## connections  1  1  1  2  1  1  1  1  3  1  1  1  2  1  1  1  1  1  1  1  1
##                                                       
## node        87 88 89 90 91 92 93 94 95 96 97 98 99 100
## connections  2  1  1  1  1  1  1  1  1  1  1  1  1   1
```

```r
# Average path length
average.path.length(gSF)
```

```
## [1] 2.374214
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
## Warning in xy.coords(x, y, xlabel, ylabel, log): 4 y values <= 0 omitted
## from logarithmic plot
```

```r
lines(x = nodes[ID],y = exp(lfit), col="red")
```

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-8-2.png)<!-- -->



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

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

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

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-9-2.png)<!-- -->


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

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-10-1.png)<!-- -->


## Recurrence Networks {.tabset .tabset-fade .tabset-pills}

Recurrence networks are graphs created from a recurrence matrix. This means the nodes of the graph represent time points and the connections between nodes represent a recurrence relation betwen the values observed at those time points. That is, often the matrix represents recurrences in a reconstructed state space, the values are coordinates and therefore we would say the edges of a recurrence network represent a temporal relation between recurring states. The ultimate reference for learning everything about recurrence networks is:

> [Zou, Y., Donner, R. V., Marwan, N., Donges, J. F., & Kurths, J. (2018). Complex network approaches to nonlinear time series analysis. Physics Reports](https://www.sciencedirect.com/science/article/abs/pii/S037015731830276X?via%3Dihub)


Package `casnet` has some functions to create recurrence networks, they are similar to the functions used for CRQA:
* `rn()` is very similar to `rp()`, it will create a matrix based on embedding parameters. One difference is the option to create a weighted matrix. This is a matrix in which non-recurring values are set to 0, but the recurring values are not replaced by a 1, the distance value is retained and acts as an edge-weight
* `rn_plot()` will produce the same as `rp_plot()`

We can turn the recurrence matrix into an adjecency matrix, an `igraph` object. This means we can use all the `igraph` functions to calculate network measures.


```r
# Reload the data we used earlier
series <- rio::import("https://github.com/complexity-methods/CSA-assignments/raw/master/assignment_data/BasicTSA_arma/series.xlsx")

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
```

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

```r
arcs=6

crqa_parameters(series$TS_1)
```

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-12-2.png)<!-- -->

```r
# By passing emRad = NA, a radius will be calculated
RN1 <- casnet::rn(y1 = series$TS_1, emDim = 7, emLag = 2, emRad = NA, targetValue = .01)
```

```
## 
## Auto-recurrence: Setting diagonal to (1 + max. distance) for analyses
## 
## Searching for a radius that will yield 0.01 for RR
```

```r
casnet::rn_plot(RN1)
```

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-12-3.png)<!-- -->

```r
# Get RQA measures
rqa1 <- crqa_rp(RN1)
knitr::kable(rqa1,digits = 2)
```



 emRad   RP_N     RR   SING_N   SING_rate   DIV_dl   REP_av   ANI   N_dl   N_dlp    DET   MEAN_dl   MAX_dl   ENT_dl   ENTrel_dl   CoV_dl   N_vl   N_vlp   LAM_vl   TT_vl   MAX_vl   ENT_vl   ENTrel_vl   CoV_vl   REP_vl   N_hlp   N_hl   LAM_hl   TT_hl   MAX_hl   ENT_hl   ENTrel_hl   CoV_hl   REP_hl
------  -----  -----  -------  ----------  -------  -------  ----  -----  ------  -----  --------  -------  -------  ----------  -------  -----  ------  -------  ------  -------  -------  ----------  -------  -------  ------  -----  -------  ------  -------  -------  ----------  -------  -------
  0.55   2368   0.01     2348        0.99     0.33     4.65     1      8      20   0.01       2.5        3     0.69        0.11     0.21     45      93     0.04    2.07        3     0.24        0.04     0.12     4.65      93     45     0.04    2.07        3     0.24        0.04     0.12     4.65

```r
g1  <- igraph::graph_from_adjacency_matrix(RN1, mode="undirected", diag = FALSE)
V(g1)$size <- degree(g1)
g1r <- casnet::make_spiral_graph(g1,arcs = arcs,type = "Euler" ,epochColours = getColours(arcs))
```

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-12-4.png)<!-- -->

```r
# Network measures
igraph::average.path.length(g1)
```

```
## [1] 4.242546
```

```r
igraph::transitivity(g1)
```

```
## [1] 0.3158716
```

```r
qgraph::smallworldness(g1)
```

```
##       smallworldness         trans_target averagelength_target 
##           6.59400692           0.31587164         215.96216865 
##          trans_rnd_M         trans_rnd_lo         trans_rnd_up 
##           0.04091995           0.03304293           0.04917736 
##  averagelength_rnd_M averagelength_rnd_lo averagelength_rnd_up 
##         184.48094592         181.75063490         194.23956359
```

```r
recs1 <- igraph::centr_degree(g1)

(RP_N <- sum(recs1$res))
```

```
## [1] 2368
```

```r
rqa1$RP_N
```

```
## [1] 2368
```

```r
RP_N / recs1$theoretical_max
```

```
## [1] 0.01000499
```

```r
rqa1$RR
```

```
## [1] 0.01000499
```

### TS 2 {-}


```r
#----------------------
# Adjacency matrix TS_2
#----------------------
plot(ts(series$TS_2))
```

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

```r
crqa_parameters(series$TS_2)
```

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-13-2.png)<!-- -->

```r
RN2 <- rn(y1 = series$TS_2, emDim = 7, emLag = 2, emRad = NA,  targetValue = 0.01)
```

```
## 
## Auto-recurrence: Setting diagonal to (1 + max. distance) for analyses
## 
## Searching for a radius that will yield 0.01 for RR
```

```r
rn_plot(RN2)
```

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-13-3.png)<!-- -->

```r
# Get RQA measures
rqa2 <- crqa_rp(RN2)
knitr::kable(rqa2,digits = 2)
```



 emRad   RP_N     RR   SING_N   SING_rate   DIV_dl   REP_av   ANI   N_dl   N_dlp    DET   MEAN_dl   MAX_dl   ENT_dl   ENTrel_dl   CoV_dl   N_vl   N_vlp   LAM_vl   TT_vl   MAX_vl   ENT_vl   ENTrel_vl   CoV_vl   REP_vl   N_hlp   N_hl   LAM_hl   TT_hl   MAX_hl   ENT_hl   ENTrel_hl   CoV_hl   REP_hl
------  -----  -----  -------  ----------  -------  -------  ----  -----  ------  -----  --------  -------  -------  ----------  -------  -----  ------  -------  ------  -------  -------  ----------  -------  -------  ------  -----  -------  ------  -------  -------  ----------  -------  -------
  0.32   2368   0.01     2270        0.96     0.14     1.99     1     28      98   0.04       3.5        7     1.23         0.2     0.57     93     195     0.08     2.1        4     0.33        0.05     0.16     1.99     195     93     0.08     2.1        4     0.33        0.05     0.16     1.99

```r
g2 <- graph_from_adjacency_matrix(RN2, mode="undirected", diag = FALSE)
V(g2)$size <- degree(g2)
g2r <- casnet::make_spiral_graph(g2,arcs = arcs,type = "Euler" ,epochColours = getColours(arcs))
```

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-13-4.png)<!-- -->

```r
# Network measures
average.path.length(g2)
```

```
## [1] 9.696495
```

```r
transitivity(g2)
```

```
## [1] 0.3422773
```

```r
smallworldness(g2)
```

```
##       smallworldness         trans_target averagelength_target 
##          10.96191871           0.34227726          77.69775479 
##          trans_rnd_M         trans_rnd_lo         trans_rnd_up 
##           0.02344651           0.01685415           0.03061576 
##  averagelength_rnd_M averagelength_rnd_lo averagelength_rnd_up 
##          58.34386230          55.86626786          68.86170647
```

```r
recs2 <- centr_degree(g2)

(RP_N <- sum(recs2$res))
```

```
## [1] 2368
```

```r
rqa2$RP_N
```

```
## [1] 2368
```

```r
RP_N / recs2$theoretical_max
```

```
## [1] 0.01000499
```

```r
rqa2$RR
```

```
## [1] 0.01000499
```

### TS 3 {-}


```r
#----------------------
# Adjacency matrix TS_3
#----------------------
plot(ts(series$TS_3))
```

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-14-1.png)<!-- -->

```r
crqa_parameters(series$TS_3)
```

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-14-2.png)<!-- -->

```r
RN3 <- rn(y1 = series$TS_3, emDim = 7, emLag = 8, emRad = NA, targetValue = 0.01)
```

```
## 
## Auto-recurrence: Setting diagonal to (1 + max. distance) for analyses
## 
## Searching for a radius that will yield 0.01 for RR
```

```r
rn_plot(RN3)
```

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-14-3.png)<!-- -->

```r
# Get RQA measures
rqa3 <- crqa_rp(RN3)
knitr::kable(rqa3,digits = 2)
```



 emRad   RP_N     RR   SING_N   SING_rate   DIV_dl   REP_av   ANI   N_dl   N_dlp    DET   MEAN_dl   MAX_dl   ENT_dl   ENTrel_dl   CoV_dl   N_vl   N_vlp   LAM_vl   TT_vl   MAX_vl   ENT_vl   ENTrel_vl   CoV_vl   REP_vl   N_hlp   N_hl   LAM_hl   TT_hl   MAX_hl   ENT_hl   ENTrel_hl   CoV_hl   REP_hl
------  -----  -----  -------  ----------  -------  -------  ----  -----  ------  -----  --------  -------  -------  ----------  -------  -----  ------  -------  ------  -------  -------  ----------  -------  -------  ------  -----  -------  ------  -------  -------  ----------  -------  -------
  0.57   2030   0.01     1852        0.91     0.25     0.09     1     84     178   0.09      2.12        4     0.37        0.06     0.19      8      16     0.01       2        2        0           0        0     0.09      16      8     0.01       2        2        0           0        0     0.09

```r
g3 <- graph_from_adjacency_matrix(RN3, mode="undirected", diag = FALSE)
V(g3)$size <- degree(g3)
g3r <- casnet::make_spiral_graph(g3,arcs = arcs,type = "Euler" ,epochColours = getColours(arcs))
```

![](ASSIGNMENTS_D4_Networks_files/figure-html/unnamed-chunk-14-4.png)<!-- -->

```r
average.path.length(g3)
```

```
## [1] 6.094221
```

```r
transitivity(g3)
```

```
## [1] 0.3300908
```

```r
smallworldness(g3)
```

```
##       smallworldness         trans_target averagelength_target 
##         29.023059466          0.330090791         13.959812762 
##          trans_rnd_M         trans_rnd_lo         trans_rnd_up 
##          0.010338521          0.005188067          0.017509728 
##  averagelength_rnd_M averagelength_rnd_lo averagelength_rnd_up 
##         12.689596344         12.063925795         19.894706430
```

```r
recs3 <- centr_degree(g3)

(RP_N <- sum(recs3$res))
```

```
## [1] 2030
```

```r
rqa3$RP_N
```

```
## [1] 2030
```

```r
RP_N / recs3$theoretical_max
```

```
## [1] 0.01000246
```

```r
rqa3$RR
```

```
## [1] 0.01000246
```


## Multiplex Recurrence Networks {.tabset .tabset-fade .tabset-pills}


Consider the three time series to be part of a multi-layer recurrence network.
Common properties of the multiplex network are multi-layer mutual information and edge overlap.


```r
# Inter-layer Mutual Information
(interlayer_mi12 <- mif_interlayer(g1,g2))
```

```
## [1] 2.20549
## attr(,"miType")
## [1] "inter-layer mutual information"
```

```r
(interlayer_mi13 <- mif_interlayer(g1,g3))
```

```
## [1] 1.666615
## attr(,"miType")
## [1] "inter-layer mutual information"
```

```r
(interlayer_mi23 <- mif_interlayer(g2,g3))
```

```
## [1] 2.155163
## attr(,"miType")
## [1] "inter-layer mutual information"
```

```r
# mean I-L MI  
mean(c(interlayer_mi12,interlayer_mi13,interlayer_mi23))
```

```
## [1] 2.009089
```

```r
# Edge Overlap
(edge_overlap12 <- length(E(g1 %s% g2)) / (length(E(g1))+length(E(g2))))
```

```
## [1] 0.003800676
```

```r
(edge_overlap13 <- length(E(g1 %s% g3)) / (length(E(g1))+length(E(g3))))
```

```
## [1] 0.005457026
```

```r
(edge_overlap23 <- length(E(g2 %s% g3)) / (length(E(g2))+length(E(g3))))
```

```
## [1] 0.004092769
```

```r
# mean EO 
mean(c(edge_overlap12,interlayer_mi13,interlayer_mi23))
```

```
## [1] 1.275193
```

```r
# Overall Edge Overlap
(eo_all  <- length(E(intersection(g1,g2,g3))))
```

```
## [1] 0
```

```r
(eo_mean <- eo_all / (edge_overlap12+edge_overlap13+edge_overlap23))
```

```
## [1] 0
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


