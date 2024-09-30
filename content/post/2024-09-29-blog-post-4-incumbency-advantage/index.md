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

Humans are predictable creatures. For example, if you choose a seat on the first day of lecture, you'll likely choose the same spot next week. And the week after that -- probably until the last day of class. Is this the same for voting behavior? Regarding elections, incumbents seem to hold a similar seat-saving advantage. Voters often stick with the familiar, and the incumbency advantage becomes a powerful force in shaping election outcomes. But is this advantage truly as reliable as picking the same lecture seat, or is it more complicated? 

Moreover, when existing presidents do a poor job of handling national concerns such as social upheaval or economic downturn, they leave a sour taste in the public's memory. Perhaps this tarnished reputation lingers, making voters more inclined to seek change rather than continuity -- just as how a coffee spill might make you choose a new lecture seat...

As usual, to analyze these questions, we'll start with the data. So, let's dive in!

## War and Incumbency

War is an unbelievably challenging time for most countries. In these pivotal moments, we look towards our leaders to navigate this environment with strength, decisiveness, and humility. Therefore, taking data between 1948 and 2024, I've produced the following summary for incumbency re-election:


|reelect_president |  N| Percent|
|:-----------------|--:|-------:|
|FALSE             | 12|   66.67|
|TRUE              |  6|   33.33|

