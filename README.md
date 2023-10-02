# A comparative analysis between mean-variance portfolio optimization and AI-empowered portfolio optimization

## Project Objective
---
This project proposes an alternative framework to the classical Markowitz modern portfolio theory (MPT). The objective is to use a predictive approach to portfolio optimization by mapping the relationship between multiple input features (at time t) and the optimal asset weights for the following month (time t+1) with tree-based AI algorithms.  

## Rationale
---
Although still widely applied, MPT has several shortcomings, e.g.:  
- *Backward-looking approach*: MPT involves solving a quadratic optimization problem that takes as inputs the sample mean returns and the sample covariance matrix. Thus, MPT inevitably relies on the disputed assumption that past return patterns persist in the future. In contrast, AI algorithms learn the relationship between current inputs and future outputs, thus applying a forward-looking approach. 

- *Input sensitivity*: A small change in input variables (mean returns + covariance matrix) results in a significant change in optimal portfolio allocation. These unstable weights demand a high amount of portfolio rebalancing, which in turn negatively effects portfolio returns.  

- *Risk estimation*: MPT uses variance to estimate risk. Therefore, both positive and negative deviations from mean returns are taken into account. However, it is argued that only negative deviations should be considered, as positive deflections are desirable for investors. Other metrics, such as value-at-risk, only focus on downside risk and, hence, potentially constitute a more accurate risk measure.  

## Problem statement
---
Are machine learning algorithms better suited to optimize investment portfolios than mean-variance analysis, while still being transparent enough to be understood by regulators and clients?  

## Project overview
---

#### Methodology


| | |
|-|-|
| **Predictive problem** | Multi-target regression problem |
| **Data** | Daily prices for 5 equity ETFs and 5 bond ETFs from December 2010 to July 2021. Data is publicly available and was imported from Yahoo Finance. 
| **Y-matrix** | Ex-post monthly asset weights for 10 assets that allow to optimize Sharpe ratio of portfolio. Weights are found by generating each month 1000 random portfolios and by selecting the weights that maximize Sharpe ratio. 
| **X-matrix** | For each asset: <br> 2 technical indicators <br> 3 expected return estimators <br> 2 risk estimators <br> <br> All features are derived from daily price data of the month preceding the prices used to define optimal Sharpe ratio weights.
| **Algorithms** | Decision Tree <br> Random Forest <br> Gradient Boosting |
| **Learning process** | *Walk-forward validation* <br> <br> 1) Initially train models with 3 years of monthly data for training (e.g. 2011-2014) + 1 year of monthly data for testing (e.g. 2015) <br> <br> 2) Re-train models by gradually expanding training set by 1 year (e.g. 2011-2015) and use following year for testing (e.g. 2016) <br> <br> 3) Continue until last testing set covers final optimal weights (here June 2021)|
| **Tuning** | *Hyperparameter Tuning* <br> <br> To avoid overfitting, each algorithm is trained on a defined set of parameters. The parameters that result in the best testing score (MSE) are chosen. This process is repeated for each training and testing sets in the walk-forward validation. |
| **Evaluation method** | For each algorithm, the predicted weights that result in the lowest MSE scores are kept. These weights are used to backtest what the performance and risk would have been if an investor had invested according to such monthly asset weights (2015 - 2021). <br> <br> The performance and risk metrics are compared between ML algorithms, but also against the mean-variance optimized portfolio. |
| **Mean-variance optimization**| Solve convex optimization problem to find *w'* which maximizes Sharpe ratio: <br> <br> $max \frac{w'\mu - R_{f}} {\sqrt{w'\Sigma w}}$ <br> <br> where: <br> w' = vector with asset weights <br> $\mu$ = vector with expected returns <br> $R_{f}$ = risk-free rate (here 0.02) <br> $\Sigma$ = Covariance matrix <br> <br> 1) Similar as for the ML learning process, initial optimal weights for the first month are found by inputing monthly returns between January 2011 and December 2014 in the convex optimization problem. <br><br> 2) Gradually expand input window by one month, e.g. January 2011 - January 2015 returns to get February 2015 weights. This allows to approximate the walk-forward approach used to train the AI models. <br><br> 3) Use period from January 2015 - June 2021 to measure performance and risk metrics.|  

#### Output variables

| ETF Ticker | Description   |
|-|:-|
|   VTI  | US total stock market|
| IUSV | US large and mid cap stocks |
| VBR | US small cap stocks |
| VEA | International developed market stocks |
| VWO | Emerging markets stocks |
| SHV | US Treasury bonds |
| STIP | US inflation-linked bonds |
| AGG | High-quality US bonds |
| IBND | International corporate bonds |
| EMB | Emerging markets bonds |

