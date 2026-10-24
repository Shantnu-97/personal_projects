# Derivatives: Delta-Hedging, Inflation Swaps and Credit Default Swaps

A university project (course assignment) with four parts: how well dynamic hedging replicates an option, how to build an inflation curve and value inflation-linked liabilities, and how to value a credit default swap (CDS) from market quotes.

## What it studies and why
1. **Delta-hedging errors.** In theory, continuously adjusting a stock position replicates an option exactly. In practice rebalancing is discrete, so how large are the leftover errors, and what drives them?
2. **Moneyness.** Does the hedging error depend on whether the option is in, at, or out of the money?
3. **Inflation markets.** Build an expected-inflation curve from swap quotes, then value and plan the hedging of a pension fund's inflation-linked payments.
4. **Credit.** Turn CDS spreads into default probabilities and use them to value a CDS contract and measure its sensitivity.

## Data and tools
All inputs are given in the assignment (no external data): a stock at 100, strike 100, 1 year, rate 4%, volatility 20%, drift 5%; a table of zero-coupon inflation swap rates (1–30 years), a base CPI of 120.70 (August 2025), a flat 3% nominal rate and a liability schedule; CDS spreads of 75–90 bp for 1–5 years, 40% recovery, a flat 3% rate.
Python, NumPy, pandas, SciPy, Matplotlib and the `financepy` library, in a Jupyter notebook.

## Method, in simple terms
- **Hedging:** simulate 10,000 random stock paths, hold the Black–Scholes delta in stock and cash, rebalance monthly (12 times), weekly (52) or daily (252), and record the gap between the hedge portfolio and the option payoff at expiry. Repeat for different drifts, and for options 20% in, at and out of the money.
- **Inflation:** each swap rate implies a future CPI level, CPI = base × (1 + rate)^years. CPI between quoted maturities is filled in by log-linear interpolation. Real liabilities are converted to nominal amounts using that curve and discounted at 3%. The sensitivity to a 1 bp rate move (PV01) is computed.
- **Credit:** bootstrap survival probabilities from the five CDS spreads so that each contract prices to zero, then use them to value a CDS and to bump each quoted spread by 1 bp.

## Key results
**Hedging (10,000 paths each).**
- The average error stays near zero at every frequency, so hedging is unbiased, but the spread of errors falls sharply with more frequent rebalancing: variance 3.76 (monthly), 0.91 (weekly), 0.19 (daily) for the at-the-money put.
- Errors form a cone, largest when the stock ends far from the strike and also peaking near the strike.
- Weekly hedging error is clearly negatively related to the path's realized volatility: when realized volatility is above the 20% assumed in the hedge, the hedge loses money, and when it is lower, the hedge makes a surplus.
- Changing the drift from 2.5% to 10% barely changes the error (mean absolute error 0.72 to 0.69).
- By moneyness (variance, monthly / weekly / daily): at-the-money 3.74 / 0.87 / 0.19, in-the-money 2.93 / 0.69 / 0.15, out-of-the-money 1.55 / 0.37 / 0.08. The at-the-money put is hardest to hedge and the out-of-the-money put easiest.

**Inflation.** The swap quotes imply CPI of 128.0 at 1 year and 245.9 at 30 years. The liabilities have a present value of about 167.2 million; nominal and real present values are identical here, as they must be when both use the same implied curve. The PV01 values grow with maturity, from about 1 (1 year) to about 25 (30 years). The notebook lists the PV01 from bumping and the analytic PV01 on different scales (the analytic column is 1/10,000 of the other), so the two columns should not be compared directly. Hedging is outlined with nominal bonds or swaps for rate risk and inflation swaps or inflation-linked bonds for inflation risk.

**Credit.** The bootstrapped curve gives a fair spread of 79.5 bp for a CDS maturing March 2029, against a contractual 120 bp. For a 20 million notional long-protection position that is a value of about −266,000 USD. Higher assumed recovery moves this only slightly (−268,301 at 0% to −266,938 at 30%). A 1 bp rise in the 3-year and 4-year quotes changes the value by about 3,510 and 2,870 USD, while the 1-, 2- and 5-year points matter little, so those two maturities would carry the hedge.

## How to run
1. Install: `pip install numpy pandas scipy matplotlib financepy jupyter`. The `financepy` import paths used in the notebook can differ between versions, so if an import fails, check the installed version.
2. Open `Hedging_Inflation_Credit.ipynb` in Jupyter and run all cells in order.
3. The simulation cells loop over 10,000 paths several times in plain Python and take a while. No random seed is set, so results differ slightly between runs.
