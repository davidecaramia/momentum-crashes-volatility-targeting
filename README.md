# Momentum Crashes and Volatility Targeting

**Testing exposure scaling and leverage limits across U.S. and European equity markets.**

## Overview

Can volatility targeting reduce momentum crash risk without amplifying sudden losses through leverage?

This individual project compares a plain cross-sectional momentum strategy with two volatility-targeted variants on historical **S&P 500** and **STOXX Europe 600** constituents. The analysis examines compound growth, drawdowns, crash mechanisms, parameter sensitivity, transaction costs and equity-index benchmarks.

The main finding is that volatility targeting improves risk control in both samples, but allowing additional leverage can magnify an unexpected crash after a quiet period. Economic performance remains market- and sample-dependent.

[Explore the notebook](FMA_DavideFrancescoCaramia_882108.ipynb) · [View the presentation](FMA_DavideFrancescoCaramia_882108.pptx)

## Data and investment universe

| Universe | Source period | Monthly price observations | Securities in the historical panel |
|---|---|---:|---:|
| S&P 500 | December 1990–November 2018 | 336 | 1,355 |
| STOXX Europe 600 | December 2002–November 2018 | 192 | 1,255 |

Historical index weights determine membership; price availability alone does not establish eligibility. Membership is lagged by one month when calculating portfolio returns. Securities must also satisfy a 12-month historical membership requirement before entering the momentum ranking.

The source workbooks are not included. Saved notebook outputs provide the analysis and results for inspection without the input data.

## Strategy construction

### Plain cross-sectional momentum

- Rank eligible securities each month using the notebook's signal: `P[t-1] / P[t-12] - 1`.
- Buy the top decile and short the bottom decile.
- Apply equal weights within each leg.
- Require at least 30 available signals before forming a portfolio.
- Flag a portfolio-month as missing if a selected security has a missing realised return, rather than silently reallocating its weight.

The notebook labels this signal **12–1 momentum**; the formula above states its exact implementation. The baseline portfolio has approximately +100% long and −100% short exposure.

### Volatility targeting

Both variants use a **10% annual volatility target** and a **six-month trailing volatility estimate**, lagged by one month. Scaling changes portfolio exposure, not the underlying stock selection.

| Strategy | Exposure multiplier | Interpretation |
|---|---|---|
| Plain momentum | 1 | Maintains baseline exposure |
| Standard VT | `min(2, 0.10 / forecast_volatility)` | Allows exposure up to twice the baseline |
| No-additional-leverage VT | `min(1, 0.10 / forecast_volatility)` | Can reduce exposure but cannot increase it above the baseline |

“No-leverage” refers only to the additional scaling factor. The underlying strategy remains long-short, with approximately 200% gross exposure at the baseline multiplier.

## Main results

All three strategies are compared on a common set of dates **within each market**: 318 monthly observations for the S&P 500 and 174 for the STOXX Europe 600. These are different market-specific samples, not one shared cross-market period.

The following results are before the separately analysed transaction costs.

### S&P 500

| Strategy | CAGR | Annual volatility | Sharpe ratio | Maximum drawdown | Worst month |
|---|---:|---:|---:|---:|---:|
| Plain momentum | −1.53% | 28.79% | 0.11 | −82.47% | −47.30% |
| Standard VT | 1.27% | 18.79% | 0.18 | −65.31% | −58.62% |
| No-additional-leverage VT | 1.25% | 15.81% | 0.17 | −54.36% | −45.56% |

Both scaling rules reduce volatility and maximum drawdown. However, Standard VT experiences the worst single-month loss because additional exposure amplifies a sudden crash.

### STOXX Europe 600

| Strategy | CAGR | Annual volatility | Sharpe ratio | Maximum drawdown | Worst month |
|---|---:|---:|---:|---:|---:|
| Plain momentum | 6.23% | 23.92% | 0.39 | −75.36% | −45.67% |
| Standard VT | 8.47% | 14.00% | 0.65 | −26.66% | −16.40% |
| No-additional-leverage VT | 8.06% | 12.41% | 0.69 | −26.66% | −16.40% |

In the European sample, both variants improve compound growth and risk-adjusted performance. The capped-at-one variant has lower volatility and the higher reported Sharpe ratio.

## Understanding momentum crashes

The notebook examines the ten worst plain-momentum months in each market and compares all strategies on those same dates.

Loser rebounds dominate the loss contribution in **9 of the 10 U.S. episodes** and **all 10 European episodes**. A sharp recovery in previously underperforming stocks creates losses on the short leg.

Two U.S. episodes illustrate the importance of exposure before the shock:

| Episode | Forecast annual volatility | Standard VT multiplier | Plain return | Standard VT return |
|---|---:|---:|---:|---:|
| April 2009 | 47.43% | 0.21× | −47.30% | −9.97% |
| January 2001 | 7.77% | 1.29× | −45.56% | −58.62% |

