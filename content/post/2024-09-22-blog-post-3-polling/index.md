---
title: Blog Post 3 –– Polling and Prediction
author: Chris S
date: '2024-09-22'
slug: blog-post-3-polling
categories: []
tags: []
---


``` r
library(ggplot2)
library(maps)
library(knitr)
library(tidyverse)
```

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

``` r
library(dplyr)
library(broom)
library(car)
```

```
## Loading required package: carData
## 
## Attaching package: 'car'
## 
## The following object is masked from 'package:dplyr':
## 
##     recode
## 
## The following object is masked from 'package:purrr':
## 
##     some
```

``` r
library(caret)
```

```
## Loading required package: lattice
## 
## Attaching package: 'caret'
## 
## The following object is masked from 'package:purrr':
## 
##     lift
```

``` r
library(tidyverse)
library(glmnet)
```

```
## Loading required package: Matrix
## 
## Attaching package: 'Matrix'
## 
## The following objects are masked from 'package:tidyr':
## 
##     expand, pack, unpack
## 
## Loaded glmnet 4.1-8
```

``` r
d_pollav_natl <- read_csv("/Users/chrisshen/Downloads/GOV1347/Election Blog 4/national_polls_1968-2024.csv")
```

```
## Rows: 7378 Columns: 9
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr  (3): state, party, candidate
## dbl  (4): year, weeks_left, days_left, poll_support
## lgl  (1): before_convention
## date (1): poll_date
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

``` r
d_pollav_state <- read_csv("/Users/chrisshen/Downloads/GOV1347/Election Blog 4/state_polls_1968-2024.csv")
```

```
## Rows: 204564 Columns: 9
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr  (3): state, party, candidate
## dbl  (4): year, weeks_left, days_left, poll_support
## lgl  (1): before_convention
## date (1): poll_date
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

``` r
d_vote <- read_csv("/Users/chrisshen/Downloads/GOV1347/Election Blog 4/popvote_1948-2020.csv")
```

```
## Rows: 38 Columns: 9
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (2): party, candidate
## dbl (3): year, pv, pv2p
## lgl (4): winner, incumbent, incumbent_party, prev_admin
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

``` r
d_vote$party[d_vote$party == "democrat"] <- "DEM"
d_vote$party[d_vote$party == "republican"] <- "REP"
d_poll_weeks <- d_pollav_natl |> 
  group_by(year, party, weeks_left) |>
  summarize(mean_poll_week = mean(poll_support)) |> 
  filter(weeks_left <= 30) |> 
  pivot_wider(names_from = weeks_left, values_from = mean_poll_week) |> 
  left_join(d_vote, by = c("year", "party"))
