# OpenAlgo Technical Indicators Documentation

## Table of Contents

1. [Introduction](#introduction)
2. [Getting Started](#getting-started)
3. [Trend Indicators](#trend-indicators)
4. [Momentum Indicators](#momentum-indicators)
5. [Volatility Indicators](#volatility-indicators)
6. [Volume Indicators](#volume-indicators)
7. [Oscillators](#oscillators)
8. [Statistical Indicators](#statistical-indicators)
9. [Hybrid Indicators](#hybrid-indicators)
10. [Utility Functions](#utility-functions)
11. [Best Practices](#best-practices)

## Introduction

The OpenAlgo Technical Indicators library provides 100+ professional-grade technical analysis indicators with TradingView Pine Script-like syntax. All indicators are optimized with NumPy and Numba for exceptional performance.

### Key Features
- ✅ TradingView-compatible calculations
- ✅ High-performance implementation with Numba JIT compilation
- ✅ Support for numpy arrays, pandas Series, and Python lists
- ✅ Comprehensive input validation
- ✅ Thread-safe calculations

### Installation

```bash
pip install openalgo
```

### Basic Usage

```python
from openalgo import ta
import numpy as np

# Example data
close = np.array([100, 102, 101, 103, 104, 102, 105, 106, 104, 107])
high = close + np.random.uniform(0, 2, len(close))
low = close - np.random.uniform(0, 2, len(close))
volume = np.random.randint(10000, 100000, len(close))

# Calculate indicators
sma = ta.sma(close, 5)
rsi = ta.rsi(close, 14)
```

---

## Getting Started

### Input Data Types

All indicators accept the following input types:
- `numpy.ndarray` (recommended for best performance)
- `pandas.Series`
- Python `list`

### Output Format

All indicators **preserve the input format**:
- **NumPy arrays** → Return `numpy.ndarray`
- **Pandas Series** → Return `pandas.Series` (with preserved index)
- **Python lists** → Return `numpy.ndarray`

Key characteristics:
- Same length as input data
- `NaN` values for periods where calculation is not possible
- Float64 precision for accurate calculations
- Pandas Series maintain their original index for time-series alignment

### Example: Preparing Data

```python
import pandas as pd
import numpy as np
from openalgo import ta

# From pandas DataFrame - returns pandas Series
df = pd.read_csv('stock_data.csv')
sma = ta.sma(df['close'], 20)  # Returns pandas.Series with same index
print(type(sma))  # <class 'pandas.core.series.Series'>

# From numpy array - returns numpy array
close_prices = np.array([100, 102, 101, 103, 104])
ema = ta.ema(close_prices, 10)  # Returns numpy.ndarray
print(type(ema))  # <class 'numpy.ndarray'>

# From Python list - returns numpy array
prices = [100, 102, 101, 103, 104]
rsi = ta.rsi(prices, 14)  # Returns numpy.ndarray
print(type(rsi))  # <class 'numpy.ndarray'>

# Pandas Series maintains index alignment
df['sma_20'] = ta.sma(df['close'], 20)  # Perfect alignment!
```

---

## Trend Indicators

Trend indicators help identify the direction and strength of market trends.

### 1. Simple Moving Average (SMA)

**Description**: The arithmetic mean of prices over a specified period. SMA smooths price data to identify trends.

**Formula**: SMA = Σ(Price) / n

**Parameters**:
- `data`: Price data (typically closing prices)
- `period`: Number of periods for calculation

**Returns**: SMA values in the same format as input

```python
# Example with NumPy array
close = np.array([100, 102, 101, 103, 104, 102, 105, 106, 104, 107])
sma_5 = ta.sma(close, 5)  # Returns numpy.ndarray

print(f"Close prices: {close}")
print(f"SMA(5): {sma_5}")
# Output: SMA(5): [nan nan nan nan 102.0 102.4 103.0 104.0 104.4 104.8]

# Example with Pandas Series
import pandas as pd
dates = pd.date_range('2024-01-01', periods=10)
close_series = pd.Series(close, index=dates)
sma_5_series = ta.sma(close_series, 5)  # Returns pandas.Series with same index

print(f"SMA as pandas Series:")
print(sma_5_series.tail())
# Maintains date index alignment!
```

### 2. Exponential Moving Average (EMA)

**Description**: A weighted moving average that gives more importance to recent prices, making it more responsive to new information.

**Formula**: EMA = α × Price + (1 - α) × Previous EMA, where α = 2/(period + 1)

**Parameters**:
- `data`: Price data
- `period`: Number of periods

**Returns**: Array of EMA values

```python
# Example
ema_10 = ta.ema(close, 10)

# Compare SMA vs EMA responsiveness
sma_10 = ta.sma(close, 10)
print(f"SMA(10) last value: {sma_10[-1]:.2f}")
print(f"EMA(10) last value: {ema_10[-1]:.2f}")
```

### 3. Weighted Moving Average (WMA)

**Description**: Assigns linearly decreasing weights to older prices, giving more importance to recent data.

**Formula**: WMA = Σ(Price × Weight) / Σ(Weight)

**Parameters**:
- `data`: Price data
- `period`: Number of periods

**Returns**: Array of WMA values

```python
# Example
wma_10 = ta.wma(close, 10)
print(f"WMA(10): {wma_10}")
```

### 4. Double Exponential Moving Average (DEMA)

**Description**: Reduces lag by applying EMA calculation twice, providing faster response to price changes.

**Formula**: DEMA = 2 × EMA(Price) - EMA(EMA(Price))

**Parameters**:
- `data`: Price data
- `period`: Number of periods

**Returns**: Array of DEMA values

```python
# Example
dema_20 = ta.dema(close, 20)
print(f"DEMA(20): {dema_20}")
```

### 5. Triple Exponential Moving Average (TEMA)

**Description**: Further reduces lag by applying EMA calculation three times.

**Formula**: TEMA = 3×EMA - 3×EMA(EMA) + EMA(EMA(EMA))

**Parameters**:
- `data`: Price data
- `period`: Number of periods

**Returns**: Array of TEMA values

```python
# Example
tema_20 = ta.tema(close, 20)
```

### 6. Hull Moving Average (HMA)

**Description**: Reduces lag while maintaining smoothness using weighted moving averages.

**Formula**: HMA = WMA(2×WMA(n/2) - WMA(n), sqrt(n))

**Parameters**:
- `data`: Price data
- `period`: Number of periods

**Returns**: Array of HMA values

```python
# Example
hma_20 = ta.hma(close, 20)
```

### 7. Volume Weighted Moving Average (VWMA)

**Description**: Weights prices by volume, giving more importance to high-volume periods.

**Formula**: VWMA = Σ(Price × Volume) / Σ(Volume)

**Parameters**:
- `data`: Price data
- `volume`: Volume data
- `period`: Number of periods

**Returns**: Array of VWMA values

```python
# Example
volume = np.array([50000, 60000, 45000, 70000, 80000, 55000, 65000, 72000, 68000, 75000])
vwma_20 = ta.vwma(close, volume, 20)
```

### 8. Arnaud Legoux Moving Average (ALMA)

**Description**: Uses Gaussian distribution to reduce lag while maintaining smoothness.

**Parameters**:
- `data`: Price data
- `period`: Number of periods (default: 21)
- `offset`: Gaussian offset (default: 0.85)
- `sigma`: Standard deviation (default: 6.0)

**Returns**: Array of ALMA values

```python
# Example
alma_21 = ta.alma(close, 21, 0.85, 6.0)
```

### 9. Kaufman's Adaptive Moving Average (KAMA)

**Description**: Adapts to market volatility, moving faster in trending markets and slower in ranging markets.

**Parameters**:
- `data`: Price data
- `period`: Efficiency ratio period (default: 10)
- `fast_period`: Fast EMA period (default: 2)
- `slow_period`: Slow EMA period (default: 30)

**Returns**: Array of KAMA values

```python
# Example
kama = ta.kama(close, 10, 2, 30)
```

### 10. Zero Lag Exponential Moving Average (ZLEMA)

**Description**: Reduces lag by adjusting for the difference between current and past prices.

**Parameters**:
- `data`: Price data
- `period`: Number of periods

**Returns**: Array of ZLEMA values

```python
# Example
zlema_20 = ta.zlema(close, 20)
```

### 11. T3 Moving Average

**Description**: Smoother moving average with reduced lag using multiple EMA calculations.

**Parameters**:
- `data`: Price data
- `period`: Number of periods (default: 21)
- `v_factor`: Volume factor (default: 0.7)

**Returns**: Array of T3 values

```python
# Example
t3_21 = ta.t3(close, 21, 0.7)
```

### 12. Fractal Adaptive Moving Average (FRAMA)

**Description**: Uses fractal geometry to adapt to price movements.

**Parameters**:
- `data`: Price data
- `period`: Number of periods (default: 16)

**Returns**: Array of FRAMA values

```python
# Example
frama_16 = ta.frama(close, 16)
```

### 13. Supertrend

**Description**: Trend-following indicator that provides clear buy/sell signals based on ATR bands.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `period`: ATR period (default: 10)
- `multiplier`: ATR multiplier (default: 3.0)

**Returns**: Tuple of (supertrend values, direction values)
- Direction: -1 for uptrend (bullish), 1 for downtrend (bearish)

```python
# Example
high = np.array([101, 103, 102, 104, 105, 103, 106, 107, 105, 108])
low = np.array([99, 101, 100, 102, 103, 101, 104, 105, 103, 106])
supertrend, direction = ta.supertrend(high, low, close, 10, 3)

# Interpret signals
for i in range(len(direction)):
    if not np.isnan(direction[i]):
        signal = "BULLISH" if direction[i] == -1 else "BEARISH"
        print(f"Day {i}: {signal}, Supertrend: {supertrend[i]:.2f}")
```

### 14. Ichimoku Cloud

**Description**: Comprehensive trend-following system with multiple components for support/resistance and trend identification.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `tenkan_period`: Conversion line period (default: 9)
- `kijun_period`: Base line period (default: 26)
- `senkou_b_period`: Leading span B period (default: 52)
- `displacement`: Cloud displacement (default: 26)

**Returns**: Tuple of (tenkan_sen, kijun_sen, senkou_span_a, senkou_span_b, chikou_span)

```python
# Example
tenkan, kijun, senkou_a, senkou_b, chikou = ta.ichimoku(high, low, close)

# Trading signals
if close[-1] > tenkan[-1] and tenkan[-1] > kijun[-1]:
    print("Bullish signal: Price above Tenkan, Tenkan above Kijun")
```

---

## Momentum Indicators

Momentum indicators measure the speed and magnitude of price changes.

### 1. Relative Strength Index (RSI)

**Description**: Measures overbought/oversold conditions by comparing magnitude of gains and losses.

**Formula**: RSI = 100 - (100 / (1 + RS)), where RS = Average Gain / Average Loss

**Parameters**:
- `data`: Price data
- `period`: Number of periods (default: 14)

**Returns**: Array of RSI values (0-100)

```python
# Example
rsi_14 = ta.rsi(close, 14)

# Identify overbought/oversold
for i, value in enumerate(rsi_14):
    if not np.isnan(value):
        if value > 70:
            print(f"Day {i}: Overbought (RSI={value:.2f})")
        elif value < 30:
            print(f"Day {i}: Oversold (RSI={value:.2f})")
```

### 2. Moving Average Convergence Divergence (MACD)

**Description**: Shows relationship between two moving averages to identify trend changes.

**Parameters**:
- `data`: Price data
- `fast_period`: Fast EMA period (default: 12)
- `slow_period`: Slow EMA period (default: 26)
- `signal_period`: Signal line EMA period (default: 9)

**Returns**: Tuple of (macd_line, signal_line, histogram)

```python
# Example
macd_line, signal_line, histogram = ta.macd(close, 12, 26, 9)

# Trading signals
for i in range(1, len(macd_line)):
    if not np.isnan(macd_line[i]) and not np.isnan(macd_line[i-1]):
        # Bullish crossover
        if macd_line[i] > signal_line[i] and macd_line[i-1] <= signal_line[i-1]:
            print(f"Day {i}: Bullish MACD crossover")
        # Bearish crossover
        elif macd_line[i] < signal_line[i] and macd_line[i-1] >= signal_line[i-1]:
            print(f"Day {i}: Bearish MACD crossover")
```

### 3. Stochastic Oscillator

**Description**: Compares closing price to price range over a period.

**Formula**: %K = 100 × (Close - Lowest Low) / (Highest High - Lowest Low)

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `k_period`: %K period (default: 14)
- `d_period`: %D period (default: 3)

**Returns**: Tuple of (k_percent, d_percent)

```python
# Example
k_percent, d_percent = ta.stochastic(high, low, close, 14, 3)

# Identify overbought/oversold
for i in range(len(k_percent)):
    if not np.isnan(k_percent[i]):
        if k_percent[i] > 80:
            print(f"Day {i}: Overbought (%K={k_percent[i]:.2f})")
        elif k_percent[i] < 20:
            print(f"Day {i}: Oversold (%K={k_percent[i]:.2f})")
```

### 4. Commodity Channel Index (CCI)

**Description**: Identifies cyclical trends by measuring deviation from average price.

**Formula**: CCI = (Typical Price - SMA) / (0.015 × Mean Deviation)

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `period`: Number of periods (default: 20)

**Returns**: Array of CCI values

```python
# Example
cci_20 = ta.cci(high, low, close, 20)

# Identify extreme levels
for i, value in enumerate(cci_20):
    if not np.isnan(value):
        if value > 100:
            print(f"Day {i}: Overbought (CCI={value:.2f})")
        elif value < -100:
            print(f"Day {i}: Oversold (CCI={value:.2f})")
```

### 5. Williams %R

**Description**: Momentum indicator showing overbought/oversold levels.

**Formula**: %R = (Highest High - Close) / (Highest High - Lowest Low) × -100

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `period`: Number of periods (default: 14)

**Returns**: Array of Williams %R values (-100 to 0)

```python
# Example
williams_r = ta.williams_r(high, low, close, 14)

# Identify signals
for i, value in enumerate(williams_r):
    if not np.isnan(value):
        if value > -20:
            print(f"Day {i}: Overbought (%R={value:.2f})")
        elif value < -80:
            print(f"Day {i}: Oversold (%R={value:.2f})")
```

---

## Volatility Indicators

Volatility indicators measure the rate of price movement regardless of direction.

### 1. Average True Range (ATR)

**Description**: Measures market volatility by analyzing the entire range of price movement.

**Formula**: TR = max(High-Low, |High-Previous Close|, |Low-Previous Close|)

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `period`: Number of periods (default: 14)

**Returns**: Array of ATR values

```python
# Example
atr_14 = ta.atr(high, low, close, 14)

# Use for position sizing
risk_per_trade = 1000  # $1000 risk
position_size = risk_per_trade / (atr_14[-1] * 2)  # 2 ATR stop loss
print(f"Position size based on ATR: {position_size:.0f} shares")
```

### 2. Bollinger Bands

**Description**: Volatility bands placed above and below a moving average.

**Formula**: 
- Upper Band = SMA + (StdDev × multiplier)
- Lower Band = SMA - (StdDev × multiplier)

**Parameters**:
- `data`: Price data
- `period`: SMA period (default: 20)
- `std_dev`: Standard deviation multiplier (default: 2.0)

**Returns**: Tuple of (upper_band, middle_band, lower_band)

```python
# Example
upper, middle, lower = ta.bbands(close, 20, 2)

# Bollinger Band squeeze detection
bandwidth = (upper - lower) / middle
for i in range(1, len(bandwidth)):
    if not np.isnan(bandwidth[i]):
        if bandwidth[i] < bandwidth[i-1]:
            print(f"Day {i}: Bollinger Band squeeze (low volatility)")
```

### 3. Keltner Channel

**Description**: Volatility-based envelope using ATR for channel width.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `ema_period`: EMA period (default: 20)
- `atr_period`: ATR period (default: 10)
- `multiplier`: ATR multiplier (default: 2.0)

**Returns**: Tuple of (upper_channel, middle_line, lower_channel)

```python
# Example
kc_upper, kc_middle, kc_lower = ta.keltner_channel(high, low, close, 20, 10, 2)

# Channel breakout detection
for i in range(len(close)):
    if close[i] > kc_upper[i]:
        print(f"Day {i}: Price broke above Keltner Channel")
    elif close[i] < kc_lower[i]:
        print(f"Day {i}: Price broke below Keltner Channel")
```

### 4. Donchian Channel

**Description**: Shows highest high and lowest low over a period.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `period`: Number of periods (default: 20)

**Returns**: Tuple of (upper_channel, middle_line, lower_channel)

```python
# Example
dc_upper, dc_middle, dc_lower = ta.donchian_channel(high, low, 20)

# Turtle trading system signals
for i in range(len(close)):
    if close[i] >= dc_upper[i]:
        print(f"Day {i}: 20-day high breakout")
    elif close[i] <= dc_lower[i]:
        print(f"Day {i}: 20-day low breakout")
```

### 5. Chaikin Volatility

**Description**: Measures volatility by calculating percentage change in trading range.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `ema_period`: EMA period (default: 10)
- `roc_period`: ROC period (default: 10)

**Returns**: Array of Chaikin Volatility values

```python
# Example
chaikin_vol = ta.chaikin_volatility(high, low, 10, 10)
```

### 6. Normalized Average True Range (NATR)

**Description**: ATR expressed as percentage of closing price.

**Formula**: NATR = (ATR / Close) × 100

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `period`: Number of periods (default: 14)

**Returns**: Array of NATR values (percentage)

```python
# Example
natr_14 = ta.natr(high, low, close, 14)
print(f"Current volatility: {natr_14[-1]:.2f}% of price")
```

### 7. Relative Volatility Index (RVI)

**Description**: Similar to RSI but uses standard deviation instead of price change.

**Parameters**:
- `data`: Price data
- `stdev_period`: Standard deviation period (default: 10)
- `rsi_period`: RSI calculation period (default: 14)

**Returns**: Array of RVI values

```python
# Example
rvi = ta.rvi_volatility(close, 10, 14)
```

### 8. Ultimate Oscillator

**Description**: Combines short, medium, and long-term market momentum.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `period1`: First period (default: 7)
- `period2`: Second period (default: 14)
- `period3`: Third period (default: 28)

**Returns**: Array of Ultimate Oscillator values

```python
# Example
ultosc = ta.ultimate_oscillator(high, low, close, 7, 14, 28)
```

### 9. Standard Deviation

**Description**: Measures dispersion of prices from the mean.

**Parameters**:
- `data`: Price data
- `period`: Number of periods (default: 20)

**Returns**: Array of standard deviation values

```python
# Example
stddev_20 = ta.stddev(close, 20)
```

### 10. True Range

**Description**: Greatest of: current high-low, |high-previous close|, |low-previous close|.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices

**Returns**: Array of true range values

```python
# Example
true_range = ta.true_range(high, low, close)
```

### 11. Mass Index

**Description**: Identifies trend reversals by measuring range expansion.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `fast_period`: Fast EMA period (default: 9)
- `slow_period`: Sum period (default: 25)

**Returns**: Array of Mass Index values

```python
# Example
mass_idx = ta.mass_index(high, low, 9, 25)

# Reversal signal
for i in range(1, len(mass_idx)):
    if not np.isnan(mass_idx[i]):
        if mass_idx[i] > 27 and mass_idx[i-1] <= 27:
            print(f"Day {i}: Mass Index reversal signal")
```

---

## Volume Indicators

Volume indicators analyze trading volume to confirm price movements and identify trends.

### 1. On Balance Volume (OBV)

**Description**: Cumulative indicator using volume flow to predict price changes.

**Formula**: 
- If Close > Previous Close: OBV = Previous OBV + Volume
- If Close < Previous Close: OBV = Previous OBV - Volume

**Parameters**:
- `close`: Close prices
- `volume`: Volume data

**Returns**: Array of OBV values

```python
# Example
volume = np.array([100000, 120000, 90000, 150000, 110000, 130000, 95000, 140000, 125000, 135000])
obv = ta.obv(close, volume)

# Divergence detection
for i in range(20, len(close)):
    price_trend = "up" if close[i] > close[i-20] else "down"
    obv_trend = "up" if obv[i] > obv[i-20] else "down"
    if price_trend != obv_trend:
        print(f"Day {i}: OBV divergence detected")
```

### 2. Volume Weighted Average Price (VWAP)

**Description**: Average price weighted by volume, often used as support/resistance.

**Formula**: VWAP = Σ(Typical Price × Volume) / Σ(Volume)

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `volume`: Volume data
- `period`: Period for rolling VWAP (0 for cumulative)

**Returns**: Array of VWAP values

```python
# Example
vwap = ta.vwap(high, low, close, volume, 0)  # Cumulative VWAP

# Trading signals
for i in range(len(close)):
    if close[i] > vwap[i]:
        print(f"Day {i}: Price above VWAP (bullish)")
    else:
        print(f"Day {i}: Price below VWAP (bearish)")
```

### 3. Money Flow Index (MFI)

**Description**: Volume-weighted RSI that measures buying and selling pressure.

**Formula**: MFI = 100 - (100 / (1 + Money Ratio))

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `volume`: Volume data
- `period`: Number of periods (default: 14)

**Returns**: Array of MFI values (0-100)

```python
# Example
mfi_14 = ta.mfi(high, low, close, volume, 14)

# Overbought/Oversold signals
for i, value in enumerate(mfi_14):
    if not np.isnan(value):
        if value > 80:
            print(f"Day {i}: Overbought (MFI={value:.2f})")
        elif value < 20:
            print(f"Day {i}: Oversold (MFI={value:.2f})")
```

### 4. Accumulation/Distribution Line (ADL)

**Description**: Measures cumulative flow of money into and out of a security.

**Formula**: ADL = Previous ADL + Money Flow Volume

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `volume`: Volume data

**Returns**: Array of ADL values

```python
# Example
adl = ta.adl(high, low, close, volume)

# Trend confirmation
for i in range(1, len(adl)):
    if adl[i] > adl[i-1] and close[i] > close[i-1]:
        print(f"Day {i}: Uptrend confirmed by ADL")
```

### 5. Chaikin Money Flow (CMF)

**Description**: Measures money flow volume over a specific period.

**Formula**: CMF = Sum(Money Flow Volume, n) / Sum(Volume, n)

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `volume`: Volume data
- `period`: Number of periods (default: 20)

**Returns**: Array of CMF values

```python
# Example
cmf_20 = ta.cmf(high, low, close, volume, 20)

# Interpret signals
for i, value in enumerate(cmf_20):
    if not np.isnan(value):
        if value > 0.05:
            print(f"Day {i}: Strong buying pressure (CMF={value:.3f})")
        elif value < -0.05:
            print(f"Day {i}: Strong selling pressure (CMF={value:.3f})")
```

### 6. Ease of Movement (EMV)

**Description**: Shows relationship between price change and volume.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `volume`: Volume data
- `scale`: Scale factor (default: 1000000)

**Returns**: Array of EMV values

```python
# Example
emv = ta.emv(high, low, volume, 1000000)
```

### 7. Force Index

**Description**: Combines price and volume to measure buying/selling pressure.

**Formula**: FI = Volume × (Close - Previous Close)

**Parameters**:
- `close`: Close prices
- `volume`: Volume data

**Returns**: Array of Force Index values

```python
# Example
force_idx = ta.force_index(close, volume)

# Strong moves
for i, value in enumerate(force_idx):
    if not np.isnan(value) and abs(value) > 1000000:
        direction = "buying" if value > 0 else "selling"
        print(f"Day {i}: Strong {direction} pressure")
```

### 8. Negative Volume Index (NVI)

**Description**: Tracks cumulative changes on days when volume decreases.

**Parameters**:
- `close`: Close prices
- `volume`: Volume data

**Returns**: Array of NVI values

```python
# Example
nvi = ta.nvi(close, volume)
```

### 9. Positive Volume Index (PVI)

**Description**: Tracks cumulative changes on days when volume increases.

**Parameters**:
- `close`: Close prices
- `volume`: Volume data

**Returns**: Array of PVI values

```python
# Example
pvi = ta.pvi(close, volume)

# Bull/Bear market detection
nvi_ma = ta.sma(nvi, 255)  # 1-year moving average
for i in range(len(nvi)):
    if not np.isnan(nvi_ma[i]):
        if nvi[i] > nvi_ma[i]:
            print(f"Day {i}: Bullish (NVI above 255-day MA)")
```

### 10. Volume Oscillator

**Description**: Shows relationship between two volume moving averages.

**Formula**: VO = ((Short MA - Long MA) / Long MA) × 100

**Parameters**:
- `volume`: Volume data
- `fast_period`: Fast MA period (default: 5)
- `slow_period`: Slow MA period (default: 10)

**Returns**: Array of Volume Oscillator values

```python
# Example
vol_osc = ta.volume_oscillator(volume, 5, 10)

# Volume trends
for i, value in enumerate(vol_osc):
    if not np.isnan(value):
        if value > 0:
            print(f"Day {i}: Increasing volume trend ({value:.2f}%)")
```

### 11. Volume Rate of Change (VROC)

**Description**: Measures rate of change in volume.

**Formula**: VROC = ((Volume - Volume[n periods ago]) / Volume[n periods ago]) × 100

**Parameters**:
- `volume`: Volume data
- `period`: Number of periods (default: 25)

**Returns**: Array of VROC values

```python
# Example
vroc_25 = ta.vroc(volume, 25)

# Volume surges
for i, value in enumerate(vroc_25):
    if not np.isnan(value) and value > 100:
        print(f"Day {i}: Volume surge ({value:.2f}% increase)")
```

---

## Oscillators

Oscillators are momentum indicators that fluctuate within a bounded range.

### 1. Rate of Change (ROC)

**Description**: Measures percentage change in price over a period.

**Formula**: ROC = ((Price - Price[n periods ago]) / Price[n periods ago]) × 100

**Parameters**:
- `data`: Price data
- `period`: Number of periods (default: 12)

**Returns**: Array of ROC values (percentage)

```python
# Example
roc_12 = ta.roc_oscillator(close, 12)

# Momentum signals
for i, value in enumerate(roc_12):
    if not np.isnan(value):
        if value > 10:
            print(f"Day {i}: Strong upward momentum (ROC={value:.2f}%)")
        elif value < -10:
            print(f"Day {i}: Strong downward momentum (ROC={value:.2f}%)")
```

### 2. Chande Momentum Oscillator (CMO)

**Description**: Measures momentum using the difference between sum of gains and losses.

**Formula**: CMO = ((Sum of Gains - Sum of Losses) / (Sum of Gains + Sum of Losses)) × 100

**Parameters**:
- `data`: Price data
- `period`: Number of periods (default: 14)

**Returns**: Array of CMO values (-100 to +100)

```python
# Example
cmo_14 = ta.cmo(close, 14)

# Overbought/Oversold
for i, value in enumerate(cmo_14):
    if not np.isnan(value):
        if value > 50:
            print(f"Day {i}: Overbought (CMO={value:.2f})")
        elif value < -50:
            print(f"Day {i}: Oversold (CMO={value:.2f})")
```

### 3. TRIX

**Description**: Triple exponentially smoothed momentum oscillator.

**Parameters**:
- `data`: Price data
- `period`: Number of periods (default: 14)

**Returns**: Array of TRIX values

```python
# Example
trix_14 = ta.trix(close, 14)

# Zero-line crossovers
for i in range(1, len(trix_14)):
    if not np.isnan(trix_14[i]) and not np.isnan(trix_14[i-1]):
        if trix_14[i] > 0 and trix_14[i-1] <= 0:
            print(f"Day {i}: TRIX bullish crossover")
        elif trix_14[i] < 0 and trix_14[i-1] >= 0:
            print(f"Day {i}: TRIX bearish crossover")
```

### 4. Ultimate Oscillator (UO)

**Description**: Combines short, medium, and long-term momentum.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `period1`: Short period (default: 7)
- `period2`: Medium period (default: 14)
- `period3`: Long period (default: 28)

**Returns**: Array of UO values (0-100)

```python
# Example
uo = ta.uo_oscillator(high, low, close, 7, 14, 28)

# Trading signals
for i, value in enumerate(uo):
    if not np.isnan(value):
        if value > 70:
            print(f"Day {i}: Overbought (UO={value:.2f})")
        elif value < 30:
            print(f"Day {i}: Oversold (UO={value:.2f})")
```

### 5. Awesome Oscillator (AO)

**Description**: Measures market momentum using difference between 5 and 34-period SMAs of midpoint.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `fast_period`: Fast SMA period (default: 5)
- `slow_period`: Slow SMA period (default: 34)

**Returns**: Array of AO values

```python
# Example
ao = ta.awesome_oscillator(high, low, 5, 34)

# Saucer signals
for i in range(2, len(ao)):
    if not np.isnan(ao[i]):
        # Bullish saucer
        if ao[i] > ao[i-1] > ao[i-2] and ao[i-1] < 0:
            print(f"Day {i}: Bullish saucer signal")
```

### 6. Accelerator Oscillator (AC)

**Description**: Measures acceleration/deceleration of the Awesome Oscillator.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `period`: SMA period for AO smoothing (default: 5)

**Returns**: Array of AC values

```python
# Example
ac = ta.accelerator_oscillator(high, low, 5)
```

### 7. Percentage Price Oscillator (PPO)

**Description**: Shows difference between two EMAs as a percentage.

**Parameters**:
- `data`: Price data
- `fast_period`: Fast EMA period (default: 12)
- `slow_period`: Slow EMA period (default: 26)
- `signal_period`: Signal line period (default: 9)

**Returns**: Tuple of (ppo_line, signal_line, histogram)

```python
# Example
ppo_line, ppo_signal, ppo_hist = ta.ppo(close, 12, 26, 9)

# Similar to MACD but percentage-based
for i in range(1, len(ppo_line)):
    if not np.isnan(ppo_line[i]):
        if ppo_line[i] > ppo_signal[i] and ppo_line[i-1] <= ppo_signal[i-1]:
            print(f"Day {i}: PPO bullish crossover")
```

### 8. Price Oscillator (PO)

**Description**: Difference between two moving averages.

**Parameters**:
- `data`: Price data
- `fast_period`: Fast MA period (default: 10)
- `slow_period`: Slow MA period (default: 20)
- `ma_type`: Moving average type ("SMA" or "EMA", default: "SMA")

**Returns**: Array of Price Oscillator values

```python
# Example
po_sma = ta.price_oscillator(close, 10, 20, "SMA")
po_ema = ta.price_oscillator(close, 10, 20, "EMA")
```

### 9. Detrended Price Oscillator (DPO)

**Description**: Removes trend from price to identify cycles.

**Formula**: DPO = Price - SMA(shifted back)

**Parameters**:
- `data`: Price data
- `period`: Number of periods (default: 20)

**Returns**: Array of DPO values

```python
# Example
dpo_20 = ta.dpo(close, 20)

# Cycle identification
for i in range(1, len(dpo_20)):
    if not np.isnan(dpo_20[i]) and not np.isnan(dpo_20[i-1]):
        if dpo_20[i] > 0 and dpo_20[i-1] <= 0:
            print(f"Day {i}: Cycle low detected")
```

### 10. Aroon Oscillator

**Description**: Difference between Aroon Up and Aroon Down.

**Formula**: Aroon Oscillator = Aroon Up - Aroon Down

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `period`: Number of periods (default: 25)

**Returns**: Array of Aroon Oscillator values (-100 to +100)

```python
# Example
aroon_osc = ta.aroon_oscillator(high, low, 25)

# Trend strength
for i, value in enumerate(aroon_osc):
    if not np.isnan(value):
        if value > 50:
            print(f"Day {i}: Strong uptrend (Aroon Osc={value:.2f})")
        elif value < -50:
            print(f"Day {i}: Strong downtrend (Aroon Osc={value:.2f})")
```

---

## Statistical Indicators

Statistical indicators apply mathematical and statistical methods to price data.

### 1. Linear Regression

**Description**: Fits a straight line to price data using least squares method.

**Parameters**:
- `data`: Price data
- `period`: Number of periods (default: 14)

**Returns**: Array of linear regression values

```python
# Example
linreg_14 = ta.linear_regression(close, 14)

# Trend direction
for i in range(1, len(linreg_14)):
    if not np.isnan(linreg_14[i]) and not np.isnan(linreg_14[i-1]):
        if linreg_14[i] > linreg_14[i-1]:
            print(f"Day {i}: Upward trend")
```

### 2. Linear Regression Slope

**Description**: Slope of the linear regression line.

**Parameters**:
- `data`: Price data
- `period`: Number of periods (default: 14)

**Returns**: Array of slope values

```python
# Example
lr_slope_14 = ta.linear_regression_slope(close, 14)

# Trend strength
for i, slope in enumerate(lr_slope_14):
    if not np.isnan(slope):
        if abs(slope) > 0.5:
            direction = "up" if slope > 0 else "down"
            print(f"Day {i}: Strong {direction} trend (slope={slope:.3f})")
```

### 3. Pearson Correlation Coefficient

**Description**: Measures linear correlation between two datasets.

**Parameters**:
- `data1`: First dataset
- `data2`: Second dataset
- `period`: Number of periods (default: 20)

**Returns**: Array of correlation values (-1 to +1)

```python
# Example: Correlation between two stocks
stock1 = np.array([100, 102, 101, 103, 104, 102, 105, 106, 104, 107])
stock2 = np.array([50, 51, 50.5, 52, 52.5, 51.5, 53, 53.5, 52, 54])
correlation = ta.correlation(stock1, stock2, 5)

print(f"5-day correlation: {correlation[-1]:.3f}")
```

### 4. Beta Coefficient

**Description**: Measures volatility relative to market.

**Formula**: β = Cov(asset, market) / Var(market)

**Parameters**:
- `asset`: Asset price data
- `market`: Market price data
- `period`: Number of periods (default: 252)

**Returns**: Array of beta values

```python
# Example
market = np.array([1000, 1010, 1005, 1020, 1025, 1015, 1030, 1035, 1025, 1040])
beta = ta.beta(close, market, 252)

# Risk assessment
if not np.isnan(beta[-1]):
    if beta[-1] > 1:
        print(f"Stock is {beta[-1]:.2f}x more volatile than market")
    else:
        print(f"Stock is {beta[-1]:.2f}x less volatile than market")
```

### 5. Variance

**Description**: Measures dispersion of returns.

**Parameters**:
- `data`: Price data
- `period`: Number of periods (default: 20)

**Returns**: Array of variance values

```python
# Example
variance_20 = ta.variance(close, 20)
volatility = np.sqrt(variance_20)  # Standard deviation
print(f"20-day volatility: {volatility[-1]:.2f}")
```

### 6. Time Series Forecast (TSF)

**Description**: Predicts next value using linear regression.

**Parameters**:
- `data`: Price data
- `period`: Number of periods (default: 14)

**Returns**: Array of forecast values

```python
# Example
tsf_14 = ta.time_series_forecast(close, 14)

# Compare forecast with actual
if not np.isnan(tsf_14[-2]):  # Previous forecast
    error = abs(close[-1] - tsf_14[-2])
    print(f"Forecast error: {error:.2f}")
```

### 7. Rolling Median

**Description**: Middle value in a sorted dataset.

**Parameters**:
- `data`: Price data
- `period`: Number of periods (default: 20)

**Returns**: Array of median values

```python
# Example
median_20 = ta.median(close, 20)

# Robust trend indicator
for i in range(len(close)):
    if not np.isnan(median_20[i]):
        if close[i] > median_20[i]:
            print(f"Day {i}: Price above median (bullish)")
```

### 8. Rolling Mode

**Description**: Most frequent value in a dataset.

**Parameters**:
- `data`: Price data
- `period`: Number of periods (default: 20)
- `bins`: Number of bins for discretization (default: 10)

**Returns**: Array of mode values

```python
# Example
mode_20 = ta.mode(close, 20, 10)

# Price clustering
for i in range(len(close)):
    if not np.isnan(mode_20[i]):
        if abs(close[i] - mode_20[i]) < 0.5:
            print(f"Day {i}: Price near mode (potential support/resistance)")
```

---

## Hybrid Indicators

Hybrid indicators combine multiple calculation methods for comprehensive analysis.

### 1. Average Directional Index (ADX) System

**Description**: Measures trend strength with directional indicators.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `period`: Number of periods (default: 14)

**Returns**: Tuple of (+DI, -DI, ADX)

```python
# Example
di_plus, di_minus, adx = ta.adx_system(high, low, close, 14)

# Trading signals
for i in range(len(adx)):
    if not np.isnan(adx[i]):
        if adx[i] > 25:
            if di_plus[i] > di_minus[i]:
                print(f"Day {i}: Strong uptrend (ADX={adx[i]:.1f})")
            else:
                print(f"Day {i}: Strong downtrend (ADX={adx[i]:.1f})")
        else:
            print(f"Day {i}: Weak/No trend (ADX={adx[i]:.1f})")
```

### 2. Aroon System

**Description**: Identifies trend changes and strength.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `period`: Number of periods (default: 25)

**Returns**: Tuple of (aroon_up, aroon_down)

```python
# Example
aroon_up, aroon_down = ta.aroon_system(high, low, 25)

# Trend identification
for i in range(len(aroon_up)):
    if not np.isnan(aroon_up[i]):
        if aroon_up[i] > 70 and aroon_down[i] < 30:
            print(f"Day {i}: Strong uptrend")
        elif aroon_down[i] > 70 and aroon_up[i] < 30:
            print(f"Day {i}: Strong downtrend")
        elif aroon_up[i] < 50 and aroon_down[i] < 50:
            print(f"Day {i}: No clear trend")
```

### 3. Pivot Points

**Description**: Calculates support and resistance levels.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices

**Returns**: Tuple of (pivot, r1, s1, r2, s2, r3, s3)

```python
# Example
pivot, r1, s1, r2, s2, r3, s3 = ta.pivot_points(high, low, close)

# Current day analysis
current_price = close[-1]
current_pivot = pivot[-1]

print(f"Pivot: {current_pivot:.2f}")
print(f"Resistance: R1={r1[-1]:.2f}, R2={r2[-1]:.2f}, R3={r3[-1]:.2f}")
print(f"Support: S1={s1[-1]:.2f}, S2={s2[-1]:.2f}, S3={s3[-1]:.2f}")

# Position relative to pivot
if current_price > current_pivot:
    print("Price above pivot - Bullish bias")
else:
    print("Price below pivot - Bearish bias")
```

### 4. Parabolic SAR

**Description**: Trend-following indicator providing stop and reverse points.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `acceleration`: Acceleration factor (default: 0.02)
- `maximum`: Maximum acceleration (default: 0.2)

**Returns**: Tuple of (sar_values, trend_direction)

```python
# Example
sar_values, sar_trend = ta.parabolic_sar(high, low, 0.02, 0.2)

# Trading signals
for i in range(1, len(sar_trend)):
    if not np.isnan(sar_trend[i]):
        # Trend reversal
        if sar_trend[i] != sar_trend[i-1]:
            if sar_trend[i] == 1:
                print(f"Day {i}: SAR flip to uptrend - BUY")
            else:
                print(f"Day {i}: SAR flip to downtrend - SELL")
        
        # Stop loss level
        print(f"Day {i}: Stop loss at {sar_values[i]:.2f}")
```

### 5. Directional Movement Index (DMI)

**Description**: Identifies directional movement in trends.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `close`: Close prices
- `period`: Number of periods (default: 14)

**Returns**: Tuple of (+DI, -DI)

```python
# Example
dmi_plus, dmi_minus = ta.directional_movement(high, low, close, 14)

# Crossover signals
for i in range(1, len(dmi_plus)):
    if not np.isnan(dmi_plus[i]):
        if dmi_plus[i] > dmi_minus[i] and dmi_plus[i-1] <= dmi_minus[i-1]:
            print(f"Day {i}: +DI crossed above -DI (Bullish)")
        elif dmi_plus[i] < dmi_minus[i] and dmi_plus[i-1] >= dmi_minus[i-1]:
            print(f"Day {i}: -DI crossed above +DI (Bearish)")
```

### 6. PSAR (Parabolic SAR values only)

**Description**: Returns only SAR values without trend direction.

**Parameters**:
- `high`: High prices
- `low`: Low prices
- `acceleration`: Acceleration factor (default: 0.02)
- `maximum`: Maximum acceleration (default: 0.2)

**Returns**: Array of PSAR values

```python
# Example
psar = ta.psar(high, low, 0.02, 0.2)

# Use as trailing stop
for i in range(len(close)):
    if not np.isnan(psar[i]):
        if close[i] > psar[i]:
            stop_distance = close[i] - psar[i]
            print(f"Day {i}: Long position, stop {stop_distance:.2f} below")
```

### 7. Hilbert Transform Trendline

**Description**: Advanced signal processing technique for trend identification.

**Parameters**:
- `data`: Price data

**Returns**: Array of trendline values

```python
# Example
ht_trendline = ta.hilbert_trendline(close)

# Smooth trend indicator
for i in range(1, len(close)):
    if not np.isnan(ht_trendline[i]):
        if close[i] > ht_trendline[i] and close[i-1] <= ht_trendline[i-1]:
            print(f"Day {i}: Price crossed above Hilbert Trendline")
```

---

## Utility Functions

Utility functions provide common calculations used in technical analysis.

### 1. Crossover

**Description**: Detects when series1 crosses above series2.

**Parameters**:
- `series1`: First data series
- `series2`: Second data series

**Returns**: Boolean array (True at crossover points)

```python
# Example
fast_ma = ta.ema(close, 10)
slow_ma = ta.ema(close, 20)
crossovers = ta.crossover(fast_ma, slow_ma)

# Trading signals
for i in range(len(crossovers)):
    if crossovers[i]:
        print(f"Day {i}: Golden cross - BUY signal")
```

### 2. Crossunder

**Description**: Detects when series1 crosses below series2.

**Parameters**:
- `series1`: First data series
- `series2`: Second data series

**Returns**: Boolean array (True at crossunder points)

```python
# Example
crossunders = ta.crossunder(fast_ma, slow_ma)

for i in range(len(crossunders)):
    if crossunders[i]:
        print(f"Day {i}: Death cross - SELL signal")
```

### 3. Highest

**Description**: Highest value over a rolling window.

**Parameters**:
- `data`: Input data
- `period`: Window size

**Returns**: Array of highest values

```python
# Example
highest_20 = ta.highest(high, 20)

# Breakout detection
for i in range(len(high)):
    if high[i] >= highest_20[i]:
        print(f"Day {i}: New 20-day high!")
```

### 4. Lowest

**Description**: Lowest value over a rolling window.

**Parameters**:
- `data`: Input data
- `period`: Window size

**Returns**: Array of lowest values

```python
# Example
lowest_20 = ta.lowest(low, 20)

# Support levels
print(f"20-day support at: {lowest_20[-1]:.2f}")
```

### 5. Change

**Description**: Difference from n periods ago.

**Parameters**:
- `data`: Input data
- `length`: Number of periods to look back (default: 1)

**Returns**: Array of change values

```python
# Example
daily_change = ta.change(close, 1)
weekly_change = ta.change(close, 5)

print(f"Daily change: {daily_change[-1]:.2f}")
print(f"Weekly change: {weekly_change[-1]:.2f}")
```

### 6. Rate of Change (ROC)

**Description**: Percentage change from n periods ago.

**Parameters**:
- `data`: Input data
- `length`: Number of periods to look back

**Returns**: Array of ROC values (percentage)

```python
# Example
roc_10 = ta.roc(close, 10)

print(f"10-day ROC: {roc_10[-1]:.2f}%")
```

### 7. Standard Deviation

**Description**: Rolling standard deviation.

**Parameters**:
- `data`: Input data
- `period`: Window size

**Returns**: Array of standard deviation values

```python
# Example
stdev_20 = ta.stdev(close, 20)

# Volatility bands
upper_band = close + (2 * stdev_20)
lower_band = close - (2 * stdev_20)
```

---

## Best Practices

### 1. Data Preparation

```python
# Always validate your data
def prepare_data(df):
    # Remove NaN values
    df = df.dropna()
    
    # Ensure proper data types
    df['close'] = df['close'].astype(float)
    df['volume'] = df['volume'].astype(float)
    
    # Sort by date
    df = df.sort_values('date')
    
    return df
```

### 2. Indicator Combinations

```python
# Multi-indicator strategy
def generate_signals(high, low, close, volume):
    # Trend
    ema_short = ta.ema(close, 20)
    ema_long = ta.ema(close, 50)
    trend = np.where(ema_short > ema_long, 1, -1)
    
    # Momentum
    rsi = ta.rsi(close, 14)
    momentum = np.where(rsi > 50, 1, -1)
    
    # Volume
    obv = ta.obv(close, volume)
    obv_ma = ta.sma(obv, 20)
    volume_confirm = np.where(obv > obv_ma, 1, -1)
    
    # Combined signal
    signal = trend + momentum + volume_confirm
    return signal
```

### 3. Parameter Optimization

```python
# Test different parameters
def optimize_ma_crossover(close, fast_range, slow_range):
    best_params = None
    best_return = -np.inf
    
    for fast in fast_range:
        for slow in slow_range:
            if fast >= slow:
                continue
                
            # Calculate returns
            fast_ma = ta.ema(close, fast)
            slow_ma = ta.ema(close, slow)
            crossovers = ta.crossover(fast_ma, slow_ma)
            
            # Simple backtest
            returns = calculate_returns(close, crossovers)
            
            if returns > best_return:
                best_return = returns
                best_params = (fast, slow)
    
    return best_params
```

### 4. Risk Management

```python
# Position sizing with ATR
def calculate_position_size(capital, risk_percent, atr_value, atr_multiplier=2):
    risk_amount = capital * (risk_percent / 100)
    stop_distance = atr_value * atr_multiplier
    position_size = risk_amount / stop_distance
    return position_size

# Example
capital = 100000
risk_percent = 1  # Risk 1% per trade
atr = ta.atr(high, low, close, 14)
position_size = calculate_position_size(capital, risk_percent, atr[-1])
```

### 5. Performance Considerations

```python
# Use numpy arrays for best performance
import numpy as np

# Good - numpy array
prices_np = np.array(price_list)
sma_fast = ta.sma(prices_np, 20)

# Also works but slower - list
sma_slow = ta.sma(price_list, 20)

# Batch calculations
indicators = {
    'sma_20': ta.sma(close, 20),
    'ema_20': ta.ema(close, 20),
    'rsi_14': ta.rsi(close, 14),
    'atr_14': ta.atr(high, low, close, 14)
}
```

### 6. Error Handling

```python
# Handle edge cases
def safe_indicator_calculation(func, *args, **kwargs):
    try:
        result = func(*args, **kwargs)
        
        # Check for all NaN
        if np.all(np.isnan(result)):
            print(f"Warning: {func.__name__} returned all NaN values")
            
        return result
    except Exception as e:
        print(f"Error in {func.__name__}: {e}")
        return None

# Example
rsi = safe_indicator_calculation(ta.rsi, close, 14)
```

---

## Complete Example: Multi-Indicator Trading System

```python
import numpy as np
from openalgo import ta

def trading_system(high, low, close, volume):
    """
    Complete trading system using multiple indicators
    """
    
    # 1. Trend Analysis
    sma_50 = ta.sma(close, 50)
    sma_200 = ta.sma(close, 200)
    ema_20 = ta.ema(close, 20)
    supertrend, st_direction = ta.supertrend(high, low, close, 10, 3)
    
    # Major trend
    major_trend = "bull" if sma_50[-1] > sma_200[-1] else "bear"
    
    # 2. Momentum Confirmation
    rsi = ta.rsi(close, 14)
    macd_line, macd_signal, macd_hist = ta.macd(close, 12, 26, 9)
    stoch_k, stoch_d = ta.stochastic(high, low, close, 14, 3)
    
    # 3. Volatility Assessment
    atr = ta.atr(high, low, close, 14)
    bb_upper, bb_middle, bb_lower = ta.bbands(close, 20, 2)
    
    # 4. Volume Analysis
    obv = ta.obv(close, volume)
    cmf = ta.cmf(high, low, close, volume, 20)
    mfi = ta.mfi(high, low, close, volume, 14)
    
    # 5. Generate Signals
    signals = []
    
    for i in range(200, len(close)):  # Start after longest MA
        # Skip if any indicator is NaN
        if np.isnan([rsi[i], macd_line[i], atr[i], obv[i]]).any():
            signals.append("HOLD")
            continue
            
        # Entry conditions
        long_conditions = [
            close[i] > sma_200[i],  # Above 200 SMA
            close[i] > ema_20[i],   # Above 20 EMA
            st_direction[i] == -1,  # Supertrend bullish
            30 < rsi[i] < 70,       # RSI not extreme
            macd_line[i] > macd_signal[i],  # MACD bullish
            cmf[i] > 0,            # Positive money flow
            close[i] > bb_lower[i]  # Not at lower band
        ]
        
        short_conditions = [
            close[i] < sma_200[i],  # Below 200 SMA
            close[i] < ema_20[i],   # Below 20 EMA
            st_direction[i] == 1,   # Supertrend bearish
            30 < rsi[i] < 70,       # RSI not extreme
            macd_line[i] < macd_signal[i],  # MACD bearish
            cmf[i] < 0,            # Negative money flow
            close[i] < bb_upper[i]  # Not at upper band
        ]
        
        # Count satisfied conditions
        long_score = sum(long_conditions)
        short_score = sum(short_conditions)
        
        # Generate signal
        if long_score >= 6:
            signals.append("BUY")
        elif short_score >= 6:
            signals.append("SELL")
        else:
            signals.append("HOLD")
    
    # 6. Risk Management
    position_size = calculate_position_size(100000, 1, atr[-1])
    stop_loss = atr[-1] * 2
    take_profit = atr[-1] * 3
    
    return {
        'signal': signals[-1],
        'position_size': position_size,
        'stop_loss': stop_loss,
        'take_profit': take_profit,
        'indicators': {
            'trend': major_trend,
            'rsi': rsi[-1],
            'atr': atr[-1],
            'cmf': cmf[-1]
        }
    }

# Helper function
def calculate_position_size(capital, risk_percent, atr_value, atr_multiplier=2):
    risk_amount = capital * (risk_percent / 100)
    stop_distance = atr_value * atr_multiplier
    position_size = risk_amount / stop_distance
    return int(position_size)

# Example usage
if __name__ == "__main__":
    # Generate sample data
    n = 500
    np.random.seed(42)
    close = 100 + np.cumsum(np.random.randn(n) * 0.01)
    high = close + np.random.uniform(0, 2, n)
    low = close - np.random.uniform(0, 2, n)
    volume = np.random.randint(100000, 1000000, n)
    
    # Run trading system
    result = trading_system(high, low, close, volume)
    
    print(f"Current Signal: {result['signal']}")
    print(f"Position Size: {result['position_size']} shares")
    print(f"Stop Loss: ${result['stop_loss']:.2f}")
    print(f"Take Profit: ${result['take_profit']:.2f}")
    print(f"Market Conditions: {result['indicators']}")
```

---

This comprehensive guide covers all 100+ technical indicators in the OpenAlgo library with detailed explanations, parameters, and practical examples. Each indicator includes its mathematical foundation, use cases, and code demonstrations to help you implement professional trading strategies.