#### Features 
| | |
|-|-|
| **Technical indicators** | *Exponential moving average return (EWMA):* <br> <br> Use past 30 daily returns to get average monthly return while assigning more weight to most recent returns <br> <br> *Relative Strength Index (RSI):* <br> <br> Use past 30 daily returns to get relative strength ranging from 0 to 100: Value over 70 means ETF is overbought (and should be sold) and value below 30 indicates ETF is oversold (and should be bought) |
| **Return estimators** | *CAPM returns:* <br><br> Regress monthly ETF returns against monthly market factor (Rm - Rf) to evaluate beta for each ETF. The market factor and the risk-free rate (Rf) are imported from the Keneth R. French library. The monthly CAPM returns are then calculated as: $\mbox{ETF CAPM return} = Rf + \beta \times (Rm - Rf)$ <br><br> *Fama/French 3-factor model:* <br><br> Regress monthly ETF returns against market factor, small-minus-big factor (SMB) and high-minus-low book-to-market factor (HML). The SMB factor accounts for the tendency of small cap firms to outperform large cap firms. The HML factor accounts for the tendency of high value stocks to outperform growth stocks. The Fama French 3-factor returns are then calculated as: $\mbox{ETF FF return} = Rf + \beta_{1}(Rm - Rf) + \beta_{2}(SMB) + \beta_{3}(HML) + \epsilon$  <br><br> *Trimmed mean:* Alternative estimator to sample mean returns that is less variable. Takes the average of daily returns for each month by ignoring the 5% highest and 5% lowest daily returns.|
| **Risk estimators** | *Parametric Value-at-risk:* <br><br> For each month and for each ETF, the parametric 95% VaR is calculated using the daily returns of the respective month. It is assumed that daily returns are normally distributed. The resulting VaR indicates the daily loss that will not be exceeded at a 95% confidence level. <br><br> *Conditional Value-at-risk:* <br><br> Similar as for VaR, the parametric CVaR is measured for each ETF and each month. It is based on daily returns and also assumes the normal distribution of returns. The 95% CVaR gives the expected daily loss in case the 95% VaR is exceeded. |

## Results 
---
#### Cumulative returns 

![Cum_return_comparison](https://github.com/DamienMuller/AI-Portfolio-optimization/assets/86874530/df3ad524-4d2a-4e8f-8cfc-dfbd1b488694)

<ul>
    <li> Between mid-2015 to mid-2016 and over the 2nd quarter of 2020 were the only periods when MVO portfolio had a higher total return than AI portfolios. </li>
    <li> Between late 2016 and late 2018 and since late 2020 performance gap between AI algorithms and MVO optimizer was quite significant. </li> 
    <li> It can be observed that MVO has had a more stable increase in return over time while AI optimizers had more ups and downs. </li>
</ul>
<br>

#### Performance and risk table 
<img width="960" alt="Screenshot 2023-10-02 at 16 30 14" src="https://github.com/DamienMuller/AI-Portfolio-optimization/assets/86874530/32a9ccf4-e86d-49fc-8ed3-c430b5f6f337">
<br>
<br>
<br>
<ul>
    <li> As indicated by the above plot, both decision tree and random forest optimizers provide the highest total returns. </li>
    <li> The higher returns come at the price of increased risk, as both algorithms have significantly higher annualized volatilities than MVO. </li>
    <li> MVO provides the highest return per unit of risk. Gradient boosting performed worst amongst all optimizers. </li>
    <li> Hyperparameter tuning had negative effects on both decision tree and random forest algorithms. </li>
</ul>
<br>

#### Asset allocation
![Average_allocation](https://github.com/DamienMuller/AI-Portfolio-optimization/assets/86874530/ce78cb56-e73c-4dc1-ab85-a2aff8c0b695)
<br><br>
<ul>
    <li> MVO portfolio is, on average, more than 70% invested in bond ETFs. It holds by far the highest share in bond ETFs. </li>
    <li> The more conservative investment of MVO portfolio explains the lower volatility, but also the lower total return. </li>
    <li> All AI portfolios have a similar asset allocation. They are slightly more invested in stock ETFs than in bond ETFs. </li>
    <li> Random Forest portfolio holds, on average, the highest share of stock ETFs. </li>
</ul>

<br>
<br>

## Key Findings
---
<ul>
    <li> Regarding entire investment period (2015-2021), mean-variance optimized (MVO) portfolio did impressively well when it comes to optimizing return per unit of risk (Sharpe ratio). 
    <li> However, MVO portfolio was, on average, only invested in 4 out of 10 positions. All AI optimizers were invested in the 10 positions, which allows for greater risk diversification.
    <li> All AI optimizers outperformed MVO portfolio when it comes to total return. However, the outperformance comes at the price of increased risk/volatility. 
    <li> During a period of increased volatility (2020), the decision tree algorithm outperformed all other optimizers in terms of total return and return per unit of risk.