```

```
## `summarise()` has grouped output by 'year', 'party'. You can override using the
## `.groups` argument.
```

*This blog is an ongoing assignment for Gov 1347: Election Analytics, a course at Harvard College taught by Professor [Ryan Enos](https://www.ryandenos.com/). It will be updated weekly and culminate in a predictive model of the 2024 presidential election.*

## Introduction -- How can we best use **polls** to predict **election outcomes?**

What's the first thing you hear when you flick on news channels covering any election? They talk about polls. We've done hundreds of them ourselves in our lifetime, from restaurant surveys to product reviews and presidential candidates. These quick little quizzes help provide direct insights from the public about various issues, including voting intensions, candidate favoriability, and more. More generally, they serve as feedback loops, allowing parties and campaigns to adjust their strategies based on how the public responds during an election cycle. Breaking these statistics down by demographic categories allow these candidates to further tailer their messaging to specific audiences. 

However, there are common pitfalls related to polling, including sampling bias, nonresponse bias, timing, and more. It's our job to figure out what matters and what doesn't. More importantly, can we use them to help predict election outcomes,  or will they simply lead us astray? Let's dive in!

## 2020 Polling -- A Detailed Analysis


``` r
d_pollav_natl |> 
  filter(year == 2020) |> 
  ggplot(aes(x = poll_date, y = poll_support, color = party)) +
  geom_rect(xmin = as.Date("2020-08-17"), xmax = as.Date("2020-08-20"), ymin = 47.5, ymax = 100, alpha = 0.1, color = NA, fill = "grey") + 
  annotate("text", x = as.Date("2020-08-07"), y = 51.5, label = "DNC", size = 4) +
  geom_rect(xmin = as.Date("2020-08-24"), xmax = as.Date("2020-08-27"), ymin = 0, ymax = 47.2, alpha = 0.1, color = NA, fill = "grey") +
  annotate("text", x = as.Date("2020-09-04"), y = 45, label = "RNC", size = 4) +
  geom_rect(xmin = as.Date("2020-10-02"), xmax = as.Date("2020-10-12"), ymin = 0, ymax = 42.7, alpha = 0.05, color = NA, fill = "grey") +
  
  geom_point(size = 1) + 
  geom_line() + 
  
  geom_segment(x = as.Date("2020-03-12"), xend = as.Date("2020-03-12"), y = 0, yend = 44.8, linetype = "dashed", alpha = 0.4, color = "grey") +
  annotate("text", x = as.Date("2020-03-12"), y = 42.5, label = "COVID \n Market Crash", size = 3) +
  geom_segment(x = as.Date("2020-04-08"), xend = as.Date("2020-04-08"), y = 49, yend = 100, linetype = "dashed", alpha = 0.4, color = "grey") +
  annotate("text", x = as.Date("2020-03-25"), y = 51.3, label = "Bernie Ends Run", size = 3) +
  geom_segment(x = as.Date("2020-04-16"), xend = as.Date("2020-04-16"), y = 0, yend = 44, linetype = "dashed", alpha = 0.4, color = "grey") +
  annotate("text", x = as.Date("2020-04-16"), y = 44.7, label = "22 mil \n Unemployment", size = 3) +
  geom_segment(x = as.Date("2020-05-27"), xend = as.Date("2020-05-27"), y = 0, yend = 43, linetype = "dashed", alpha = 0.4, color = "grey") +
  annotate("text", x = as.Date("2020-06-05"), y = 44, label = "100k COVID Dead, \n George Floyd", size = 3) +
  
  geom_segment(x = as.Date("2020-07-14"), xend = as.Date("2020-07-14"), y = 0, yend = 50.3, linetype = "dashed", alpha = 0.4, color = "grey") +
  annotate("text", x = as.Date("2020-06-19"), y = 47.5, label = "Moderna Announces", size = 3) +
  
  geom_segment(x = as.Date("2020-09-29"), xend = as.Date("2020-09-29"), y = 50, yend = 100, linetype = "dashed", alpha = 0.4, color = "grey") +
  annotate("text", x = as.Date("2020-9-12"), y = 49.5, label = "Pres. Debate", size = 3) +
  geom_segment(x = as.Date("2020-10-07"), xend = as.Date("2020-10-07"), y = 51.7, yend = 100, linetype = "dashed", alpha = 0.4, color = "grey") +
  annotate("text", x = as.Date("2020-10-17"), y = 50.3, label = "VP Debate", size = 3) +
  geom_segment(x = as.Date("2020-10-22"), xend = as.Date("2020-10-22"), y = 52, yend = 100, linetype = "dashed", alpha = 0.4, color = "grey") +
  annotate("text", x = as.Date("2020-10-30"), y = 51.5, label = "Pres. Debate", size = 3) +
  annotate("text", x = as.Date("2020-10-15"), y = 43.7, label = "Trump Has COVID", size = 3) +
  geom_segment(x = as.Date("2020-09-18"), xend = as.Date("2020-09-18"), y = 50, yend = 100, linetype = "dashed", alpha = 0.4, color = "grey") +
  annotate("text", x = as.Date("2020-09-03"), y = 51.5, label = "RBG Passes", size = 3) +
  
  scale_x_date(date_labels = "%b %d") + 
  scale_color_manual(values = c("dodgerblue4", "firebrick1")) +
  labs(x = "Date",
       y = "Average Poll Approval", 
       title = "Polling Averages by Date, 2020 (with Game Changers)") + 
  theme_classic()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" width="672" />

