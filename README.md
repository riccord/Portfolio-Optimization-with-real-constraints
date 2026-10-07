# Portfolio Optimization Analysis

This repository implements a quantitative portfolio optimization framework in Python using CVXPY and yfinance. Beginning with Markowitz Modern Portfolio Theory (Mean-Variance Framework), the project extends into constrained optimization models incorporate sector limits, turnover penalties, single-asset maximum weights, and covariance matrix regularization.

---

## Overview and Objectives

The objective is to construct optimal asset allocations from SPDR Sector ETFs using historical market data. The project addresses real-world portfolio management constraints and estimation risk through regularization and explicit trading constraints.

Key capabilities included in the implementation:
* Historical data retrieval and log-return normalization via yfinance.
* Tikhonov regularization ($\Sigma_{\text{reg}} = \Sigma + \varepsilon I$) to reduce the covariance matrix condition number and improve numerical stability.
* Convex optimization solvers built on CVXPY.
* Incorporation of practical constraints: weight bounds ($w_{\text{max}}$), sector exposure limits, and turnover restrictions.

---

## Optimization Models and Constraints

### Core Formulations

1. Global Minimum Variance (GMV):
   Minimize portfolio variance $w^T \Sigma_{\text{reg}} w$ subject to budget and long-only constraints.

2. Target Return Optimization:
   Minimize portfolio variance $w^T \Sigma_{\text{reg}} w$ subject to achieving a minimum expected portfolio return $\mu_{\text{target}}$.

3. Mean-Variance Utility Maximization:
   Maximize $w^T \mu - \lambda w^T \Sigma_{\text{reg}} w$, balancing return and risk according to the risk-aversion parameter $\lambda$.

---

### Implemented Portfolio Constraints

To ensure realistic and actionable portfolio allocations, the optimization functions support the following explicit constraints:

* Budget and Long-Only Constraint:
  $\sum w_i = 1 \quad \text{and} \quad w_i \ge 0 \quad \forall i$

* Maximum Individual Asset Weight ($w_{\text{max}}$):
  $w_i \le w_{\text{max}} \quad \forall i$
  Prevents over-concentration in single asset classes or equities.

* Sector Exposure Constraints:
  $\sum_{i \in \text{Sector}_k} w_i \le S_k$
  Restricts aggregate exposure to specific sectors (e.g., Technology, Financials, Energy) to maintain diversification across macroeconomic factors.

* Turnover Limits:
  $| w - w_{\text{initial}} | \leq T_{\text{max}}$
  Limits the total absolute rebalancing change relative to a current baseline portfolio $w_{\text{initial}}$, controlling transaction costs and market impact.

---


## Requirements and Installation

Install the required Python packages prior to running the notebook:

```bash
pip install pandas numpy matplotlib yfinance cvxpy
