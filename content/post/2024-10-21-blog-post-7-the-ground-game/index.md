---
title: Blog Post 7 -- The Ground Game
author: Chris S
date: '2024-10-21'
slug: blog-post-7-the-ground-game
categories: []
tags: []
---


``` r
library(geofacet)
library(ggpubr)
```

```
## Loading required package: ggplot2
```

``` r
library(ggthemes)
library(haven)
library(kableExtra)
library(maps)
library(mgcv)
```

```
## Loading required package: nlme
```

```
## This is mgcv 1.9-1. For overview type 'help("mgcv-package")'.
```

``` r
library(mgcViz)
```

```
## Loading required package: qgam
```

```
## Registered S3 method overwritten by 'GGally':
##   method from   
##   +.gg   ggplot2
```

```
## Registered S3 method overwritten by 'mgcViz':
##   method from  
##   +.gg   GGally
```

```
## 
## Attaching package: 'mgcViz'
```

```
## The following objects are masked from 'package:stats':
## 
##     qqline, qqnorm, qqplot
```

``` r
library(RColorBrewer)
library(scales)
library(sf)
```

```
## Linking to GEOS 3.10.2, GDAL 3.4.2, PROJ 8.2.1; sf_use_s2() is TRUE
```

``` r
library(spData)
```