Immediately, we can notice some interesting trends. First, before COVID_19 hit, both democratic and republican polling averages were roughly stable. However, once COVID-19 was in full swing, we see a significant decline in Republican approval, likely due to the public's perception of how the Trump was handling the pandemic, on both the healthcare and economic fronts. With George Floyd's death, we see a sharp decline in Republican approval, especially when faced with handling racial justice issues and social upheaval. Both the RNC and DNCs showed a bump in approval allowing parties to get in front of viewers, increase media coverage, and consolidating support.


``` r
d_pollav_natl |> 
  filter(year == 2024) |> 
  ggplot(aes(x = poll_date, y = poll_support, color = party)) +
  geom_point(size = 1) + 
  geom_line() + 
  scale_x_date(date_labels = "%b %d") + 
  scale_color_manual(values = c("dodgerblue4", "firebrick1")) +
  labs(x = "Date",
       y = "Average Poll Approval", 
       title = "Polling Averages by Date, 2024") + 
  theme_classic()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-3-1.png" width="672" />

Looking at 2024 polling averages, we see a general candidate disapproval trend for both parties. Particularly for the Biden administration, the democratic party was struggling to gain ground as the November election quickly approached. The sharp increase we observe for the democrats has to do with Biden's decision to drop out of the race and endorse Kamala Harris as the democratic presidential candidate. Moreover, her strong performance in the recent debate proved her capabilities as a leader and vision for the future which many American seemed to resonate with.

Altogether, we can see the influence of major political, social, and health-related events on candidate approval, which can be captured by polling averages. Now the question is, do these polling behaviors provide any predictive power. Let's find out!

## Polls and Predictions -- How about November?

Before we look into 2024, let's examine how powerful November polling averages can be at predicting election outcomes for the democratic party in 2020:


``` r
d_poll_nov <- d_vote |> 
  left_join(d_pollav_natl |> 
              group_by(year, party) |> 
              top_n(1, poll_date) |> 
              select(-candidate), 
            by = c("year", "party")) |> 
  rename(nov_poll = poll_support) |> 
  filter(year <= 2020) |> 
  drop_na()

ols.nov.1 <- lm(pv2p ~ nov_poll, 
                data = subset(d_poll_nov, party == "DEM"))
summary(ols.nov.1)
```

```
## 
## Call:
## lm(formula = pv2p ~ nov_poll, data = subset(d_poll_nov, party == 
##     "DEM"))
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -4.0155 -2.4353 -0.3752  1.4026  5.8014 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  14.2936     7.1693   1.994 0.069416 .  
## nov_poll      0.7856     0.1608   4.885 0.000376 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.968 on 12 degrees of freedom
## Multiple R-squared:  0.6654,	Adjusted R-squared:  0.6375 
## F-statistic: 23.86 on 1 and 12 DF,  p-value: 0.0003756
```

There are a few critical results to note about the model above. First, the coefficient for **nov_poll** (0.7856) means that for each 1-point increase in November polling support, the predicted vote share increases by 0.79. Moreover, the p-value is extremely small (0.000376), indicating that the **nov_poll** variable is a statistically significant predictor of pv2p. What's more impressive is the multiple R-squared value of 0.66554 (closer to 1 is better), showing us that the model provides a reasonably good fit and reliability. In the context of the 2020 election results, we can see that polling support in November was a strong indicator of electoral success. With it's proximity to election day, polls can capture the most recent public opinion, particularly since voters will have likely made up their minds by now. As we've seen before, major campaign events have great influences on voter support and approval, and as the election nears, there are simply less of these so opinions are less volatile.

Instead of focusing solely on the democratic party, let's widen our scope to all parties in the dataset in the following model:


``` r
ols.nov.2 <- lm(pv2p ~ nov_poll, 
                data = d_poll_nov)
