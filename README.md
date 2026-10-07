# Portfolio-Optimization-with-real-constraints


A Python-based framework for professional-grade portfolio allocation and optimization. This project implements constrained optimization models, covariance matrix regularization techniques to reduce estimation noise, market perturbation stability analysis, and shadow cost evaluation for portfolio constraints.

---

## Key Features

* **Constrained Portfolio Optimization:** Solves convex Markowitz portfolio problems (Mean-Variance, Max Sharpe Ratio, Minimum Variance) with real-world operational constraints using `CVXPY`.
* **Covariance Matrix Regularization:** Applies Tikhonov regularization (L2 Shrinkage) to stabilize ill-conditioned sample covariance matrices and remove historical noise.
* **Stability & Perturbation Analysis:** Evaluates portfolio weight robustness against stochastic fluctuations in input parameters ($\mu$ and $\Sigma$).
* **Shadow Cost Analysis:** Analyzes the economic impact of allocation constraints by evaluating the trade-offs between constraints and portfolio performance.

---

## Theoretical Framework & Methodology

### 1. Covariance Matrix Regularization (Tikhonov / L2 Shrinkage)
The empirical sample covariance matrix $\Sigma_{sample}$ often suffers from estimation noise, particularly when the number of assets is large relative to the historical time horizon. To improve the conditioning of the matrix, Tikhonov regularization is applied:

$$\Sigma_{reg} = \Sigma_{sample} + \gamma I$$

Where:
* $\gamma \ge 0$ is the regularization parameter.
* $I$ is the identity matrix ($N \times N$).

*Benefit:* Reduces the matrix condition number, prevents extreme or unstable positions, and enhances out-of-sample weight stability.

---

### 2. Optimization Problem Formulation

Asset allocation is structured as a Quadratic Programming (QP) problem:

$$\begin{aligned} \min_{w} \quad & \frac{1}{2} w^T \Sigma_{reg} w - \lambda \mu^T w \\ \text{subject to} \quad & \sum_{i=1}^{N} w_i = 1 \quad \text{(Full Investment)} \\ & w_{min} \le w_i \le w_{max} \quad \text{(Position Limits / Long-Only)} \\ & A_{sector} w \le b_{settore} \quad \text{(Sector Exposure Constraints)} \end{aligned}$$

Where $w$ represents the asset weight vector, $\Sigma_{reg}$ the regularized covariance matrix, and $\mu$ the expected return vector.

---

### 3. Stability & Perturbation Analysis

To assess portfolio sensitivity to market data uncertainty, Gaussian noise is injected into returns ($\mu$) and the covariance matrix ($\Sigma$):

$$\mu_{perturbed} \sim \mathcal{N}(\mu, \eta_\mu \cdot \text{diag}(\Sigma))$$
$$\Sigma_{perturbed} = \Sigma + E, \quad E \sim \mathcal{N}(0, \eta_\Sigma)$$

Allocation stability is measured by tracking weight dispersion via L1/L2 norm:

$$\text{Dispersion} = \mathbb{E} \left[ \Vert{} w^* - w^*_{perturbed} \Vert{}_2 \right]$$

---

### 4. Constraint Shadow Costs

The framework evaluates the marginal cost of imposed constraints (e.g., concentration limits or risk thresholds). By gradually tweaking constraint bounds $b$, it quantifies the sacrifice in return or Sharpe ratio per unit of constraint tightened:

$$\text{Shadow Cost} = \frac{\partial f^*(b)}{\partial b}$$

---

## Project Structure

```text
├── data/                  # Historical data downloads and cache
├── notebooks/             # Exploratory notebooks and graphic reports
├── src/
│   ├── data_loader.py     # YFinance fetching and log returns calculation
│   ├── covariance.py      # Covariance estimation and Tikhonov regularization
│   ├── optimizer.py       # Optimization engine with CVXPY and constraint management
│   └── stability.py       # Module for Monte Carlo stability analysis and Shadow Costs
├── main.py                # Main execution pipeline
├── requirements.txt       # Project dependencies
└── README.md
