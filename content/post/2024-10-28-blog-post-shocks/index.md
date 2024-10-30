---
title: Blog Post -- Shocks, Models, and Possums?
author: Chris S
date: '2024-10-28'
slug: blog-post-shocks
categories: []
tags: []
---




*This blog is an ongoing assignment for Gov 1347: Election Analytics, a course at Harvard College taught by Professor [Ryan Enos](https://www.ryandenos.com/). It will be updated weekly and culminate in a predictive model of the 2024 presidential election.*

## Introduction -- What role do *shocks* play in election results? Do these *apolitical shocks* actually play a role, or do they simply make for humorous article titles?

In politics, experts often talk about the predictability. For example, a candidate's popularity might rise with a strong economy, or perhaps fall after a scandal. But what about those random, seemingly apolitical moments that, for whatever reason, capture the public's attention and steer a bit of the national conversation? From an unexpected celebrity endorsement to a viral meme, do these quirky shocks actually shift election results, or are they just great headlines?

Exploring these “shocks” feels a bit like chasing down the hidden influences on public opinion or perhaps they hold the secret ingredient that adds an unexpected twist to a familiar political recipe So, just how much do these factors play into elections, if at all? Or do they simply give us a chance to enjoy a laugh before getting back into nitty-gritty? 

Let's take a closer look.

## Iconic Election Forecasting

Before we get into shocks, let's examine a few legendary (and perhaps unusual) election forecasting models.

To kick things off, the [Iowa Electronic Markets](https://www.cambridge.org/core/services/aop-cambridge-core/content/view/B256F4EA39F7F9B6F0423BA4EDA46B4A/S1049096524000921a.pdf/iowa-electronic-markets-forecasting-the-2024-us-presidential-election.pdf) model used real-money prediction markets to predict the 2024 US Presidential election. Since its inception in 1988, the IEM has done surprisingly level, averaging an error rate of roughly 1.34 percentage points.

However, it is quite sensitive to political shifts (like the June 27th debate), with confidence intervals and prediction rates changing drastically with a new Democratic nominee as the market adapted to surprising information. It also occasional showed bimodal distributions, indicating scenarios with both potential Republican and Democratic wins -- uncommon, but not entirely unprecendented in the context of market forecasts. Altogether, we're seeing the model predict a slight Democratic edge in the popular vote, but maintains substantial uncertainty particularly as voter sentiment continues to shift.

Another iconic model is the [Economic Pessimism Model](https://www.cambridge.org/core/services/aop-cambridge-core/content/view/EE1A580C166E04F3D34C071AF413A3E1/S104909652400091Xa.pdf/the-challenge-of-forecasting-the-2024-presidential-and-house-elections-economic-pessimism-and-election-outcomes.pdf), which leverages public economic outlook as a basis for its election outcome predictions. This goes back to our very first post on the idea of fundamentals, like the economy, which have reflected consistent trends since the 1960s whereby econmic downturn have correlated with incumbent loss. For example, inflationary pressures in the 1980s and Jimmy Carter's loss while positive growth helped Reagan in 1984. 

The model currently predicts a small Republican win, but Lockerbie also recognizes that unforeseen events and circumstances as the election nears can dramatically change voter sentiment. With the race this close, even the smallest shift may be the difference between a win and a loss.

The final interesting model is the [PoSSUM Model](https://www.cambridge.org/core/services/aop-cambridge-core/content/view/89476D06B0055BD8B9E4557F09C9D10A/S1049096524000982a.pdf/the-2024-us-presidential-election-possum-poll.pdf). Instead of using state-wide polling or economic data, the authors analyzed users' digital traces on social media platforms like X (formerly Twitter) to compute their predictions. The addition of AI and large language models (LLMs) can help infer voting preferences and behavior via sentiment analysis without direct interviews or polling.

By assessing digital profiles, it assigns "speculation scores" to guage the confidence of inferences about voting preferences. Overall, the model's results mirror closely with traditional polls, suggesting that AI polling and sophisticated algorithms are as good, if not better, than traditional methods. 

Altogether, these somewhat unusual models provide a brief glimpse into the variety and creativity that political scientists and researchers are exploring when it comes to the game of predicting election outcomes.

## Apolitical Shocks

Before we dive into some final tweaking of our own model, let's examine the common debate over how sudden shocks affect voting behavior. This first began in [Achen and Bartels (2016)](https://muse-jhu-edu.ezp-prod1.hul.harvard.edu/book/64646) which explored the influence of shark attacks in New Jersey beach towns. Through their analysis, they uncovered that these towns often voted significantly less for the incumbent compared to non-beach towns that weren't facing this surprising shock. 

However, research by [Fowler and Hall (2018)](https://www-journals-uchicago-edu.ezp-prod1.hul.harvard.edu/doi/pdfplus/10.1086%2F699244) later discovered that by incorporating other counties as a whole dramatically reduced the significance of this unusual shark attack shock. What they found was that Ocean County, the county that Achen and Bartels studied, was an outlier and in reality, beach and non-beach towns voted rather similarly. 

These aren't the only examples of apolitical shocks and their influence. For instance, [Healy and Malhotra (2010)](https://www-nowpublishers-com.ezp-prod1.hul.harvard.edu/article/Details/QJPS-9057) found that whether or not there was a "disaster declaration" on certain shocking events created different impacts on voting behavior. They argued that such declarations often amplify incumbent support because it creates the impression of proactive response, regardless of the disaster's scale or actual effectiveness of relief efforts.

Together, we can see that while these apolitical shocks provide interesting insights to voting behavior and election outcomes, their effects feel too context-dependent and may not be generalizable across many states. Thus, I won't be including this in my final model.

## Random Forest Tweaks

Of all the models I've investigated thus far, the random forest's robustness and ability to do both regression and classification tasks make it my favorite and most compelling. In particular, I'll be using the ANES dataset that provides a rich collectino of variables related to voting behavior and demographics to justify this decision.



As we've seen, the model's ability to capture the 2016 election shows promising results:


```
## Confusion Matrix and Statistics
## 
##             Reference
## Prediction   Democrat Republican
##   Democrat        774        302
##   Republican      318        694
##                                          
##                Accuracy : 0.7031         
##                  95% CI : (0.683, 0.7226)
##     No Information Rate : 0.523          
##     P-Value [Acc > NIR] : <2e-16         
##                                          
##                   Kappa : 0.4053         
##                                          
##  Mcnemar's Test P-Value : 0.5469         
##                                          
##             Sensitivity : 0.7088         
##             Specificity : 0.6968         
##          Pos Pred Value : 0.7193         
##          Neg Pred Value : 0.6858         
##              Prevalence : 0.5230         
##          Detection Rate : 0.3707         
##    Detection Prevalence : 0.5153         
##       Balanced Accuracy : 0.7028         
##                                          
##        'Positive' Class : Democrat       
## 
```

Here, we can see that **strong accuracy of 0.703**, with a 95% confidence interval of 0.683 to 0.7226, suggesting a **fairly robust and confident estimate of the model's performance.** Compared to the simple random guessing (NIR) rate of 0.523, we e see that our model **performs significantly better**.

Reviewing again, its out-of-sample accuracy is just as good:


```
## Confusion Matrix and Statistics
## 
##             Reference
## Prediction   Democrat Republican
##   Democrat        187         70
##   Republican       85        179
##                                           
##                Accuracy : 0.7025          
##                  95% CI : (0.6612, 0.7415)
##     No Information Rate : 0.5221          
##     P-Value [Acc > NIR] : <2e-16          
##                                           
##                   Kappa : 0.4053          
##                                           
##  Mcnemar's Test P-Value : 0.2608          
##                                           
##             Sensitivity : 0.6875          
##             Specificity : 0.7189          
##          Pos Pred Value : 0.7276          
##          Neg Pred Value : 0.6780          
##              Prevalence : 0.5221          
##          Detection Rate : 0.3589          
##    Detection Prevalence : 0.4933          
##       Balanced Accuracy : 0.7032          
##                                           
##        'Positive' Class : Democrat        
## 
```

Thus, we can see how the random forest model maintains an accuracy of roughly 0.70, demonstrating how it can consistently capture the complex interactions between education, race, religion, etc.

Our previous prediction was:


```
## [1] 52.21456 48.67542
```

Thus, see that the random forest model predicts a reasonably **narrow gap in vote share,** with Harris leading by a slim margin. Now, we'll incorporate economic variables into our model to improve its accuracy and predictive ability.

Research from [Fair (2018)](https://fairmodel.econ.yale.edu/RAYFAIR/PDF/2018B.htm) and [Sides and Vavreck (2013)](https://hollis.harvard.edu/primo-explore/fulldisplay?docid=TN_cdi_jstor_books_j_ctt7ztpn1&context=PC&vid=HVD2&search_scope=everything&tab=everything&lang=en_US) have shown that the state of the economy has a non-negligible ability to mobilize and persuade voters. Thus, let's take a look at the model's prediction:


```
## Predicted Vote Shares for 2024 Election:
```

```
## Democratic candidate (Harris): 52.88 %
```

```
## Republican candidate (Trump): 47.72 %
```

Here, we can see how the estimates still imply a fairly narrow margin, highlighting the competitiveness of this presidential race. We also see a very small increase in predicted vote share for Harris, which makes sense as economic data like GDP, unemployment rate, CPI, and the S&P often favor the incumbent party when the economy is doing well.

To further cement our prediction, let's do a cross-validation to examine the model's precision:


```
## Random Forest 
## 
## 448 samples
##  28 predictor
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold) 
## Summary of sample sizes: 404, 404, 402, 403, 402, 404, ... 
## Resampling results:
## 
##   RMSE         Rsquared   MAE         
##   0.003043932  0.9999996  0.0009377337
## 
## Tuning parameter 'mtry' was held constant at a value of 9
## Tuning
##  parameter 'splitrule' was held constant at a value of variance
## 
## Tuning parameter 'min.node.size' was held constant at a value of 5
```

```
## [1] "Cross-validated RMSE: 0.00304393235350963"
```

```
## [1] "Cross-validated R-squared: 0.999999560925517"
```

Here, we can see an R-squared value of nearly 1, suggesting that the model has a near-perfect explanatory power over the training data. However, I'm also thinking about potential overfitting and whether the model is simply capturing the "noise" of the dataset. Then again, the random forest model has the ability to capture non-linear interactions, which may be well suited for the current data structure. My next steps will be to ensure its reliability by evaluating the model performance on an independent test set, and considering additional techniques like feature selection or reducing complexity if the precision drops significantly.

Altogether, this will make for an exciting model as we gear up for the final days before Nov 5th. Stay tuned for the final results next Monday!

## Data Sources:

Data are from the GOV 1347 course materials. All files can be found [here](https://github.com/cys9772/election-blog4). All external sources are hyperlinked.


```