summary(ols.nov.2)
```

```
## 
## Call:
## lm(formula = pv2p ~ nov_poll, data = d_poll_nov)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -4.6190 -1.6523 -0.5808  1.3629  6.0220 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 17.92577    4.15543   4.314 0.000205 ***
## nov_poll     0.70787    0.09099   7.780 2.97e-08 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.75 on 26 degrees of freedom
## Multiple R-squared:  0.6995,	Adjusted R-squared:  0.6879 
## F-statistic: 60.52 on 1 and 26 DF,  p-value: 2.974e-08
```

Similar to our previous model, we see that the **nov_poll** coefficient (0.70787) means that for each 1-point increase in November polling support, the predicted vote share increases by roughly 0.71 points. The positive relationship indicates that higher polling support in November is strongly associated with higher vote share. Once again, we see that the p-value is extremely lough, reinforcing the statistical significance of November's reliability as an indicator. A strong 0.7 R-squared value also provides confidence that the model is robust and capturing the data effectively. More importantly, this model confirms that November polls are a strong predictor of election outcomes, regardless of party affiliation.


``` r
d_poll_weeks_train <- d_poll_weeks |> 
  filter(year <= 2020)
d_poll_weeks_test <- d_poll_weeks |> 
  filter(year == 2024)

colnames(d_poll_weeks)[3:33] <- paste0("poll_weeks_left_", 0:30)
colnames(d_poll_weeks_train)[3:33] <- paste0("poll_weeks_left_", 0:30)
colnames(d_poll_weeks_test)[3:33] <- paste0("poll_weeks_left_", 0:30)
```

## The Power of Weeks and Ridge Regression

Instead of looking just at November results, let's examine how our model performs when we consider weekly polling averages. Of course, we run into an alarming issue -- multicolinearity. This means that we have several variables in our model that are correlated, producing a skewed view of our results. To handle this, we'll use ridge regression, which introduces a **penalty term** to the least squares cost function, preventing the coefficient from becoming too large, helping to adjust the multicolinearity factor. Let's examine our results:


``` r
ols.pollweeks <- lm(paste0("pv2p ~ ", paste0( "poll_weeks_left_", 0:30, collapse = " + ")), 
                    data = d_poll_weeks_train)
summary(ols.pollweeks)
```

```
## 
## Call:
## lm(formula = paste0("pv2p ~ ", paste0("poll_weeks_left_", 0:30, 
##     collapse = " + ")), data = d_poll_weeks_train)
## 
## Residuals:
## ALL 28 residuals are 0: no residual degrees of freedom!
## 
## Coefficients: (4 not defined because of singularities)
##                    Estimate Std. Error t value Pr(>|t|)
## (Intercept)        28.25534        NaN     NaN      NaN
## poll_weeks_left_0   3.24113        NaN     NaN      NaN
## poll_weeks_left_1   0.02516        NaN     NaN      NaN
## poll_weeks_left_2  -8.87360        NaN     NaN      NaN
## poll_weeks_left_3   7.91455        NaN     NaN      NaN
## poll_weeks_left_4   0.74573        NaN     NaN      NaN
## poll_weeks_left_5   1.41567        NaN     NaN      NaN
## poll_weeks_left_6  -4.58444        NaN     NaN      NaN
## poll_weeks_left_7   4.63361        NaN     NaN      NaN
## poll_weeks_left_8  -0.95121        NaN     NaN      NaN
## poll_weeks_left_9  -1.55307        NaN     NaN      NaN
## poll_weeks_left_10 -1.38062        NaN     NaN      NaN
## poll_weeks_left_11  1.74881        NaN     NaN      NaN
## poll_weeks_left_12 -1.28871        NaN     NaN      NaN
## poll_weeks_left_13 -0.08482        NaN     NaN      NaN
## poll_weeks_left_14  0.87498        NaN     NaN      NaN
## poll_weeks_left_15 -0.16310        NaN     NaN      NaN
## poll_weeks_left_16 -0.34501        NaN     NaN      NaN
## poll_weeks_left_17 -0.38689        NaN     NaN      NaN
## poll_weeks_left_18 -0.06281        NaN     NaN      NaN
## poll_weeks_left_19 -0.17204        NaN     NaN      NaN
## poll_weeks_left_20  1.52230        NaN     NaN      NaN
## poll_weeks_left_21 -0.72487        NaN     NaN      NaN
## poll_weeks_left_22 -2.76531        NaN     NaN      NaN
## poll_weeks_left_23  4.90361        NaN     NaN      NaN
## poll_weeks_left_24 -2.04431        NaN     NaN      NaN
## poll_weeks_left_25 -0.76078        NaN     NaN      NaN
## poll_weeks_left_26 -0.47860        NaN     NaN      NaN
## poll_weeks_left_27       NA         NA      NA       NA
## poll_weeks_left_28       NA         NA      NA       NA
## poll_weeks_left_29       NA         NA      NA       NA
## poll_weeks_left_30       NA         NA      NA       NA
## 
## Residual standard error: NaN on 0 degrees of freedom
## Multiple R-squared:      1,	Adjusted R-squared:    NaN 
## F-statistic:   NaN on 27 and 0 DF,  p-value: NA
```

``` r
x.train <- d_poll_weeks_train |>
  ungroup() |> 
  select(all_of(starts_with("poll_weeks_left_"))) |> 
  as.matrix()
