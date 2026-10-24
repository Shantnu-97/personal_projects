# Real Estate Portfolio Valuation: Hedonic Pricing Model

A university project (course assignment) that tests whether the appraisal values in a 120-apartment portfolio in Greater Manchester, UK, match what the market would pay, and recommends which assets to sell, hold or improve.

## What it studies and why
An institutional investor holds apartments valued by an independent appraiser. Appraisals can lag or deviate from market prices. The project estimates a market price for every asset from recent real transactions, compares it with the appraisal, and turns the gaps into a hold/sell/reposition plan with estimated value from upgrades.

## Data and tools
- **Market data:** 2,687 apartment transactions from 2020–2021 across five districts of Greater Manchester, with price, floor area, bedrooms, new-build flag, energy rating, amenity flags (garden, parking, balcony and others), distances to services and transport, listing date and district.
- **Portfolio data:** 120 assets with the same characteristics plus an appraisal value.
- **Tools:** Python, pandas, NumPy, scikit-learn, statsmodels, SciPy, Matplotlib and seaborn, in a Jupyter notebook.

## Method, in simple terms
A hedonic model explains price through property characteristics. The target is the log of price, because prices are right-skewed (skewness 1.71 for price vs 0.20 for log price), so coefficients read roughly as percentage effects.
1. **Explore:** compare the portfolio with the market (similar average price, size, bedrooms, share of new builds, district mix).
2. **Model:** an 80/20 train/test split, with three models: a baseline with four structural variables, an extended linear model with all variables, and a Lasso regression (which drops unhelpful variables) with a squared size term and a new-build × energy-rating term. A Ridge model is run for comparison.
3. **Check the model:** residual plots, a test for uneven error spread, collinearity (VIF) and influential points (Cook's distance).
4. **Benchmark:** predict each asset's market value with the Lasso model and compute gap = (predicted − appraisal) / appraisal. Gap below −15% is SELL; above +5% is HOLD; in between it is REPOSITION if the asset is not refurbished or has a poor energy rating, otherwise HOLD.
5. **Reposition:** re-predict the REPOSITION assets after an energy-rating upgrade, a refurbishment, or both.

## Key results
- **Model fit (test set):** baseline R² 0.29 (RMSE 0.402), extended 0.593 (0.304), Lasso 0.599 (0.302), Ridge 0.5995. Location, size, new-build status and bedrooms matter most, and the Lasso keeps 53 non-zero coefficients. Size has a negative linear coefficient only because the squared size term is in the model.
- **Diagnostics:** error spread is uneven (Breusch–Pagan p < 0.001), many distance variables are highly collinear (20 coefficients with VIF > 5), and 177 points exceed the usual Cook's distance cut-off (4/n).
- **Classification of 120 assets:**

| Class | Assets | Avg appraisal | Avg predicted | Avg gap |
|---|---|---|---|---|
| SELL | 46 | £217,348 | £165,715 | −23.2% |
| HOLD | 41 | £117,829 | £155,962 | +33.4% |
| REPOSITION | 33 | £165,212 | £161,024 | −1.9% |

  Total predicted value is about £19.3M against about £20.3M of appraisals (roughly 5% lower). SELL assets are most numerous in Tameside (16) and Manchester (15).
- **Repositioning (33 assets, average gain per asset):** energy upgrade +£1,410, refurbishment +£11,944, both +£13,458, about £444k in total. Refurbishment is by far the stronger lever. No renovation costs are in the data, so a return cannot be calculated.
- **Caution:** the model's typical error (about 30% in price terms) is as large as the gaps used for the thresholds, so individual classifications are uncertain.

## How to run
1. Install: `pip install pandas numpy scikit-learn statsmodels scipy matplotlib seaborn jupyter`
2. The data are not included. The code reads two CSV files from the notebook's folder, `MAN_apartment_sale_clean.csv` (market) and `PATRIZIA_Portfolio.csv` (portfolio). If you have the original `.RData` files, export them in R:
   ```r
   load("MAN_apartment_sale_clean.RData"); write.csv(MAN_apartment_sale_clean, "MAN_apartment_sale_clean.csv", row.names = FALSE)
   load("PATRIZIA_Portfolio.RData"); write.csv(PATRIZIA_Portfolio, "PATRIZIA_Portfolio.csv", row.names = FALSE)
   ```
3. Open `Real_Estate_Hedonic_Pricing.ipynb` and run all cells in order.

Notes: the "top 10 price drivers" chart plots the first 10 selected variables, not the 10 largest, and the R² label on one plot is typed in rather than computed.
