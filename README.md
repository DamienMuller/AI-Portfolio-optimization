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
| **Evaluation method** | For each algorithm, the predicted weights that result in the lowest MSE scores are kept. These weights are used to backtest what the performance and risk would have been if an investor had invested according to such monthly asset weights (2015 - 2021). <br> <br> The performance and risk metrics are compared amongst ML algorithms, but also against two mean-variance optimized portfolios. |
| **Mean-variance optimization**| Solve convex optimization problem to find *w'* which maximizes Sharpe ratio: <br> <br> $ max \frac{w'\mu - R_{f}} {\sqrt{w'\Sigma w}}$ <br> <br> *where: <br> w' = vector with asset weights <br> $\mu$ = vector with expected returns <br> $R_{f}$ = risk-free rate (here 0.02) <br> $\Sigma$ = Covariance matrix* <br> <br>  The above optimization outputs a *single optimized MPT portfolio:* Annualized mean returns and annualized covariance matrix between 2011 and 2014 used to define optimal weights. Corresponding peformance and risk metrics are assessed for 2015 to 2021. |

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

## Key Findings
---
<ul>
    <li> Regarding entire investment period (2015-2021), mean-variance optimized (MVO) portfolio did impressively well when it comes to optimizing return per unit of risk (Sharpe ratio). 
    <li> However, MVO portfolio was, on average, only invested in 4 out of 10 positions. All AI optimizers were invested in the 10 positions, which allows for greater risk diversification.
    <li> All AI optimizers outperformed MVO portfolio when it comes to total return. However, the outperformance comes at the price of increased risk/volatility. 
    <li> During a period of increased volatility (2020), the decision tree algorithm outperformed all other optimizers in terms of total return and return per unit of risk.