y.train <- d_poll_weeks_train$pv2p
```

The models shows that we have no residual degrees of freedom, meaning we have perfect multicolinearity, which happens when there is an exact linear dependence among the predicators, in this case, weekly polling averages. The R-squared value is 1.0, indicating perfect fit, but this misleading since this fit is not due to the model's inherent capabilities, but rather from overfitting caused by too many correlated predictors.

To better understand what we're doing, let's visualize our ridge regression:


``` r
ridge.pollsweeks <- glmnet(x = x.train, y = y.train, alpha = 0) # Set ridge using alpha = 0. 
plot(ridge.pollsweeks, xvar = "lambda")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-8-1.png" width="672" />

``` r
coef(ridge.pollsweeks, s = 0.1)
```

```
## 32 x 1 sparse Matrix of class "dgCMatrix"
##                              s1
## (Intercept)        29.951147799
## poll_weeks_left_0   0.032163983
## poll_weeks_left_1   0.025440084
## poll_weeks_left_2   0.024404320
## poll_weeks_left_3   0.024688870
## poll_weeks_left_4   0.024695646
## poll_weeks_left_5   0.024725772
## poll_weeks_left_6   0.024080438
## poll_weeks_left_7   0.023636908
## poll_weeks_left_8   0.024487501
## poll_weeks_left_9   0.026498950
## poll_weeks_left_10  0.025642838
## poll_weeks_left_11  0.021361476
## poll_weeks_left_12  0.017386999
## poll_weeks_left_13  0.013378030
## poll_weeks_left_14  0.010078675
## poll_weeks_left_15  0.007248494
## poll_weeks_left_16  0.012943440
## poll_weeks_left_17  0.012879654
## poll_weeks_left_18  0.011157452
## poll_weeks_left_19  0.008302783
## poll_weeks_left_20  0.004012987
## poll_weeks_left_21  0.003350434
## poll_weeks_left_22  0.004458406
## poll_weeks_left_23  0.001019583
## poll_weeks_left_24 -0.002711193
## poll_weeks_left_25 -0.002447895
## poll_weeks_left_26  0.001121142
## poll_weeks_left_27  0.005975853
## poll_weeks_left_28  0.011623984
## poll_weeks_left_29  0.013833925
## poll_weeks_left_30  0.018964139
```

We can clearly see **lambda** acting as the regularization parameter here, adding more penalty to the coefficients and shrinking them towards zero to fight the multicolinearity and overfitting. This prevents any single variable from dominating the model, which is particularly important when dealing with models containing lots of multicolinearity. Of course, there are many other regularization methods, including OLS, Lasso, and Elastic Net, each with their own attributes. For the purposes of this blog, we will dive straight into a comparison between all three and draw conclusions, instead of an in-depth examination of each method:


``` r
lasso.pollsweeks <- glmnet(x = x.train, y = y.train, alpha = 1) 
enet.pollsweeks <- glmnet(x = x.train, y = y.train, alpha = 0.5)

cv.ridge.pollweeks <- cv.glmnet(x = x.train, y = y.train, alpha = 0)
```

```
## Warning: Option grouped=FALSE enforced in cv.glmnet, since < 3 observations per
## fold
```

``` r
cv.lasso.pollweeks <- cv.glmnet(x = x.train, y = y.train, alpha = 1)
```

```
## Warning: Option grouped=FALSE enforced in cv.glmnet, since < 3 observations per
## fold
```

``` r
cv.enet.pollweeks <- cv.glmnet(x = x.train, y = y.train, alpha = 0.5)
```

