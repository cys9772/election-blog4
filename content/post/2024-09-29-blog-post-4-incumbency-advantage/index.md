---
title: Blog Post 4 –– Incumbency Advantage
author: Chris S
date: '2024-09-29'
slug: blog-post-4-incumbency-advantage
categories: []
tags: []
---




*This blog is an ongoing assignment for Gov 1347: Election Analytics, a course at Harvard College taught by Professor [Ryan Enos](https://www.ryandenos.com/). It will be updated weekly and culminate in a predictive model of the 2024 presidential election.*

## Introduction -- How should we treat the *incumbency advantage* in our 2024 electoral forecasts?

Humans are predictable creatures. For example, if you choose a seat to sit in on the first day of lecture, chances are, you'll choose the same spot next week. And the week after that -- probably until the last day of class. Is this the same for voting behavior? When it comes to elections, incumbents seem to hold a similar seat-saving advantage. Voters often stick with the familiar, and the incumbency advantage becomes a powerful force in shaping election outcomes. But is this advantage truly as reliable as picking the same lecture seat, or is it more complicated? 

Moreover, when existing presidents do a poor job at handling national concerns such as social upheaval or economic downturn, they leave a sour taste in the public's memory. Perhaps this tarnished reputation lingers, making voters more inclined to seek change rather than continuity -- just as how a coffee spill might make you choose a new lecture seat...

As usual, to analyze these questions, we'll start with the data. So, let's dive in!

## War and Incumbency

War is an unbelievably challenging time for most countries. It is in these pivotal moments when we look towards our leaders to navigate this environment with strength, decisiveness, and humility. Therefore, taking data between 1948 and 2024, I've produced the following summary for incumbency re-election:


|reelect_president |  N| Percent|
|:-----------------|--:|-------:|
|FALSE             | 12|   66.67|
|TRUE              |  6|   33.33|

We can immediately notice something -- in 12/18 post-war elections, the incumbent president did not win re-election, meaning that voters opted for change rather than continuing with the same president. Historically, the incumbent advantage seems to be weaker than what's often assumed, which may be due to several factors. For example, many of these incumbents faced significant fundamental economic crises, including Carter's era of inflation and Bush's period of recession. In doing so, voters may often seek fresh leadership if the current administration fails to address these national concerns effectively in the public's eye.

Of course, we also have to consider potential outliers -- such as Kennedy's close victory in 1960 or the Watergate scandal that impacted Ford's chances in 1976 -- that may have contributed to surprisingly low re-election rate.

However, the analysis above represents overall incumbency re-election. Let's see how it changes when the incumbent **actually runs** for re-election:


```
## Elections with At Least One Incumbent Running: 11
## Incumbent Victories: 7
## Percentage: 63.64
```

The data shows a slightly higher success rate (63.64%) when the incumbents choose to run for re-election. Under standard assumptions, we can see that these candidates benefit from increased name recognition, established campaign infrastructure, and the ability to leverage their position to shape public perception (e.g., by delivering policies or showing leadership in crises).

Let's zoom in even closer and look at the six elections since 2000:


| year|candidate_DEM    |candidate_REP    |incumbent_DEM |incumbent_REP |winner_DEM |winner_REP |
|----:|:----------------|:----------------|:-------------|:-------------|:----------|:----------|
| 2004|Kerry, John      |Bush, George W.  |FALSE         |TRUE          |FALSE      |TRUE       |
| 2012|Obama, Barack H. |Romney, Mitt     |TRUE          |FALSE         |TRUE       |FALSE      |
| 2020|Biden, Joseph R. |Trump, Donald J. |FALSE         |TRUE          |TRUE       |FALSE      |

The table above details the each of the specific candidates running in the past 3 elections. We see that 2/3 elections were won by incumbents seeking re-election (Bush in 2004 and Obama in 2012), which mathematically checks out roughly with our previous 63% calculation. More importantly, the 2020 election breaks the previous pattern of incumbency advantage, showing that a sitting president is still vulnerable, especially under critical circumstances including a national health crisis and economic upheaval.

## Candidate vs Party Incumbency

Instead of focusing solely on the candidates running for re-election, let's examine how the party incumbency performs during these situations:


|reelect_party |  N| Percent|
|:-------------|--:|-------:|
|FALSE         | 10|   55.56|
|TRUE          |  8|   44.44|

From above, we can see that 55.56% of the time, the party that held the presidency **did not** win the next election. Surprisingly, this suggests that the party in power is slightly more likely to **lose the presidency** than retain it. A big factor we're seeing here is party fatigue -- a phenomenon in which voters will often desire change if a party has been in power for multiple terms, particularly if it has faced challenges and controversies. This can be further amplified when a former president is very popular and the party fails to secure an impressive successor, which can be seen in the 1992 election after Reagan. Another simple example could be in 2016, in which Obama's two terms of success could not sustain the democratic party, and a general voter desire for change led to Trump's presidency.

There are also economic and political components in play, such as in 1980 when the Democrats controlled the presidency but Carter's disastrous handling of inflation and the Iran Hostage Crisis led to a republican victory by Reagan. Similarly, Trump's failed leadership during the COVID-19 pandemic further led to a loss in party confidence by voters, leading to an eventually transition to Biden and the democrats.

Overall, the near-even split between party retention and loss highlights the competitive and unpredictable nature of presidential elections, making our forecasting job more challenging and exciting!

## I'm In Power -- Now What?


``` r
d_pork_state |> 
  filter(!is.na(state_year_type)) |> 
  group_by(state_year_type) |>
  summarize(mean_grant = mean(grant_mil, na.rm = T), se_grant = sd(grant_mil, na.rm = T)/sqrt(n())) |> 
  ggplot(aes(x = state_year_type, y = mean_grant, ymin = mean_grant-1.96*se_grant, ymax = mean_grant+1.96*se_grant)) + 
  coord_flip() + 
  geom_bar(stat = "identity", fill = "chartreuse4") + 
  geom_errorbar(width = 0.2) + 
  labs(x = "Type of State & Year", 
       y = "Federal Grant Spending (Millions of $)", 
       title = "Federal Grant Spending (Millions $) by State Election Type") + 
  theme_minimal() + 
  theme(plot.title = element_text(size = 20),
        axis.title = element_text(size = 15),
        axis.text = element_text(size = 12))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-7-1.png" width="672" />





``` r
d_pork_state |> 
  filter(!is.na(state_year_type2)) |> 
  group_by(state_year_type2) |>
  summarize(mean_grant = mean(grant_mil, na.rm = T), se_grant = sd(grant_mil, na.rm = T)/sqrt(n())) |> 
  ggplot(aes(x = state_year_type2, y = mean_grant, ymin = mean_grant-1.96*se_grant, ymax = mean_grant+1.96*se_grant)) + 
  coord_flip() + 
  geom_bar(stat = "identity", fill = "chartreuse4") + 
  geom_errorbar(width = 0.2) + 
  labs(x = "Type of State & Year", 
       y = "Federal Grant Spending (Millions of $)", 
       title = "Federal Grant Spending (Millions $) by State Election Type") + 
  theme_minimal() +
  theme(plot.title = element_text(size = 20),
        axis.title = element_text(size = 15),
        axis.text = element_text(size = 12))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-8-1.png" width="672" />

# county model


``` r
pork_mod_county_1 <- lm(dvoteswing_inc  ~ dpct_grants*comp_state + as.factor(year), 
                      d_pork_county)
summary(pork_mod_county_1)
```

```
## 
## Call:
## lm(formula = dvoteswing_inc ~ dpct_grants * comp_state + as.factor(year), 
##     data = d_pork_county)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -27.7179  -2.8547  -0.0047   2.7889  23.2187 
## 
## Coefficients:
##                         Estimate Std. Error t value Pr(>|t|)    
## (Intercept)            -6.450079   0.084452 -76.376  < 2e-16 ***
## dpct_grants             0.004762   0.001036   4.595 4.35e-06 ***
## comp_state              0.152687   0.076143   2.005 0.044949 *  
## as.factor(year)1992     0.170688   0.115787   1.474 0.140458    
## as.factor(year)1996     6.345396   0.115509  54.934  < 2e-16 ***
## as.factor(year)2000    -2.049544   0.116215 -17.636  < 2e-16 ***
## as.factor(year)2004     8.407388   0.115576  72.743  < 2e-16 ***
## as.factor(year)2008     3.136792   0.116122  27.013  < 2e-16 ***
## dpct_grants:comp_state  0.006391   0.001764   3.623 0.000292 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 4.495 on 18455 degrees of freedom
##   (1 observation deleted due to missingness)
## Multiple R-squared:  0.4027,	Adjusted R-squared:  0.4025 
## F-statistic:  1556 on 8 and 18455 DF,  p-value: < 2.2e-16
```

``` r
pork_mod_county_2 <- lm(dvoteswing_inc ~ dpct_grants*comp_state + as.factor(year) +
                          dpc_income + inc_ad_diff + inc_campaign_diff + 
                          dhousevote_inc + iraq_cas2004 + iraq_cas2008 + 
                          dpct_popl,
                        data = d_pork_county)
summary(pork_mod_county_2)
```

```
## 
## Call:
## lm(formula = dvoteswing_inc ~ dpct_grants * comp_state + as.factor(year) + 
##     dpc_income + inc_ad_diff + inc_campaign_diff + dhousevote_inc + 
##     iraq_cas2004 + iraq_cas2008 + dpct_popl, data = d_pork_county)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -27.321  -2.848  -0.025   2.728  22.994 
## 
## Coefficients:
##                         Estimate Std. Error t value Pr(>|t|)    
## (Intercept)            -6.523210   0.084963 -76.777  < 2e-16 ***
## dpct_grants             0.003954   0.001043   3.792 0.000150 ***
## comp_state              0.155418   0.077223   2.013 0.044173 *  
## as.factor(year)1992    -0.156389   0.120591  -1.297 0.194699    
## as.factor(year)1996     6.230500   0.119533  52.124  < 2e-16 ***
## as.factor(year)2000    -2.000293   0.118588 -16.868  < 2e-16 ***
## as.factor(year)2004     8.248378   0.119371  69.099  < 2e-16 ***
## as.factor(year)2008     3.574248   0.124060  28.811  < 2e-16 ***
## dpc_income              0.134285   0.022326   6.015 1.84e-09 ***
## inc_ad_diff             0.061345   0.010851   5.654 1.60e-08 ***
## inc_campaign_diff       0.161845   0.013166  12.292  < 2e-16 ***
## dhousevote_inc          0.012093   0.001952   6.196 5.91e-10 ***
## iraq_cas2004           -0.153092   0.069585  -2.200 0.027816 *  
## iraq_cas2008           -0.164783   0.021677  -7.602 3.07e-14 ***
## dpct_popl               2.103344   0.530292   3.966 7.33e-05 ***
## dpct_grants:comp_state  0.006411   0.001781   3.600 0.000319 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 4.452 on 17943 degrees of freedom
##   (506 observations deleted due to missingness)
## Multiple R-squared:  0.4199,	Adjusted R-squared:  0.4194 
## F-statistic: 865.9 on 15 and 17943 DF,  p-value: < 2.2e-16
```

# pork state model


``` r
d_pork_state_model <- d_state_vote |>
  mutate(state_abb = state.abb[match(d_state_vote$state, state.name)]) |>
  inner_join(d_pork_state, by = c("year", "state_abb")) |>
  left_join(d_vote, by = "year") |>
  filter(incumbent_party == TRUE) |>
  mutate(inc_pv2p = ifelse(party == "REP", R_pv2p, D_pv2p)) |>
  mutate(is_comp = case_when(state_year_type == "swing + election year" ~ 1,
                             .default = 0)) |>
  group_by(state) |>
  mutate(change_grant_mil = (1-grant_mil/(lag(grant_mil, n = 1)))*100,
         change_inc_pv2p = (1-inc_pv2p/(lag(inc_pv2p, n = 1)))*100) |>
  ungroup() |>
  select(state, year, is_comp, change_grant_mil, change_inc_pv2p)
```

```
## Warning in left_join(inner_join(mutate(d_state_vote, state_abb = state.abb[match(d_state_vote$state, : Detected an unexpected many-to-many relationship between `x` and `y`.
## ℹ Row 1 of `x` matches multiple rows in `y`.
## ℹ Row 19 of `y` matches multiple rows in `x`.
## ℹ If a many-to-many relationship is expected, set `relationship =
##   "many-to-many"` to silence this warning.
```

``` r
pork_state_mod <- lm(change_inc_pv2p ~ is_comp*change_grant_mil + as.factor(year),
                     data = d_pork_state_model)
summary(pork_state_mod)
```

```
## 
## Call:
## lm(formula = change_inc_pv2p ~ is_comp * change_grant_mil + as.factor(year), 
##     data = d_pork_state_model)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -136.740   -6.628    0.341    7.176   64.748 
## 
## Coefficients:
##                          Estimate Std. Error t value Pr(>|t|)    
## (Intercept)                9.6346     3.6317   2.653  0.00842 ** 
## is_comp                   -0.4004     4.1498  -0.096  0.92319    
## change_grant_mil           0.1138     0.1051   1.082  0.28001    
## as.factor(year)1992        6.8952     6.7168   1.027  0.30548    
## as.factor(year)1996      -21.3789     5.2732  -4.054 6.46e-05 ***
## as.factor(year)2000        3.5773     5.6260   0.636  0.52537    
## as.factor(year)2004      -30.1619     5.4753  -5.509 7.96e-08 ***
## as.factor(year)2008        1.0850     4.8627   0.223  0.82360    
## is_comp:change_grant_mil  -0.1027     0.1643  -0.625  0.53246    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 23.43 on 291 degrees of freedom
##   (50 observations deleted due to missingness)
## Multiple R-squared:  0.2675,	Adjusted R-squared:  0.2474 
## F-statistic: 13.29 on 8 and 291 DF,  p-value: 2.299e-16
```

# time for a change model


``` r
d_tfc_train <- d_vote |> 
  left_join(d_econ, by = "year") |> 
  filter(incumbent_party) |>
  mutate(incumbent = as.numeric(incumbent))
```

```
## Warning in left_join(d_vote, d_econ, by = "year"): Detected an unexpected many-to-many relationship between `x` and `y`.
## ℹ Row 1 of `x` matches multiple rows in `y`.
## ℹ Row 82 of `y` matches multiple rows in `x`.
## ℹ If a many-to-many relationship is expected, set `relationship =
##   "many-to-many"` to silence this warning.
```

``` r
# estimate through 2016
tfc_mod_2016 <- lm(pv2p ~ GDP_growth_quarterly + incumbent + juneapp, 
                   data = subset(d_tfc_train, year < 2020))
summary(tfc_mod_2016)
```

```
## 
## Call:
## lm(formula = pv2p ~ GDP_growth_quarterly + incumbent + juneapp, 
##     data = subset(d_tfc_train, year < 2020))
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -5.3398 -2.4193  0.0809  1.3467  6.3340 
## 
## Coefficients:
##                      Estimate Std. Error t value Pr(>|t|)    
## (Intercept)          49.33341    0.58816  83.877  < 2e-16 ***
## GDP_growth_quarterly  0.15374    0.09177   1.675  0.09845 .  
## incumbent             2.09521    0.71428   2.933  0.00457 ** 
## juneapp               0.14966    0.01391  10.756 2.55e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.835 on 68 degrees of freedom
## Multiple R-squared:  0.7106,	Adjusted R-squared:  0.6978 
## F-statistic: 55.65 on 3 and 68 DF,  p-value: < 2.2e-16
```

``` r
# estimate for 2020
tfc_mod_2020 <- lm(pv2p ~ juneapp, 
                   data = subset(d_tfc_train, year < 2024))
summary(tfc_mod_2020)
```

```
## 
## Call:
## lm(formula = pv2p ~ juneapp, data = subset(d_tfc_train, year < 
##     2024))
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -6.0627 -2.0250 -0.1604  1.8038  6.9681 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 51.02572    0.34522  147.81   <2e-16 ***
## juneapp      0.16501    0.01351   12.22   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.961 on 74 degrees of freedom
## Multiple R-squared:  0.6685,	Adjusted R-squared:  0.664 
## F-statistic: 149.2 on 1 and 74 DF,  p-value: < 2.2e-16
```

