# Derivatives Pricing Lab

**Volatility modelling · Numerical option pricing · Monte Carlo variance reduction**

A Python-based quantitative finance project investigating how volatility assumptions, numerical methods, and simulation design affect derivative valuations. The case study uses **Palantir Technologies (NASDAQ: PLTR)** and an option-market snapshot dated **9 October 2026**.

> **[Read the full research report (PDF)](Conclusion.pdf)**

## Project at a glance

| Item | Specification |
| --- | --- |
| Underlying | PLTR |
| Valuation date | 9 October 2026 |
| Benchmark option | 15 January 2027 call, strike $210 |
| Spot price | $209.05 |
| Market bid / ask | $22.00 / $22.35 |
| Mid-quote implied volatility | 49.80% |
| Black–Scholes benchmark | **$22.1750** |

The project compares historical and option-implied volatility, independently implemented numerical pricing methods, and a risk-neutral GARCH scenario. The full report also investigates path dependence and variance reduction for arithmetic-average Asian options.

## Research highlights

### 1. Volatility: forecasting versus pricing

- Estimated **constant-volatility GBM**, **EWMA**, and **GARCH(1,1)** using historical PLTR returns.
- Evaluated one-step-ahead variance forecasts on a chronological holdout sample.
- Examined implied-volatility smiles, the ATM term structure, and forward variance across four expiries.
- Found that GARCH achieved the lowest validation MSE, but improved on constant volatility by only **0.22%**. Its physical-measure volatility forecast should not be substituted directly for risk-neutral implied volatility in option pricing.

### 2. Independent pricing engines and model risk

Implemented and compared:

- **Black–Scholes:** analytical benchmark and Greeks.
- **Monte Carlo:** exact GBM terminal sampling; common-random-number and pathwise delta estimators.
- **Binomial trees:** Cox–Ross–Rubinstein and Jarrow–Rudd, with convergence diagnostics.
- **Finite differences:** explicit, fully implicit, and Crank–Nicolson schemes for the transformed Black–Scholes PDE; custom tridiagonal LU solver; stability, spatial, temporal, and boundary-truncation studies.
- **Risk-neutral GARCH scenario:** simulated stochastic conditional variance, with a GBM control variate and initial-variance sensitivity analysis.

**Selected results from the report:**

| Pricing method | Reported call price | Interpretation |
| --- | ---: | --- |
| Black–Scholes | $22.175000 | Common constant-volatility benchmark |
| Crude Monte Carlo (262,144 paths) | $22.148523 | Sampling uncertainty; 95% CI covers BS |
| CRR tree (800 steps) | $22.178229 | Small discretisation difference |
| Jarrow–Rudd tree (800 steps) | $22.180942 | Small discretisation difference |
| Explicit FDM (M=400, N=3330) | $22.175123 | Stable grid, close to BS |
| Fully implicit FDM (M=800, N=3200) | $22.174099 | Stable but first-order time discretisation |
| Crank–Nicolson FDM (M=800, N=3200) | $22.174904 | Refined finite-difference result |
| Risk-neutral GARCH scenario (200,000 paths) | $22.617856 | Different model assumptions, not a numerical discrepancy |

**Key insight:** Agreement between numerical engines validates their implementation of a *shared model*; it does not eliminate volatility-input uncertainty or model risk.

### 3. Asian options and variance reduction

The full report also studies arithmetic-average Asian call options under GBM, comparing correct Brownian paths with incorrectly sampled independent marginal prices.

It evaluates five estimators under an equal normal-draw budget: crude Monte Carlo, antithetic variates, a geometric-Asian control variate with fixed coefficient, a pilot-estimated control variate, and antithetic sampling combined with a pilot-estimated control variate.

For the at-the-money Asian call, the report finds a **time-adjusted relative efficiency of approximately 614×** for the combined antithetic and pilot-control-variate estimator versus crude Monte Carlo (point estimate; uncertainty intervals are in the report).

> **Code availability:** The Q1/Q2 notebooks are included in this repository. The Q3 analysis is documented in the PDF; its separate simulation source is not yet included in the current repository snapshot.

## Repository structure

```text
Derivatives-Pricing-Lab/
├── README.md
├── Conclusion.pdf
├── .gitignore
├── Notebooks/
│   ├── 01_data_audit.ipynb
│   ├── 02_option_audit.ipynb
│   ├── 03_Historical_volatility.ipynb
│   ├── 04_Implied_volatility.ipynb
│   ├── 05_BS_benchmark.ipynb
│   ├── 06_Monte_Carlo.ipynb
│   ├── 07_Binomial_Trees.ipynb
│   ├── 08_Finite_Difference.ipynb
│   ├── 09_risk_neutral_GARCH_scenario.ipynb
│   └── 10_Reconciliation_Model_Risk.ipynb
├── Data/           # Local input data; check availability before reproducing
├── figures/
├── Src/
└── tests/
```

The notebooks cover the data audit, volatility estimation, numerical pricing, GARCH scenario analysis, and reconciliation. The report describes a broader automated reproduction workflow; **that workflow is not claimed to be fully available in this repository snapshot**.

## Getting started

1. Clone the repository and open the folder in VS Code or JupyterLab.
2. Set up a Python environment with the dependencies used by the notebooks (including NumPy, pandas, SciPy, Matplotlib, and `arch` for GARCH estimation).
3. Confirm the required archived input CSV files are present in `Data/` and that notebook data paths resolve correctly.
4. Run notebooks in numbered order, checking outputs against the benchmarks and diagnostics in the PDF.

A pinned environment file and end-to-end reproduction script may be added in a future update. Runtime results in the report were measured in a separate environment and may differ by machine.

## Research report and attribution

The full **FN6808 Derivative Securities course project report** contains methodology, derivations, diagnostics, additional figures, Asian-option results, references, and reproducibility details.

**[View the complete report](Conclusion.pdf)**

## Disclaimer

This project is for academic research and educational purposes only. The numerical values are tied to the documented historical market snapshot and assumptions, not live quotes or investment recommendations.