```
## To access larger datasets in this package, install the spDataLarge
## package with: `install.packages('spDataLarge',
## repos='https://nowosad.github.io/drat/', type='source')`
```

``` r
library(stargazer)
```

```
## 
## Please cite as:
```

```
##  Hlavac, Marek (2022). stargazer: Well-Formatted Regression and Summary Statistics Tables.
```

```
##  R package version 5.2.3. https://CRAN.R-project.org/package=stargazer
```

``` r
library(tidygeocoder)
library(tidyverse)
```

```
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.4     ✔ readr     2.1.5
## ✔ forcats   1.0.0     ✔ stringr   1.5.1
## ✔ lubridate 1.9.3     ✔ tibble    3.2.1
## ✔ purrr     1.0.2     ✔ tidyr     1.3.1
```

```
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ readr::col_factor() masks scales::col_factor()
## ✖ dplyr::collapse()   masks nlme::collapse()
## ✖ purrr::discard()    masks scales::discard()
## ✖ dplyr::filter()     masks stats::filter()
## ✖ dplyr::group_rows() masks kableExtra::group_rows()
## ✖ dplyr::lag()        masks stats::lag()
## ✖ purrr::map()        masks maps::map()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

``` r
library(tigris)
```

```
## To enable caching of data, set `options(tigris_use_cache = TRUE)`
## in your R script or .Rprofile.
```

``` r
library(tmap)
```

```
## Breaking News: tmap 3.x is retiring. Please test v4, e.g. with
## remotes::install_github('r-tmap/tmap')
```

``` r
library(tmaptools)
library(viridis)
```

```
## Loading required package: viridisLite
## 
## Attaching package: 'viridis'
## 
## The following object is masked from 'package:scales':
## 
##     viridis_pal
## 
## The following object is masked from 'package:maps':
## 
##     unemp
```








*This blog is an ongoing assignment for Gov 1347: Election Analytics, a course at Harvard College taught by Professor [Ryan Enos](https://www.ryandenos.com/). It will be updated weekly and culminate in a predictive model of the 2024 presidential election.*

## Introduction -- What role does the *VEP* play in state-level election results? How effective is the *ground game* at swinging election outcomes? Ultimately, can *campaign events* help predict these results?

*Knock knock.* You open your front door to find a campaign volunteer, clipboard in hand, eager to talk to you about the upcoming election. We've all seen these people randomly roaming our neighborhoods. In reality, this is just one piece of the larger ground game strategy, where candidates and their teams hit the pavement, host rallies, and knock on doors to sway voters face-to-face. But with digital ads and viral tweets dominating the outreach scene, how effective is this old-school approach?

Some argue that this holds the secret to electoral success, while others say that rallies and door-knocking are more symbolic than substantial. So, can these campaign events and personal interactions really move the needle and predict state-level results? Or, is this all just political theater?

As always, let's start with the data!

## Back To Polls?

To begin our analysis, let's take a step back and first examine state-specific forecasts for Democratic and Republican vote shares based on polling data:

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-3-1.png" width="672" />

Here, we can see that for each state, the blue line represents the Democratic vote share prediction, while the red line represents the Republican vote share. Immediately, we notice that for most states, as poll support increases, the predicted vote share for both parties also increase. This makes sense. 

However, there are also some issues with the plot. For example, some states have odd patterns, such as negative slopes or very high variance, suggesting that the linear models may not fit well in certain states. For example, in Nevada (NV), we see high variance in predictions, while in Mississippi (MS), there might be a negative slope, which is not realistic for real-world elections. Historically, polling errors are more pronounced in states with smaller or harder-to-reach populations, such as Nevada. There are other accuracy concerns, including factors like partison lean and polling methodology.

Thus, we're seeing instances when polling data might be unreliable or where the relationship between polling support and actual vote share is rather weak.

Let's take a closer look at two key states:

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-1.png" width="672" />

For California, we can see that the Democrats have a higher predicted vote share across almost all levels of polling support compared to Republicans. Historically democratic years like 2020, 2016, and 2008 align closely with the predicted vote share for Democrats at mid-to-high polling support. On the other hand, years like 1980 and 1984 show Republicans doing better, but this makes sense as California was less reliably blue at the time.

In contrast, Florida reveals how the lines are closer together, indicating a more competitive electoral environment between the two parties. At the same time, the confidence intervals in Florida are wider, reflecting more uncertainty in the model's predictions. This is likely due to Florida’s more volatile voting patterns and demographic shifts, making it harder to predict election outcomes based solely on polling.

## Voting Eligible Population -- Pennsylvania

Let's take a deeper dive into the voting eligible population (VEP). These people represent the portion of the population that is legally eligible to vote. Knowing the VEP helps in predicting election outcomes, especially in close races. A growing or shrinking VEP can significantly alter electoral dynamics, especially in battleground states.

Let's examine a specific battleground case study:

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-1.png" width="672" />

Here, we see that Pennsylvania is showing a steady, nearly linear increase in the VEP over the last few decades, reflecting population growth and demographic shifts. The red regression line hugs the points closely, predicting future VEP based on historical trends. Thus, we can ultimately assess how much influence a growing population could have on turnout rates and on election results.

Now, let's expand the OLS model to include generalized additive modeling (GAM) and use a weighted average to create a final prediciton:

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-6-1.png" width="672" />

```
## [1] 10044706
```

Here, we're adding a 75% weight to the GAM prediction, and 25% weight to the OLS prediction. We can immediately see how GAM provides much more flexibility by allowing the model to fit a curved relationship between VEP and time, capturing complex demographic changes that might influence VEP growth in the future. As a result, our final VEP prediction for 2024 is approximately, 10,044,706.

Crossing 10 million eligible voters by the next election has significant implications for Pennsylvania's electoral dynamics, as a larger electorate could change the composition of young voters, influencing overall turnout and election outcomes.

## Field Offices and The Ground Game

Field offices play a crucial role in elections in terms of voter outreach and mobilization. These offices act as hubs for campaigns to connect directly with voters, organize events, and more. This allows for more specialized campaign strategies with tailored messaging and more rapid response to local issues.

Let's trace the data across 2012 and 2016:


```
## 
## \begin{table}[!htbp] \centering 
##   \caption{Placement of Field Offices (2012)} 
##   \label{} 
## \scriptsize 
## \begin{tabular}{@{\extracolsep{3pt}}lcc} 
## \\[-1.8ex]\hline 
## \hline \\[-1.8ex] 
##  & \multicolumn{2}{c}{\textit{Dependent variable:}} \\ 
## \cline{2-3} 
## \\[-1.8ex] & obama12fo & romney12fo \\ 
## \\[-1.8ex] & (1) & (2)\\ 
## \hline \\[-1.8ex] 
##  romney12fo & 2.546$^{***}$ (0.114) &  \\ 
##   obama12fo &  & 0.374$^{***}$ (0.020) \\ 
##   swing & 0.001 (0.055) & $-$0.012 (0.011) \\ 
##   core\_rep & 0.007 (0.061) &  \\ 
##   core\_dem &  & 0.004 (0.027) \\ 
##   battle & 0.541$^{***}$ (0.096) & 0.014 (0.042) \\ 
##   medage08 &  &  \\ 
##   romney12fo:swing & $-$0.765$^{***}$ (0.116) &  \\ 
##   romney12fo:core\_rep & $-$1.875$^{***}$ (0.131) &  \\ 
##   obama12fo:swing &  & $-$0.081$^{***}$ (0.020) \\ 
##   obama12fo:core\_dem &  & $-$0.164$^{***}$ (0.023) \\ 
##   Constant & $-$0.340$^{*}$ (0.196) & 0.001 (0.079) \\ 
##  \hline \\[-1.8ex] 
## \hline 
## \hline \\[-1.8ex] 
## \end{tabular} 
## \end{table}
```

```
## 
## \begin{table}[!htbp] \centering 
##   \caption{Effect of DEM Field Offices on Turnout and DEM Vote Share (2004-2012)} 
##   \label{} 
## \scriptsize 
## \begin{tabular}{@{\extracolsep{3pt}}lcc} 
## \\[-1.8ex]\hline 
## \hline \\[-1.8ex] 
##  & \multicolumn{2}{c}{\textit{Dependent variable:}} \\ 
## \cline{2-3} 
## \\[-1.8ex] & turnout\_change & dempct\_change \\ 
## \\[-1.8ex] & (1) & (2)\\ 
## \hline \\[-1.8ex] 
##  dummy\_fo\_change & 0.004$^{***}$ (0.001) & 0.009$^{***}$ (0.002) \\ 
##   battle & 0.024$^{***}$ (0.002) & 0.043$^{***}$ (0.003) \\ 
##   as.factor(state)Arizona &  &  \\ 
##   dummy\_fo\_change:battle & $-$0.002 (0.002) & 0.007$^{**}$ (0.003) \\ 
##   Constant & 0.029$^{***}$ (0.002) & 0.022$^{***}$ (0.003) \\ 
##  \hline \\[-1.8ex] 
## Observations & 6,224 & 6,224 \\ 
## Adjusted R$^{2}$ & 0.419 & 0.469 \\ 
## \hline 
## \hline \\[-1.8ex] 
## \textit{Note:}  & \multicolumn{2}{r}{$^{*}$p$<$0.1; $^{**}$p$<$0.05; $^{***}$p$<$0.01} \\ 
## \end{tabular} 
## \end{table}
```

```
## 
  |                                                                            
  |                                                                      |   0%
  |                                                                            
  |                                                                      |   1%
  |                                                                            
  |=                                                                     |   1%
  |                                                                            
  |=                                                                     |   2%
  |                                                                            
  |==                                                                    |   2%
  |                                                                            
  |==                                                                    |   3%
  |                                                                            
  |==                                                                    |   4%
  |                                                                            
  |===                                                                   |   4%
  |                                                                            
  |===                                                                   |   5%
  |                                                                            
  |====                                                                  |   5%
  |                                                                            
  |====                                                                  |   6%
  |                                                                            
  |=====                                                                 |   6%
  |                                                                            
  |=====                                                                 |   7%
  |                                                                            
  |=====                                                                 |   8%
  |                                                                            
  |======                                                                |   8%
  |                                                                            
  |======                                                                |   9%
  |                                                                            
  |=======                                                               |   9%
  |                                                                            
  |=======                                                               |  10%
  |                                                                            
  |=======                                                               |  11%
  |                                                                            
  |========                                                              |  11%
  |                                                                            
  |========                                                              |  12%
  |                                                                            
  |=========                                                             |  12%
  |                                                                            
  |=========                                                             |  13%
  |                                                                            
  |==========                                                            |  14%
  |                                                                            
  |==========                                                            |  15%
  |                                                                            
  |===========                                                           |  15%
  |                                                                            
  |===========                                                           |  16%
  |                                                                            
  |============                                                          |  17%
  |                                                                            
  |============                                                          |  18%
  |                                                                            
  |=============                                                         |  18%
  |                                                                            
  |=============                                                         |  19%
  |                                                                            
  |==============                                                        |  19%
  |                                                                            
  |==============                                                        |  20%
  |                                                                            
  |==============                                                        |  21%
  |                                                                            
  |===============                                                       |  21%
  |                                                                            
  |===============                                                       |  22%
  |                                                                            
  |================                                                      |  22%
  |                                                                            
  |================                                                      |  23%
  |                                                                            
  |================                                                      |  24%
  |                                                                            
  |=================                                                     |  24%
  |                                                                            
  |=================                                                     |  25%
  |                                                                            
  |==================                                                    |  25%
  |                                                                            
  |==================                                                    |  26%
  |                                                                            
  |===================                                                   |  26%
  |                                                                            
  |===================                                                   |  27%
  |                                                                            
  |===================                                                   |  28%
  |                                                                            
  |====================                                                  |  28%
  |                                                                            
  |====================                                                  |  29%
  |                                                                            
  |=====================                                                 |  29%
  |                                                                            
  |=====================                                                 |  30%
  |                                                                            
  |=====================                                                 |  31%
  |                                                                            
  |======================                                                |  31%
  |                                                                            
  |======================                                                |  32%
  |                                                                            
  |=======================                                               |  32%
  |                                                                            
  |=======================                                               |  33%
  |                                                                            
  |=======================                                               |  34%
  |                                                                            
  |========================                                              |  34%
  |                                                                            
  |========================                                              |  35%
  |                                                                            
  |=========================                                             |  35%
  |                                                                            
  |=========================                                             |  36%
  |                                                                            
  |==========================                                            |  36%
  |                                                                            
  |==========================                                            |  37%
  |                                                                            
  |==========================                                            |  38%
  |                                                                            
  |===========================                                           |  38%
  |                                                                            
  |===========================                                           |  39%
  |                                                                            
  |============================                                          |  39%
  |                                                                            
  |============================                                          |  40%
  |                                                                            
  |============================                                          |  41%
  |                                                                            
  |=============================                                         |  41%
  |                                                                            
  |=============================                                         |  42%
  |                                                                            
  |==============================                                        |  42%
  |                                                                            
  |==============================                                        |  43%
  |                                                                            
  |===============================                                       |  44%
  |                                                                            
  |===============================                                       |  45%
  |                                                                            
  |================================                                      |  45%
  |                                                                            
  |================================                                      |  46%
  |                                                                            
  |=================================                                     |  47%
  |                                                                            
  |=================================                                     |  48%
  |                                                                            
  |==================================                                    |  48%
  |                                                                            
  |==================================                                    |  49%
  |                                                                            
  |===================================                                   |  49%
  |                                                                            
  |===================================                                   |  50%
  |                                                                            
  |===================================                                   |  51%
  |                                                                            
  |====================================                                  |  51%
  |                                                                            
  |====================================                                  |  52%
  |                                                                            
  |=====================================                                 |  52%
  |                                                                            
  |=====================================                                 |  53%
  |                                                                            
  |=====================================                                 |  54%
  |                                                                            
  |======================================                                |  54%
  |                                                                            
  |======================================                                |  55%
  |                                                                            
  |=======================================                               |  55%
  |                                                                            
  |=======================================                               |  56%
  |                                                                            
  |========================================                              |  56%
  |                                                                            
  |========================================                              |  57%
  |                                                                            
  |========================================                              |  58%
  |                                                                            
  |=========================================                             |  58%
  |                                                                            
  |=========================================                             |  59%
  |                                                                            
  |==========================================                            |  59%
  |                                                                            
  |==========================================                            |  60%
  |                                                                            
  |==========================================                            |  61%
  |                                                                            
  |===========================================                           |  61%
  |                                                                            
  |===========================================                           |  62%
  |                                                                            
  |============================================                          |  62%
  |                                                                            
  |============================================                          |  63%
  |                                                                            
  |============================================                          |  64%
  |                                                                            
  |=============================================                         |  64%
  |                                                                            
  |=============================================                         |  65%
  |                                                                            
  |==============================================                        |  65%
  |                                                                            
  |==============================================                        |  66%
  |                                                                            
  |===============================================                       |  66%
  |                                                                            
  |===============================================                       |  67%
  |                                                                            
  |===============================================                       |  68%
  |                                                                            
  |================================================                      |  68%
  |                                                                            
  |================================================                      |  69%
  |                                                                            
  |=================================================                     |  69%
  |                                                                            
  |=================================================                     |  70%
  |                                                                            
  |=================================================                     |  71%
  |                                                                            
  |==================================================                    |  71%
  |                                                                            
  |==================================================                    |  72%
  |                                                                            
  |===================================================                   |  72%
  |                                                                            
  |===================================================                   |  73%
  |                                                                            
  |====================================================                  |  74%
  |                                                                            
  |====================================================                  |  75%
  |                                                                            
  |=====================================================                 |  75%
  |                                                                            
  |=====================================================                 |  76%
  |                                                                            
  |======================================================                |  77%
  |                                                                            
  |======================================================                |  78%
  |                                                                            
  |=======================================================               |  78%
  |                                                                            
  |=======================================================               |  79%
  |                                                                            
  |========================================================              |  79%
  |                                                                            
  |========================================================              |  80%
  |                                                                            
  |========================================================              |  81%
  |                                                                            
  |=========================================================             |  81%
  |                                                                            
  |=========================================================             |  82%
  |                                                                            
  |==========================================================            |  82%
  |                                                                            
  |==========================================================            |  83%
  |                                                                            
  |==========================================================            |  84%
  |                                                                            
  |===========================================================           |  84%
  |                                                                            
  |===========================================================           |  85%
  |                                                                            
  |============================================================          |  85%
  |                                                                            
  |============================================================          |  86%
  |                                                                            
  |=============================================================         |  87%
  |                                                                            
  |=============================================================         |  88%
  |                                                                            
  |==============================================================        |  88%
  |                                                                            
  |==============================================================        |  89%
  |                                                                            
  |===============================================================       |  89%
  |                                                                            
  |===============================================================       |  90%
  |                                                                            
  |===============================================================       |  91%
  |                                                                            
  |================================================================      |  91%
  |                                                                            
  |================================================================      |  92%
  |                                                                            
  |=================================================================     |  92%
  |                                                                            
  |=================================================================     |  93%
  |                                                                            
  |=================================================================     |  94%
  |                                                                            
  |==================================================================    |  94%
  |                                                                            
  |==================================================================    |  95%
  |                                                                            
  |===================================================================   |  95%
  |                                                                            
  |===================================================================   |  96%
  |                                                                            
  |====================================================================  |  97%
  |                                                                            
  |====================================================================  |  98%
  |                                                                            
  |===================================================================== |  98%
  |                                                                            
  |===================================================================== |  99%
  |                                                                            
  |======================================================================|  99%
  |                                                                            
  |======================================================================| 100%
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-7-1.png" width="672" />

Here, we can see how the Obama campaign deployed a widespread and dense network of field offices, with a heavy concentration in battleground states like Ohio, Florida, and Pennsylvania. Clinton followed a similar strategy, with a focus on the midwest battleground states. However, a lagging voter turnout in crucial areas especially among working-class voters in the Rust Belt was one of the reasons for her defeat.

The Republicans showed a significant shift from 2012 to 2016. We can see how Trump's campaign had a more focused field office strategy, targeting specific battlegrounds like Florida, Pennsylvania, Ohio, and Michigan. However, his campaign relied heavily on digital outreach and rallies rather than a traditional field game. This unconventional method allowed him to increase his presence and reach, generating a high turnout particularly in rural and exurban areas.

## The Democratic Ground Game

Let's dive deeper into Democratic field office work in battleground states by comparing some more historical trends:

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-8-1.png" width="672" />

Here we can see how in in most battleground states, Clinton had fewer field offices in 2016 than Obama did in 2008, indicated by the substantial negative values. This was detrimental when considering crucial states like Ohio, Florida, and Pennsylvania —- all critical swing states that contributed to Trump's victory.

Ultimately, we see that the graph shows how Clinton's campaign may have underinvested in field offices compared to Obama’s 2008 campaign, particularly in key Rust Belt states. These offices play a crucial role in get-out-the-vote (GOTV) efforts, volunteer mobilization, and localized campaign activities. This made it more challenging to mobilize Democratic voters, particularly in areas where voter enthusiasm may have been lower than in 2008.

Let's continue our analysis by looking at 2012 and 2016:

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-9-1.png" width="672" />

Once again, we see how Obama had a dense network of field offices spread across Wisconsin, particularly concentrated in urban and suburban areas, including the Milwaukee and Madison regions. His aggressive ground game aimed at reaching a wide range of voters through traditional canvassing and mobilization efforts. Comparatively, we can see Clinton's fewer offices, especially in rural areas and northern parts of the state.


<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-10-1.png" width="672" />

As expected, Obama won many counties by comfortable margins, particularly in urban areas like Milwaukee, Madison, and along the state's southern border. His distribution across both urban and rural areas helped increase turnout in a wide range of regions.

Comparatively for Clinton, many of the counties turned red, highlighting how Trump outperformed Clinton in large parts of Wisconsin, especially in rural areas. Her concentrated efforts in urban areas came at the cost of losing support from rural and working-class voters.

## So...What About 2024?

With the context and analysis we have now, let's examine current trends and events:

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-11-1.png" width="672" />


```
## 
  |                                                                            
  |                                                                      |   0%
  |                                                                            
  |=                                                                     |   1%
  |                                                                            
  |=                                                                     |   2%
  |                                                                            
  |==                                                                    |   2%
  |                                                                            
  |==                                                                    |   3%
  |                                                                            
  |===                                                                   |   4%
  |                                                                            
  |===                                                                   |   5%
  |                                                                            
  |====                                                                  |   5%
  |                                                                            
  |====                                                                  |   6%
  |                                                                            
  |=====                                                                 |   6%
  |                                                                            
  |=====                                                                 |   7%
  |                                                                            
  |=====                                                                 |   8%
  |                                                                            
  |======                                                                |   8%
  |                                                                            
  |======                                                                |   9%
  |                                                                            
  |=======                                                               |   9%
  |                                                                            
  |=======                                                               |  10%
  |                                                                            
  |========                                                              |  11%
  |                                                                            
  |========                                                              |  12%
  |                                                                            
  |=========                                                             |  12%
  |                                                                            
  |=========                                                             |  13%
  |                                                                            
  |==========                                                            |  14%
  |                                                                            
  |==========                                                            |  15%
  |                                                                            
  |===========                                                           |  15%
  |                                                                            
  |===========                                                           |  16%
  |                                                                            
  |============                                                          |  17%
  |                                                                            
  |============                                                          |  18%
  |                                                                            
  |=============                                                         |  18%
  |                                                                            
  |=============                                                         |  19%
  |                                                                            
  |==============                                                        |  20%
  |                                                                            
  |==============                                                        |  21%
  |                                                                            
  |===============                                                       |  21%
  |                                                                            
  |===============                                                       |  22%
  |                                                                            
  |================                                                      |  23%
  |                                                                            
  |================                                                      |  24%
  |                                                                            
  |=================                                                     |  24%
  |                                                                            
  |=================                                                     |  25%
  |                                                                            
  |==================                                                    |  25%
  |                                                                            
  |==================                                                    |  26%
  |                                                                            
  |===================                                                   |  27%
  |                                                                            
  |===================                                                   |  28%
  |                                                                            
  |====================                                                  |  28%
  |                                                                            
  |====================                                                  |  29%
  |                                                                            
  |=====================                                                 |  29%
  |                                                                            
  |=====================                                                 |  30%
  |                                                                            
  |======================                                                |  31%
  |                                                                            
  |======================                                                |  32%
  |                                                                            
  |=======================                                               |  32%
  |                                                                            
  |=======================                                               |  33%
  |                                                                            
  |========================                                              |  34%
  |                                                                            
  |========================                                              |  35%
  |                                                                            
  |=========================                                             |  35%
  |                                                                            
  |=========================                                             |  36%
  |                                                                            
  |==========================                                            |  37%
  |                                                                            
  |==========================                                            |  38%
  |                                                                            
  |===========================                                           |  38%
  |                                                                            
  |===========================                                           |  39%
  |                                                                            
  |============================                                          |  40%
  |                                                                            
  |=============================                                         |  41%
  |                                                                            
  |=============================                                         |  42%
  |                                                                            
  |==============================                                        |  42%
  |                                                                            
  |==============================                                        |  43%
  |                                                                            
  |===============================                                       |  44%
  |                                                                            
  |===============================                                       |  45%
  |                                                                            
  |================================                                      |  45%
  |                                                                            
  |================================                                      |  46%
  |                                                                            
  |=================================                                     |  47%
  |                                                                            
  |=================================                                     |  48%
  |                                                                            
  |==================================                                    |  48%
  |                                                                            
  |==================================                                    |  49%
  |                                                                            
  |===================================                                   |  50%
  |                                                                            
  |===================================                                   |  51%
  |                                                                            
  |====================================                                  |  51%
  |                                                                            
  |====================================                                  |  52%
  |                                                                            
  |=====================================                                 |  53%
  |                                                                            
  |======================================                                |  54%
  |                                                                            
  |======================================                                |  55%
  |                                                                            
  |=======================================                               |  56%
  |                                                                            
  |========================================                              |  56%
  |                                                                            
  |========================================                              |  57%
  |                                                                            
  |=========================================                             |  58%
  |                                                                            
  |=========================================                             |  59%
  |                                                                            
  |==========================================                            |  59%
  |                                                                            
  |==========================================                            |  60%
  |                                                                            
  |===========================================                           |  61%
  |                                                                            
  |===========================================                           |  62%
  |                                                                            
  |============================================                          |  62%
  |                                                                            
  |============================================                          |  63%
  |                                                                            
  |=============================================                         |  64%
  |                                                                            
  |=============================================                         |  65%
  |                                                                            
  |==============================================                        |  65%
  |                                                                            
  |==============================================                        |  66%
  |                                                                            
  |===============================================                       |  67%
  |                                                                            
  |===============================================                       |  68%
  |                                                                            
  |================================================                      |  68%
  |                                                                            
  |================================================                      |  69%
  |                                                                            
  |=================================================                     |  69%
  |                                                                            
  |=================================================                     |  70%
  |                                                                            
  |==================================================                    |  71%
  |                                                                            
  |==================================================                    |  72%
  |                                                                            
  |===================================================                   |  72%
  |                                                                            
  |===================================================                   |  73%
  |                                                                            
  |====================================================                  |  74%
  |                                                                            
  |====================================================                  |  75%
  |                                                                            
  |=====================================================                 |  75%
  |                                                                            
  |=====================================================                 |  76%
  |                                                                            
  |======================================================                |  77%
  |                                                                            
  |======================================================                |  78%
  |                                                                            
  |=======================================================               |  78%
  |                                                                            
  |=======================================================               |  79%
  |                                                                            
  |========================================================              |  80%
  |                                                                            
  |========================================================              |  81%
  |                                                                            
  |=========================================================             |  81%
  |                                                                            
  |=========================================================             |  82%
  |                                                                            
  |==========================================================            |  83%
  |                                                                            
  |==========================================================            |  84%
  |                                                                            
  |===========================================================           |  84%
  |                                                                            
  |===========================================================           |  85%
  |                                                                            
  |============================================================          |  85%
  |                                                                            
  |============================================================          |  86%
  |                                                                            
  |=============================================================         |  87%
  |                                                                            
  |==============================================================        |  88%
  |                                                                            
  |==============================================================        |  89%
  |                                                                            
  |===============================================================       |  89%
  |                                                                            
  |===============================================================       |  90%
  |                                                                            
  |================================================================      |  91%
  |                                                                            
  |================================================================      |  92%
  |                                                                            
  |=================================================================     |  92%
  |                                                                            
  |=================================================================     |  93%
  |                                                                            
  |==================================================================    |  94%
  |                                                                            
  |==================================================================    |  95%
  |                                                                            
  |===================================================================   |  95%
  |                                                                            
  |===================================================================   |  96%
  |                                                                            
  |====================================================================  |  97%
  |                                                                            
  |====================================================================  |  98%
  |                                                                            
  |===================================================================== |  98%
  |                                                                            
  |===================================================================== |  99%
  |                                                                            
  |======================================================================|  99%
  |                                                                            
  |======================================================================| 100%
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-12-1.png" width="672" />

As predicted, both parties are concentrating their campaign events in battleground states like Michigan, Wisconsin, Pennsylvania, and Arizona. These are crucial Rust Belt states that helped decide the 2020 election results. They also have significant presence in Georgia and Arizona, two historically Republican states that flipped to the Democrats in 2020, suggesting that Democrats are working to consolidate their gains in these areas.

The Republicans are also concentrating in battleground states, but are also hosting events in Ohio, Florida, and North Carolina, which have been traditionally red but occasionally competitive. Doing so will help secure support in historical strongholds while trying to gain a competitive edge in the swing states.

Overall, we can see how both parties are hosting fewer campaign events in traditionally solid states, allocating more of their resources to swing states where the outcomes are more unpredictable.

## Campaign Events and Predicting Vote Share?

We can clearly see trends in campaign events, presence in battleground states, and historical results. Thus, can we use this information to improve our model and predict vote share?

Let's find out:


```
## 
## Call:
## lm(formula = D_pv2p ~ n_ev_D + ev_diff_D_R, data = d)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -17.6200  -4.7648   0.3904   5.2448  16.4092 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 48.18936    0.36910 130.559  < 2e-16 ***
## n_ev_D       0.12588    0.03351   3.757 0.000186 ***
## ev_diff_D_R  0.10453    0.06726   1.554 0.120618    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 7.508 on 711 degrees of freedom
##   (6896 observations deleted due to missingness)
## Multiple R-squared:  0.02141,	Adjusted R-squared:  0.01866 
## F-statistic: 7.778 on 2 and 711 DF,  p-value: 0.0004554
```

Here, we can see that for each additional Democratic event in a state, the Democratic vote share is expected to increase by roughly 0.126 percentage points, with a statistically significant p-value. However, the R-squared value is very low, suggesting that it only captures roughly 1.87% of the variance in state-level vote share based on the number of campaign events and the event difference.

Is this consistent with the Republican model?


```
## 
## Call:
## lm(formula = R_pv2p ~ n_ev_R + ev_diff_R_D, data = d)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -16.4088  -5.2449  -0.3903   4.7647  17.6204 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 51.81023    0.36913 140.359  < 2e-16 ***
## n_ev_R      -0.12586    0.03351  -3.756 0.000187 ***
## ev_diff_R_D  0.23042    0.07796   2.956 0.003222 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 7.509 on 711 degrees of freedom
##   (6896 observations deleted due to missingness)
## Multiple R-squared:  0.02141,	Adjusted R-squared:  0.01865 
## F-statistic: 7.776 on 2 and 711 DF,  p-value: 0.0004564
```

Interestingly, the model predicts that for each additional Republican event, the Republican vote share decreases by roughly 0.126 percentage points, with a statistically significant p-value. This could mean that Republicans tend to hold events in more competitive or difficult states, where their baseline support is lower.

However, the positive association in ev_diff_R_D suggests that Republicans benefit more then they hold more events than Democrats, showing how the ground game is particularly effective when they out-campaign Democrats in terms of event presence.

Still, the very low R-squared value suggests that while campaign events can influence voter engagement and media coverage, they cannot account for most of the variance in state-level vote shares. Statistically, the high residual standard error compared to the effect sizes indicates that there is a lot of unexplained noise in the relationship between the predictors. While the coefficients are statistically significant, meaning there is some measurable impact on vote share, it is relatively small in the broader context of all factors that determine election outcomes that we've seen in previous blogs.

Thus, I predict that its addition in our model will likely produce negligible impact...especially when election day 14 days away!

Stay tuned!

## Data Sources:

Data are from the GOV 1347 course materials. All files can be found [here](https://github.com/cys9772/election-blog4). All external sources are hyperlinked.
