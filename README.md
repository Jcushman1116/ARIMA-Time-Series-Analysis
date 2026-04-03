# NVIDIA Acquisition Attempt — Time Series Analysis with Auto ARIMA

## Overview

Analyzes the market impact of NVIDIA's attempted acquisition of ARM Holdings (2020–2022)
using time series modeling. The study tracks price, log returns, and trading volume for
NVIDIA and SoftBank (used as an ARM proxy prior to the 2023 IPO), with AMD and Intel
as exogenous variables. Models are fit and evaluated across four event-driven time periods
defined by key regulatory announcements.

## Event Timeline

| Date | Event |
|------|-------|
| 2020-09-13 | NVIDIA announces intent to acquire ARM |
| 2021-07-20 | Regulatory concerns trigger security investigation |
| 2021-12-02 | FTC files lawsuit to block the acquisition |
| 2022-02-07 | Deal terminated |

## Methodology

Daily closing prices are pulled from `yfinance` for NVDA, SFTBY, AMD, and INTC.
For each ticker the following series are derived: simple returns, log returns, squared
returns, squared log returns, and volume. Missing values are forward-filled on a
business-day frequency.

The dataset is segmented into four periods defined by the event timeline above. Within
each period, `auto_arima` from `pmdarima` fits a SARIMAX model for NVIDIA and SoftBank
independently, using the remaining two tickers as exogenous regressors. Model selection
is driven by AIC with the following search bounds:

$$
\text{SARIMAX}(p,d,q)(P,D,Q)_m, \quad p,q \leq 7,\ d \leq 2,\ P,Q \leq 4,\ D \leq 2,\ m=5
$$

Models trained on one period are then used to generate out-of-sample predictions for
the subsequent period, and predicted series are plotted against realized values for
price, log returns, and volume.

## Language & Packages 

Python

- `numpy`
- `pandas`
- `matplotlib`
- `seaborn`
- `scipy`
- `statsmodels`
- `pmdarima`
- `arch`
- `scikit-learn`
- `yfinance`

## How to Run

1. Clone the repository
2. Install dependencies: `pip install -r requirements.txt`
3. Open `TS_Auto_Arima.ipynb` in Jupyter
4. Run all cells in order — data is pulled live from `yfinance` on execution
5. Key date variables (`start`, `end`, `announcement_1`, `announcement_2`, `scandal`)
   are defined in Cell 7 and can be adjusted to re-run the analysis over a different window
