# Portfolio-Optimization-with-real-constraints


# Portfolio Optimization Analysis

Questo progetto implementa un sistema completo di ottimizzazione di portafoglio finanziario in Python utilizzando **CVXPY** e **yfinance**. Partendo dal classico modello di Markowitz (Mean-Variance), il notebook sviluppa varianti avanzate che includono vincoli di rendimento target, parametro di avversione al rischio ($\lambda$) e regolarizzazione della matrice di covarianza.

---

## 📋 Indice dei Contenuti
- [Asset e Dati Utilizzati](#-asset-e-dati-utilizzati)
- [Metodologia e Modelli](#-metodologia-e-modelli)
  - [1. Fondamenti Matematici](#1-fondamenti-matematici)
  - [2. Regolarizzazione della Matrice di Covarianza (Tikhonov)](#2-regolarizzazione-della-matrice-di-covarianza-tikhonov)
  - [3. Strategie di Ottimizzazione](#3-strategie-di-ottimizzazione)
- [Risultati dell'Analisi](#-risultati-dellanalisi)
- [Requisiti e Installazione](#-requisiti-e-installazione)

---

## 📈 Asset e Dati Utilizzati

L'analisi utilizza **5 anni di dati storici giornalieri** estratti tramite Yahoo Finance (`yfinance`) relativi a **10 ETF settoriali SPDR**:

| Ticker | Settore | Rendimento Atteso Ann. (%) | Volatilità Ann. (%) | Sharpe Ratio |
| :--- | :--- | :---: | :---: | :---: |
| **XLK** | Technology | 6.086% | 19.084% | 0.319 |
| **XLF** | Financials | 19.895% | 25.539% | 0.779 |
| **XLV** | Healthcare | 8.272% | 18.318% | 0.452 |
| **XLY** | Consumer Disc | 12.186% | 17.588% | 0.693 |
| **XLP** | Consumer Staples | 20.425% | 25.896% | 0.789 |
| **XLE** | Energy | 5.786% | 13.714% | 0.422 |
| **XLI** | Industrials | 1.768% | 19.150% | 0.092 |
| **XLB** | Materials | 7.850% | 17.441% | 0.450 |
| **XLU** | Utilities | 7.168% | 15.173% | 0.472 |
| **XLRE**| Real Estate | 4.902% | 24.129% | 0.203 |

> *Nota: I rendimenti giornalieri sono calcolati come log-returns e annualizzati con un fattore pari a 252.*

---

## 📐 Metodologia e Modelli

### 1. Fondamenti Matematici
Dato un vettore dei rendimenti attesi $\mu \in \mathbb{R}^n$ e una matrice di covarianza $\Sigma$:

* **Minima Varianza:**
  $$\min_w w^T \Sigma w \quad \text{s.t.} \quad \mathbf{1}^T w = 1, \quad w \ge 0$$

* **Target Return:**
  $$\min_w w^T \Sigma w \quad \text{s.t.} \quad \mathbf{1}^T w = 1, \quad w \ge 0, \quad w^T \mu \ge \mu_{\text{target}}$$

* **Avversione al Rischio ($\lambda$):**
  $$\max_w \left( \mu^T w - \lambda \cdot w^T \Sigma w \right) \quad \text{s.t.} \quad \mathbf{1}^T w = 1, \quad w \ge 0$$

---

### 2. Regolarizzazione della Matrice di Covarianza (Tikhonov)
Per migliorare la stabilità numerica dell'ottimizzazione e ridurre il numero di condizionamento, viene applicata la regolarizzazione di Tikhonov:
$$\Sigma_{\text{reg}} = \Sigma + \varepsilon I$$

Nel notebook viene analizzato l'impatto di vari livelli di $\varepsilon$ ($0 \le \varepsilon \le 0.01$). Per tutte le successive ottimizzazioni è stato selezionato **$\varepsilon = 0.0089$**, riducendo il *condition number* della matrice da **48.22** a **17.30**.

---

### 3. Strategie di Ottimizzazione

All'interno della classe Python `PortfolioOptimization`, sono stati implementati tre risolutori convessi tramite **CVXPY**:

1. `solve_min_variance()`: Calcola il portafoglio a minima varianza globale.
2. `solve_target_return(target_return)`: Trova i pesi ottimali vincolati a un rendimento minimo target.
3. `solve_risk_aversion(risk_aversion)`: Massimizza la funzione di utilità quadratica in base al parametro $\lambda$.

---

## 📊 Risultati dell'Analisi

I tre portafogli generati nel notebook mostrano la seguente allocazione dei pesi:

| Asset / Settore | Min Variance (%) | Target Return (12%) (%) | Risk Aversion ($\lambda=1.5$) (%) |
| :--- | :---: | :---: | :---: |
| **Technology** | 0.00% | 0.00% | 0.00% |
| **Financials** | 9.82% | 19.89% | 49.28% |
| **Healthcare** | 4.10% | 0.00% | 0.00% |
| **Consumer Disc** | 6.88% | 5.33% | 0.00% |
| **Consumer Staples** | 5.90% | 17.47% | 50.72% |
| **Energy** | 34.93% | 27.07% | 0.00% |
| **Industrials** | 0.00% | 0.00% | 0.00% |
| **Materials** | 15.74% | 13.43% | 0.00% |
| **Utilities** | 22.64% | 16.81% | 0.00% |
| **Real Estate** | 0.00% | 0.00% | 0.00% |
| **Rendimento Atteso** | — | — | **20.16%** |
| **Volatilità Totale** | **13.14%** | **13.77%** | **21.33%** |

---

## 🛠 Requisiti e Installazione

Per eseguire il notebook è necessario installare i seguenti pacchetti Python:

```bash
pip install pandas numpy matplotlib yfinance cvxpy
