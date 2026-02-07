# Monte Carlo Option Pricing

A Python implementation of the Monte Carlo simulation method for pricing European call options using Geometric Brownian Motion (GBM).

## Overview

This project implements a numerical method to estimate the price of European call options by simulating multiple paths of underlying asset prices based on the Geometric Brownian Motion (GBM) model. The Monte Carlo method is particularly useful for complex derivative pricing when analytical solutions are unavailable.

## Features

- **Geometric Brownian Motion Simulation**: Models realistic stock price movements with drift and volatility
- **Flexible Configuration**: Easily adjust all model parameters (strike price, volatility, interest rate, time to maturity, etc.)
- **Monte Carlo Estimation**: Uses multiple simulation paths for robust option price estimation
- **Visualization**: Plots sample price paths with strike price reference line
- **Discounted Payoff Calculation**: Computes option value using risk-neutral valuation

## Mathematical Background

### Geometric Brownian Motion (GBM)

The stock price evolution is modeled using the following stochastic differential equation:

```
dS = μS dt + σS dW
```

Where:
- **S**: Stock price
- **μ**: Drift rate (related to risk-free rate r)
- **σ**: Volatility
- **dW**: Brownian motion increment

### Discrete Implementation

The discrete approximation used in the simulation is:

```
S(t+dt) = S(t) * exp((r - 0.5σ²)dt + σ√dt * ε)
```

Where **ε** is a standard normal random variable.

### European Call Option Price

The option price is calculated as the discounted expected payoff:

```
C₀ = e^(-rT) * E[max(S_T - K, 0)]
```

Where:
- **C₀**: Current option price
- **r**: Risk-free interest rate
- **T**: Time to maturity
- **S_T**: Stock price at maturity
- **K**: Strike price

## Installation

### Prerequisites

- Python 3.7+
- NumPy
- Matplotlib

### Setup

```bash
# Install required packages
pip install numpy matplotlib
```

## Usage

### Basic Example

```python
from monte_carlo_option import MonteCarloOption

# Create an instance with desired parameters
mc = MonteCarloOption(
    K=105,      # Strike price
    S0=100,     # Initial stock price
    r=0.05,     # Risk-free interest rate (5%)
    sigma=0.12, # Volatility (12%)
    T=0.5,      # Time to maturity (6 months)
    P=500,      # Number of simulation paths
    M=500       # Number of time steps
)

# Calculate the option price
option_price = mc.calcul()
print(f"Estimated European Call Option Price: ${option_price:.4f}")

# Visualize the simulation paths
mc.plot()
```

## Class Documentation

### MonteCarloOption

#### Methods

**`calcul()`**
- Simulates stock price paths using GBM
- Calculates the discounted expected payoff
- Returns the estimated option price (float)

**`plot()`**
- Visualizes sample price paths (1 in every 20 paths to reduce clutter)
- Displays strike price as a red dashed horizontal line
- Requires `calcul()` to be called first

## Parameters Explanation

### Key Parameters

- **Strike Price (K)**: The price at which the option can be exercised
- **Initial Price (S0)**: Current stock price
- **Risk-Free Rate (r)**: Used for discounting future cash flows (typically 2-5%)
- **Volatility (sigma)**: Standard deviation of stock returns (typically 5-50%)
- **Time to Maturity (T)**: Remaining time until option expiration (in years)
- **Paths (P)**: More paths = better convergence but slower computation
- **Time Steps (M)**: More steps = finer discretization but slower computation

### Choosing Parameters

- **For faster results**: Use P=100, M=100
- **For accurate results**: Use P=10000, M=1000
- **Typical use case**: P=1000, M=500 (good balance)

## Output Example

```
Estimated price of the European Call option: 5.2847
```

This means the European call option with the given parameters is worth approximately $5.28.

## How It Works

1. **Initialize**: Create a matrix to store M+1 time steps for P simulation paths
2. **Simulate**: For each time step and path:
   - Generate standard normal random variables
   - Apply GBM formula to compute next price
3. **Calculate Payoff**: For each path, compute `max(S_T - K, 0)`
4. **Discount**: Calculate the average payoff and discount it at the risk-free rate
5. **Visualize**: Plot selected paths with the strike price reference

## Advantages and Limitations

### Advantages

- **Flexibility**: Can handle complex options and multiple underlying assets
- **Intuitive**: Straightforward to understand and implement
- **General**: Works for any option type with defined payoff function
- **Scalable**: Easy to increase accuracy by adding more paths

### Limitations

- **Computational Cost**: Requires many simulations for good accuracy
- **Convergence**: Slower convergence than analytical methods when available
- **Discretization Error**: Finite time steps introduce approximation errors
- **Random Variation**: Results vary slightly between runs (use seed for reproducibility)

## Convergence Improvement Tips

1. **Increase the number of paths** (P): Reduces variance in the estimate
2. **Increase time steps** (M): Reduces discretization error
3. **Use antithetic variates**: Include both `Z` and `-Z` to reduce variance
4. **Use control variates**: Use analytical solutions where available as reference

## Example with Different Parameters

```python
# In-the-money option (higher probability of profit)
mc_itm = MonteCarloOption(K=95, S0=100, r=0.05, sigma=0.12, T=0.5, P=1000, M=500)
print(f"ITM Call Price: ${mc_itm.calcul():.4f}")

# Out-of-the-money option (lower probability of profit)
mc_otm = MonteCarloOption(K=115, S0=100, r=0.05, sigma=0.12, T=0.5, P=1000, M=500)
print(f"OTM Call Price: ${mc_otm.calcul():.4f}")

# Higher volatility scenario
mc_high_vol = MonteCarloOption(K=105, S0=100, r=0.05, sigma=0.30, T=0.5, P=1000, M=500)
print(f"High Volatility Call Price: ${mc_high_vol.calcul():.4f}")
```

## Visualization Output

The `plot()` method generates a graph showing:
- Multiple sample paths of stock price evolution
- Strike price reference line (red dashed)
- Price evolution over time steps
- Time steps on x-axis, asset price on y-axis

This visualization helps understand the range of possible outcomes under GBM.

## Future Enhancements

- European put option pricing
- American option pricing (with early exercise)
- Greeks calculation (Delta, Gamma, Vega, Theta)
- Antithetic variates variance reduction
- Control variates for improved accuracy
- Support for dividends
- Multi-asset options (basket options)
- Sensitivity analysis and parameter optimization

## Author

Created as an educational tool for understanding Monte Carlo methods in quantitative finance.

