# Option Pricing and Greeks: Black–Scholes, Monte Carlo and Binomial Tree

A university project (course assignment) that prices a European call and put three different ways and studies how close, and how certain, the numerical answers are.

## What it studies and why
Closed-form prices exist for options, but most real products are priced numerically. This project uses the case where the exact answer is known, as a test. It asks how quickly Monte Carlo simulation and a binomial tree approach the exact value as more simulations or time steps are used, how large the remaining uncertainty is, and which of delta, gamma and vega are hard to estimate numerically.

**Option and market inputs:** non-dividend stock at 40, strike 40, interest rate 3.2%, volatility 20%, expiry 1/3 year.

## Data and tools
No external data. Everything is computed from the inputs above.
Python 3, NumPy, pandas, Matplotlib, in a Jupyter notebook.

## Method, in simple terms
- **Black–Scholes:** the exact formula for the call and put price, plus the exact delta (price sensitivity to the stock), gamma (how delta changes) and vega (sensitivity to volatility).
- **Monte Carlo:** simulate many possible stock prices at expiry, average the discounted payoffs, and report a 95% confidence interval. Greeks come from nudging an input slightly, re-pricing with the same random numbers, and measuring the change. Tested with 1,000 up to 300,000 simulations (fixed random seed 42).
- **Binomial tree (Cox–Ross–Rubinstein):** step the stock price up or down over many small time steps and work backwards from expiry. Greeks again come from small bumps. Tested with 10 up to 1,600 steps.

## Key results
| Method | Call | Put | Call delta | Gamma | Vega |
|---|---|---|---|---|---|
| Black–Scholes (exact) | 2.0518 | 1.6274 | 0.5597 | 0.0854 | 9.110 |
| Monte Carlo, 300,000 paths | 2.0526 | 1.6275 | 0.5590 | 0.0859 | 9.119 |
| Binomial, 1,600 steps | 2.0516 | 1.6272 | 0.5596 | 0.0909 | 9.108 |

- **Monte Carlo** converges slowly and with noise. The 95% interval on the call price shrinks from ±0.181 (1,000 paths) to ±0.011 (300,000), roughly in line with the 1/√N rule, so each extra digit of accuracy costs about 100 times more simulations. The estimate wanders around the true value rather than approaching it smoothly (for example 2.078 at 30,000 paths vs 2.045 at 100,000).
- **Binomial tree** converges steadily but not perfectly smoothly: the call price is 2.0065 at 10 steps, 2.0700 at 25 and 2.0427 at 50 before settling near 2.0516, and it is within about 0.0003 of the exact value at 1,600 steps.
- **Greeks:** delta and vega are estimated well by both methods. Gamma is the hardest. Monte Carlo gamma ranges from 0.075 to 0.094 across sample sizes, and binomial gamma is unreliable at low step counts (0.61 at 10 steps, exactly 0 at 25) and is still about 6% above the exact value at 1,600 steps. A likely cause is that the 1% price bump used for the finite difference is smaller than the gap between tree nodes.

## How to run
1. Install the requirements: `pip install numpy pandas matplotlib jupyter`
2. Open `Option_Pricing_Greeks_Project.ipynb` in Jupyter and run all cells in order. The notebook produces the price and Greek tables and two convergence plots.