```
## Warning: Option grouped=FALSE enforced in cv.glmnet, since < 3 observations per
## fold
```

``` r
# Get minimum lambda values 
lambda.min.ridge <- cv.ridge.pollweeks$lambda.min
lambda.min.lasso <- cv.lasso.pollweeks$lambda.min
lambda.min.enet <- cv.enet.pollweeks$lambda.min

(mse.ridge <- mean((predict(ridge.pollsweeks, s = lambda.min.ridge, newx = x.train) - y.train)^2))
```

```
## [1] 9.575001
```

``` r
(mse.lasso <- mean((predict(lasso.pollsweeks, s = lambda.min.lasso, newx = x.train) - y.train)^2))
```

```
## [1] 4.575388
```

``` r
(mse.enet <- mean((predict(enet.pollsweeks, s = lambda.min.enet, newx = x.train) - y.train)^2))
```

```
## [1] 4.467311
```

``` r
# Generate plot comparing coefficients for each of the weeks. 
d.coefplot <- data.frame("OLS" = coef(ols.pollweeks)[-1], 
                         "Ridge" = coef(ridge.pollsweeks, s = lambda.min.ridge)[-1], 
                         "Lasso" = coef(lasso.pollsweeks, s = lambda.min.lasso)[-1], 
                         "Elastic Net" = coef(enet.pollsweeks, s = lambda.min.enet)[-1]) |> 
  rownames_to_column("coef_name") |> 
  pivot_longer(cols = -coef_name, names_to = "method", values_to = "coef_est") |> 
  mutate(week = rep(0:30, each = 4))

d.coefplot[which(is.na(d.coefplot$coef_est)),]$coef_est <- 0 

d.coefplot |>
  ggplot(aes(x = coef_est, y = reorder(coef_name, -week), color = method)) +
  geom_segment(aes(xend = 0, yend = reorder(coef_name, -week)), alpha = 0.5, lty = "dashed") +
  geom_vline(aes(xintercept = 0), lty = "dashed") +   
  geom_point() + 
  labs(x = "Coefficient Estimate", 
       y = "Coefficient Name", 
       title = "Comparison of Coefficients Across Regularization Methods") + 
  theme_classic()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-9-1.png" width="672" />

Comparing the fou

### STILL NEED TO TALK ABOUT PREDICTION AND BLOG EXTENSIONS


``` r
d_pollav_natl |> 
  filter(year == 2024) |> 
  select(weeks_left) |> 
  distinct() |> 
  range() # Let's take week 30 - 7 as predictors since those are the weeks we have polling data for 2024 and historically. 
```

```
## [1]  7 36
```

``` r
x.train <- d_poll_weeks_train |>
  ungroup() |> 
  select(all_of(paste0("poll_weeks_left_", 7:30))) |> 
  as.matrix()
y.train <- d_poll_weeks_train$pv2p
x.test <- d_poll_weeks_test |>
  ungroup() |> 
  select(all_of(paste0("poll_weeks_left_", 7:30))) |> 
  as.matrix()

# Using elastic-net for simplicity. 
set.seed(02138)
enet.poll <- cv.glmnet(x = x.train, y = y.train, alpha = 0.5)
```

```
## Warning: Option grouped=FALSE enforced in cv.glmnet, since < 3 observations per
## fold
```

``` r
lambda.min.enet.poll <- enet.poll$lambda.min

# Predict 2024 national pv2p share using elastic-net. 
(polls.pred <- predict(enet.poll, s = lambda.min.enet.poll, newx = x.test))
```

```
##            s1
## [1,] 51.79268
## [2,] 50.65879
```



``` r
d_econ <- read_csv("/Users/chrisshen/Downloads/GOV1347/Election Blog 4/fred_econ.csv") |> 
  filter(quarter == 2)
```

```
## Rows: 387 Columns: 14
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## dbl (14): year, quarter, GDP, GDP_growth_quarterly, RDPI, RDPI_growth_quarte...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