We can immediately notice something -- in 12/18 post-war elections, the incumbent president did not win re-election, meaning that voters opted for change rather than continuing with the same president. Historically, the incumbent advantage seems to be weaker than what's often assumed, which may be due to several factors. For example, many of these incumbents faced significant fundamental economic crises, including Carter's era of inflation and Bush's period of recession. In doing so, voters may often seek fresh leadership if the current administration fails to address these national concerns effectively in the public's eye -- also known as blame attribution [(Achen and Bartels, 2016)](https://hollis.harvard.edu/primo-explore/fulldisplay?docid=01HVD_ALMA512397392810003941&vid=HVD2&search_scope=everything&tab=everything&lang=en_US&context=L)

Of course, we also have to consider potential outliers -- such as Kennedy's close victory in 1960 or the Watergate scandal that impacted Ford's chances in 1976 -- that may have contributed to a surprisingly low re-election rate.

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

There are also economic and political components in play, such as in 1980 when the Democrats controlled the presidency but Carter's disastrous handling of inflation and the Iran Hostage Crisis led to a republican victory by Reagan. Similarly, Trump's failed leadership during the COVID-19 pandemic further led to a loss in party confidence by voters, leading to an eventual transition to Biden and the democrats.

Overall, the near-even split between party retention and loss highlights the competitive and unpredictable nature of presidential elections, making our forecasting job more challenging and exciting!

## I'm In Power -- Now What?

The executive branch has a lot of power -- namely, the "power of proposal". In this case, we're talking about the budget. However, the amount that presidents allocate to who and why varies depending on state and election factors. Let's take a closer look:

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-7-1.png" width="672" />

The phenomenon we're seeing here is known as "pork barrel" politics. No, not the meat. This practice refers to how politicians direct government funds to target specific states or districts that are critical to an election cycle. In essence, they can please local voters and gather support, generating the ubiquitous phrase "bringing home the bacon".

For our visualization, we can observe that swing states in election years receive the highest average federal grand spending, approaching nearly $150 million. Strategically, this makes sense when considering trends in political behavior and competitiveness. These states are the most competitive and critical for determining the outcome of elections, so presidents and parties on both sides funnel additional money and resources to win over undecided voters. We see this as an example of **electoral influence**. By directing more federal resources to swing states, incumbents can attempt to sway public opinion and rally support.

Comparatively, core states receive less attention overall, regardless of whether it's an election or non-election year. These states are considered "safe", meaning that candidates don't have to provide the same level of financial investment to secure electoral support.

What's interesting is that even in non-election years, swing states receive a significant amount of funding, likely so that incumbents can maintain a steady presence in these key battleground areas. In doing so, they can build a strong reputation for future election cycles, increasing their influence over time.

Digging deeper, let's look at the difference between an incumbent re-election year and a successor election year:

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-8-1.png" width="672" />

Here, we can see that federal grant spending in swing states during a successor election year is considerably lower, at around $120 million, compared to 150 during an incumbent re-election year. The greater spending under incumbent years demonstrates that sitting presidents are more likely to direct resources to these critical areas, increasing their chances of re-election and leveraging their executive power to influence voters. Historically, we see this in the 2012 election, when Obama's administration focused on delivering resources to key states such as Ohio and Florida to help secure a second term.

For successor election years, the absence of an incumbent means that there is both less direct control over the distribution of resources and less urgency since the current president is not on the ballot. Of course, there are other factors, including a strategic shift in priorities within a party, such as candidate outreach or messaging, or simply a less desire to continue the same level of funding as during an incumbent re-election year. 

Altogether, the data remains consistent from both visualizations with the idea that swing states receive more attention and funding in critical election years, especially when an incumbent is involved.

## Pork and Vegetables -- Adding Additional Factors

To analyze our results further, let's examine the relationship between federal grant spending and changes in vote swing: 


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

From our coefficient estimates, we can see that holding other factors constant, increases in federal grant spending are associated with increases in vote swing toward the incumbent candidate. Thus, we're seeing confirmation that increased federal money allocation to a county seems to positively influence voter behavior toward supporting the incumbent.

We can also see a positive coefficient term for competitive states, in which the effect of federal grant spending on vote swings seems to be even stronger. This supports our previous discussion of how competitive states are more heavily targeted with spending, and can therefore effectively shift voters in favor of the incumbent.

While 2000 seemed to be an unusual year with odd voting patterns, all of the other election years showed how federal spending had a positive impact, especially with 1996 and 2004 being considerably effective.

Let's zoom in closer and see the effects on counties across different states during election cycles:


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

Compared to our previous model, we're now combining additional variables, including advertising, campaign, war, house vote, and population change. The key takeaway here is that changes in income, campaign efforts, and local political support significantly affect voting behavior, reinforcing how both economic performance and campaign intensity can play a critical role in securing votes. Of course, these results are not bulletproof as poor foreign policy decisions during the Iraq War by the incumbent can cause broad dissatisfaction among voters and shift support the other way.

However, when we look at state comparisons, these results aren't as conclusive:


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

Here we can see that while federal grant spending shows a positive relationship with vote share changes, a high p-value indicates that it does not have statistical significance and pork barrel spending may not be as strong as expected in influencing vote outcomes. In essence, we're seeing how simply throwing money at swing states may not automatically lead to an increase in vote shares for the incumbent, without considering other fundamental political or social factors!

## Time For Change

These relationships have been studied extensively throughout history. Now we'll take a look at Alan Abramowitz's Time for Change Model, a classic benchmark model of incumbency between 1992 and 2016. Impressively, it had a true out-of-sample PV prediction error of 1.7%:


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

Most notably, we see here that the coefficient for incumbency is positive and highly significant, indicating a 2.1 percentage-point increase in vote share. Thus we can see that trends of greater name recognition and established campaign infrastructure help sitting presidents secure this incumbency advantage. Further, June approval ratings show that the voter perception of the incumbent's performance can be a powerful predictor of electoral success and something to seriously consider when creating our own models.

Ultimately, we see the multiple R-squared value of 0.7106 which is relatively high, suggesting that the model captures a large portion of what influences vote share, with the roughly 29% remaining variance being left unexplained by other factors. This is so far the highest R-squared value we've seen in our models which could hint towards a good direction.

Could we reduce our model to something even more simple?


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

Here we see that the June approval ratings itself is a strong indicator of electoral success and are statistically significant. Further, even dropping the incumbency factor doesn't reduce the model's fit by much as its R-squared value is still roughly 0.67 which is considerably high. Broadly speaking, this could suggest that simple models may even perform as well or better than complex ones.

## Time for Change vs Polls?

To further extend our understanding, let's examine differences between Abramowitz's Time for Change Model and our previous model created in [Blog Post 3](https://cys9772.github.io/election-blog4/post/2024/09/22/blog-post-3-polling/) using November polling averages:


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

Most notably, we see how Abramowitz's model accounts for a more comprehensive assessment by combining voter sentiment in June with economic and historical trends, which may not necessarily be reflected in polling ratings. Thus, we observe a slightly higher R-squared value for Abramowitz's model, while ols.nov.2 still performs reasonably well. 
From this, we can anticipate that for 2024, the Time for Change Model would suggest a decline in vote share for the incumbent party, penalizing them for voter fatigue. Slow economic growth in later quarters will also further hurt the incumbent's chances. Comparatively, the polling model will predict the winner regardless of how the economy is performing or whether voters experience certain fatigue.

Either way, as mentioned before, both models perform reasonably well under changing election dynamics, and our preferred model may become a combination of both. Or, maybe we'll stick with something simpler...stay tuned!

## Data Sources:

Data are from the GOV 1347 course materials. All files can be found [here](https://github.com/cys9772/election-blog4). All external sources are hyperlinked.
