---
title: 'Blog Post 1: A Dive Into Detailed Visualizations'
author: Chris S
date: '2024-09-08'
slug: blog-post-1-a-dive-into-detailed-visualizations
categories: []
tags: []
---


```
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.4     ✔ readr     2.1.5
## ✔ forcats   1.0.0     ✔ stringr   1.5.1
## ✔ lubridate 1.9.3     ✔ tibble    3.2.1
## ✔ purrr     1.0.2     ✔ tidyr     1.3.1
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
## ✖ purrr::map()    masks maps::map()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

*This blog is an ongoing assignment for Gov 1347: Election Analytics, a course at Harvard College taught by Professor Ryan Enos. It will be updated weekly and culminate in a predictive model of the 2024 presidential election.*

## Introduction -- How competitive are presidential elections in the United States? Which states vote blue/red and how consistently?

As data analysts and political scientists get geared up for an exciting **2024 Presidential Election** between Kamala Harris (Democrat) and Donald Trump (Republican), we must first build an understanding of the landscape from historical data. This will give us the context necessary to build a substantial model that will hopefully predict the winner within reasonable margins.

## A Starting Point

To begin, let's start by examining the **two-party presidential popular vote** over time in the US from 1948 to 2020. The following is a line plot that depicts the how each political party's presidential popular vote has fluctuated over the decades indicated above.

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" width="672" />
Looking at the graph above, note that the Republican vote share is the **exact inverse** of the Democratic vote share. This makes sense since we're analyzing *two-party* vote share: Republican (R) + Democrat (D) = 1, meaning R = 1 - D. Mathematically, we can verify the visualization.

Next, we observe substantial volatility in from the 1960s to the 1980s, portrayed by sharp increases and decreases by both party graph lines. Historically, this was a period of great change and turmoil in American history. For example, the Civil Rights Movement, Vietnam War, and the Watergate scandal had dramatic influences on electoral shifts and popular vote decisions. Entering the 1990s and beyond saw periods of stability and narrowing of political polarization. More importantly, the **economy and global economic conditions** played greater roles in determining voter trends. Entering the 21st-century, such as the 2004 and 2016 elections, saw increased emphasis on economic dynamics [Fair 2018](https://fairmodel.econ.yale.edu/RAYFAIR/PDF/2018B.htm). Further analysis of economic influence will be discussed in later blog posts, so stay tuned!

## Geographical Additions


```
## Rows: 959 Columns: 14
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr  (1): state
## dbl (13): year, D_pv, R_pv, D_pv2p, R_pv2p, D_pv_lag1, R_pv_lag1, D_pv2p_lag...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

<div class="figure">
<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-3-1.png" alt="Added state abbreviations and custom theme as part of possible blog extension" width="672" />
<p class="caption"><span id="fig:unnamed-chunk-3"></span>Figure 1: Added state abbreviations and custom theme as part of possible blog extension</p>
</div>

```
## Warning in left_join(filter(d_pvstate_wide, year >= 1980), states_map, by = "region"): Detected an unexpected many-to-many relationship between `x` and `y`.
## ℹ Row 1 of `x` matches multiple rows in `y`.
## ℹ Row 1 of `y` matches multiple rows in `x`.
## ℹ If a many-to-many relationship is expected, set `relationship =
##   "many-to-many"` to silence this warning.
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-1.png" width="672" />
From second visualization, we can see that some states **consistently vote for one party**, indicating strong partisanship and party loyalty. More specifically, western coastal states like California, Oregon, and Washington and northeastern states such as New York and Massachusetts have consistently voted Democratic (blue). In contrast, the south and midwest states like Texas, Oklahoma, and Alabama have stuck with republican candidates. Research suggests factors including demographics, geography, cultural values, and economic conditions play key roles in these consistent states. For example, states with large urban populations with greater racial and ethnic diversity historically lean left compared to more white, working-class voters. Traditionally Christian communities in Alabama, Mississippi, and Oklahoma remain stiff on social issues that align more closely with the right. These are just a few significant identifiers among these states in a larger tug-of-war between blue and red.

Interestingly, we also see clear **swing states** among such regional loyalty like Florida, Ohio, and Pennsylvania with far more partisan variability over time. Within battlegrounds (eg. Texas) include many large urban centers, such as Austin and Houston, that lean left but remain overshadowed by the strength of suburban conservative voters. These will likely be key battleground areas that can shift the direction for both parties, amplifying the competitiveness of an already tense election.

## A Simple Model

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-1.png" width="672" />

```
## Rows: 936 Columns: 3
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (1): state
## dbl (2): electors, year
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```
## # A tibble: 2 × 2
##   winner electoral_votes
##   <chr>            <dbl>
## 1 D                  276
## 2 R                  262
```
Looking at the simple forecasting model above, we can see how swing states (nearly white, meaning zero or very small win margin) continue to be up for grabs, while consistently red and blue states still show similar patterns. The model also predicts southwest and sun belt states, such as Nevada and Arizona, will also be contentious, particularly as shifting political attitudes and younger Latino voters [Kim 2022](https://link.springer.com/article/10.1007/s11109-022-09816-z) become more involved. These changes will only heighten the competitive nature of this year's election, making the challenge of predicting the election ever-more interesting.

## Now What?

With an extensive understanding of the historical two-party presidential vote share and geographic voting trends, we can now begin diving deeper into more fundamental influences that shape elections, now and in the future.

## Data Sources:

Data are from the GOV 1347 course materials. All files can be found [here](https://github.com/cys9772/election-blog4).