``` r
# Combine datasets and create vote lags. 
d_combined <- d_econ |> 
  left_join(d_poll_weeks, by = "year") |> 
  filter(year %in% c(unique(d_vote$year), 2024)) |> 
  group_by(party) |> 
  mutate(pv2p_lag1 = lag(pv2p, 1), 
         pv2p_lag2 = lag(pv2p, 2)) |> 
  ungroup() |> 
  mutate(gdp_growth_x_incumbent = GDP_growth_quarterly * incumbent, 
         rdpi_growth_quarterly = RDPI_growth_quarterly * incumbent,
         cpi_x_incumbent = CPI * incumbent,
         unemployment_x_incumbent = unemployment * incumbent,
         sp500_x_incumbent = sp500_close * incumbent) # Generate interaction effects.

# Create fundamentals-only dataset and split into training and test sets. 
d_fund <- d_combined |> 
  select("year", "pv2p", "GDP", "GDP_growth_quarterly", "RDPI", "RDPI_growth_quarterly", "CPI", "unemployment", "sp500_close",
         "incumbent", "gdp_growth_x_incumbent", "rdpi_growth_quarterly", "cpi_x_incumbent", "unemployment_x_incumbent", "sp500_x_incumbent", 
         "pv2p_lag1", "pv2p_lag2") 
x.train.fund <- d_fund |> 
  filter(year <= 2020) |>
  select(-c(year, pv2p)) |> 
  slice(-c(1:9)) |> 
  as.matrix()
y.train.fund <- d_fund |> 
  filter(year <= 2020) |> 
  select(pv2p) |> 
  slice(-c(1:9)) |> 
  as.matrix()
x.test.fund <- d_fund |> 
  filter(year == 2024) |> 
  select(-c(year, pv2p)) |> 
  drop_na() |> 
  as.matrix()
```

``` r
set.seed(02138)
enet.fund <- cv.glmnet(x = x.train.fund, y = y.train.fund, intercept = FALSE, alpha = 0.5)
```

```
## Warning: Option grouped=FALSE enforced in cv.glmnet, since < 3 observations per
## fold
```

``` r
lambda.min.enet.fund <- enet.fund$lambda.min

# Predict 2024 national pv2p share using elastic-net. 
(fund.pred <- predict(enet.fund, s = lambda.min.enet.fund, newx = x.test.fund))
```

```
## Warning in cbind2(1, newx): number of rows of result is not a multiple of
## vector length (arg 1)
```

```
##      s1
```

``` r
# Sequester data for combined model.
d_combo <- d_combined |> 
  select("year", "pv2p", "GDP", "GDP_growth_quarterly", "RDPI", "RDPI_growth_quarterly", "CPI", "unemployment", "sp500_close",
         "incumbent", "gdp_growth_x_incumbent", "rdpi_growth_quarterly", "cpi_x_incumbent", "unemployment_x_incumbent", "sp500_x_incumbent", 
         "pv2p_lag1", "pv2p_lag2", all_of(paste0("poll_weeks_left_", 7:30))) 

x.train.combined <- d_combo |> 
  filter(year <= 2020) |> 
  select(-c(year, pv2p)) |> 
  slice(-c(1:9)) |> 
  as.matrix()
y.train.combined <- d_combo |>
  filter(year <= 2020) |> 
  select(pv2p) |> 
  slice(-c(1:9)) |> 
  as.matrix()
x.test.combined <- d_combo |>
  filter(year == 2024) |> 
  select(-c(year, pv2p)) |> 
  drop_na() |> 
  as.matrix()
  
# Estimate combined model.
set.seed(02138)
enet.combined <- cv.glmnet(x = x.train.combined, y = y.train.combined, intercept = FALSE, alpha = 0.5)
```

```
## Warning: Option grouped=FALSE enforced in cv.glmnet, since < 3 observations per
## fold
```

``` r
lambda.min.enet.combined <- enet.combined$lambda.min

# Predict 2024 national pv2p share using elastic-net.
(combo.pred <- predict(enet.combined, s = lambda.min.enet.combined, newx = x.test.combined))
```

```
## Warning in cbind2(1, newx): number of rows of result is not a multiple of
## vector length (arg 1)
```

```
##      s1
```
## Data Sources:

Data are from the GOV 1347 course materials. All files can be found [here](https://github.com/cys9772/election-blog4). All external sources are hyperlinked.