Risk scaling protects when elevated past volatility has already reduced exposure. It can amplify a sudden loss when a low historical estimate permits additional leverage.

## Robustness and trading costs

The sensitivity analysis considers volatility windows of **3, 6 and 12 months** and annual targets of **8%, 10% and 12%**. Longer windows are more stable in this sample, while short windows combined with leverage can create substantial tail risk. The predefined six-month / 10% baseline is retained rather than selected retrospectively.

Transaction costs are applied through a simplified linear traded-notional model at **0, 5, 10 and 20 basis points**.

| Market and strategy | CAGR: 0 bps | CAGR: 10 bps | CAGR: 20 bps |
|---|---:|---:|---:|
| S&P 500 — Standard VT | 1.27% | 0.09% | −1.08% |
| S&P 500 — No-additional-leverage VT | 1.25% | 0.29% | −0.67% |
| STOXX Europe 600 — Standard VT | 8.47% | 7.08% | 5.71% |
| STOXX Europe 600 — No-additional-leverage VT | 8.06% | 6.92% | 5.80% |

U.S. performance is particularly sensitive to costs. European results remain positive under the tested assumptions. This exercise is a sensitivity check, not a complete implementation-cost estimate.

## Benchmark context

The corresponding long-only indices are evaluated over the same months as each market's strategy comparison.

| Benchmark | CAGR | Annual volatility | Sharpe ratio | Maximum drawdown |
|---|---:|---:|---:|---:|
| S&P 500 Index | 7.41% | 13.98% | 0.58 | −52.56% |
| STOXX 600 Index | 2.86% | 13.69% | 0.28 | −56.41% |

The S&P 500 index has stronger compound growth and Sharpe performance than the U.S. momentum strategies. In Europe, the volatility-targeted strategies have higher compound growth and shallower drawdowns than the index. Long-only indices and long-short portfolios have different exposure structures, so this is contextual rather than a like-for-like ranking.

## Repository contents

| File | Contents |
|---|---|
| `FMA_DavideFrancescoCaramia_882108.ipynb` | Complete analysis with saved tables, charts and explanations |
| `FMA_DavideFrancescoCaramia_882108.pptx` | Project presentation |
| `requirements.txt` | Notebook and Excel-reading dependencies |
| `.gitignore` | Local data, caches and temporary-file exclusions |
| `README.md` | Project overview and execution instructions |

This layout retains the original notebook and presentation filenames and places them in the repository root. No external image files are required by this README; charts are available in the notebook and presentation.

## Running the analysis

The notebook metadata records **Python 3.11.0**. Its exact original package versions are not recorded as a dependency lockfile. The supplied requirements list the required packages without claiming a validated version-pinned environment.

1. Obtain authorised access to the original workbooks:
   - `SPX500 Original.xlsm`
   - `Stoxx 600 Originale.xlsm`
2. Place both workbooks in the repository root, alongside the notebook.
3. Create and activate a virtual environment.
4. Install the dependencies and open the notebook from that directory.

```bash
python -m venv .venv
```

On macOS or Linux:

```bash
source .venv/bin/activate
```

On Windows using PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Then run:

```bash
python -m pip install -r requirements.txt
jupyter notebook FMA_DavideFrancescoCaramia_882108.ipynb
```

Execute all cells in order. The initial configuration uses `DATA_DIR = Path(".")`, so input paths are relative to the kernel's working directory. Update `DATA_DIR` if storing the workbooks elsewhere.

The notebook reads the `Price`, `Peso` and `AC` sheets. Input workbooks must retain the expected sheet names, headers and benchmark columns. A generic price spreadsheet is not a drop-in replacement.

The requirements include `openpyxl` for `.xlsm` input and Jupyter for the notebook interface. Python's `pathlib` and `warnings` modules do not require separate installation.

The results above are taken from the supplied notebook outputs and presentation. The analysis has not been rerun as part of this repository preparation because the input workbooks were not supplied.

## Limitations

- The evidence covers two historical equity universes and ends in 2018.
- Backward-looking volatility estimates cannot anticipate sudden regime changes.
- Trading costs use an approximate turnover measure based on target-weight changes; weight drift between rebalancing dates is not fully modelled.
- Borrowing fees, bid-ask spreads, market impact, taxes and time-varying liquidity are not explicitly modelled.
- Stale prices, corporate actions and duplicate share classes may affect security-level inputs.
- Parameter sensitivity is evaluated within the historical sample, not in a fully independent out-of-sample experiment.
- Factor-adjusted alpha and statistical significance are not the primary focus.
- The long-only benchmarks and long-short strategies are not directly equivalent investments.

## Author

**[Davide Francesco Caramia](https://github.com/davidecaramia)**

Individual project developed for Financial Markets Analytics at Università degli Studi di Milano-Bicocca, academic year 2025/2026.

