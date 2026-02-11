## Core Concepts

### Capacity Factor (CF)

The capacity factor measures how much energy a renewable source actually produces compared to its maximum potential.

**Instantaneous capacity factor:** $$CF_t = \frac{\text{actual electrical energy output}}{\text{installed capacity} \cdot 1 \text{ hour}}$$

**Annual capacity factor:** $$\overline{CF} = \frac{\text{annual electrical energy output}}{\text{installed capacity} \cdot 8760 \text{ hours}}$$

**Key values to remember:**

- Solar PV: typically CFS ≈ 0.15 (15%)
- Onshore wind: typically CFW ≈ 0.25 (25%)

These values vary by location and technology.

---

## Mismatch Between Generation and Demand

### Definition

The **mismatch** at time t is the difference between renewable generation and electricity demand:

$$\Delta_t = g_t^W + g_t^S - d_t$$

where:

- $g_t^W$ = wind generation at time t
- $g_t^S$ = solar generation at time t
- $d_t$ = demand at time t

### Interpreting Mismatch

**When Δt > 0 (positive mismatch):**

- Renewable generation exceeds demand
- Excess energy must be curtailed (wasted) or stored
- This is **curtailed energy**

**When Δt < 0 (negative mismatch):**

- Demand exceeds renewable generation
- Shortfall must be met by backup generation or storage discharge
- This is **backup energy** needed

---

## Key Metrics for Balancing (Problem 1.3)

### 1. Backup Energy (EB)

Total energy that must be produced by dispatchable generators:

$$E_B = \sum_{t=0}^{8759} \Delta_t^-$$

where $\Delta_t^- = \min(0, \Delta_t)$ (only negative values)

### 2. Backup Capacity (CB)

Maximum power capacity needed from backup generators:

$$C_B = \max(|\Delta_t^-|)$$

This is the largest absolute value of negative mismatch.

### 3. Curtailed Energy (EC)

Total renewable energy that must be wasted:

$$E_C = \sum_{t=0}^{8759} \Delta_t^+$$

where $\Delta_t^+ = \max(0, \Delta_t)$ (only positive values)

---

## Important Parameters (Problem 1.3)

### Parameter γ (gamma)

Ratio of annual renewable generation to annual demand:

$$\gamma = \frac{\sum_t g_t^S + \sum_t g_t^W}{\sum_t d_t}$$

- γ = 1 means renewables produce exactly the annual demand (but not necessarily when needed!)
- γ < 1 means renewables undersupply
- γ > 1 means renewables oversupply annually

### Parameter α (alpha)

Average share of wind in total renewable generation:

$$\alpha = \frac{\sum_t g_t^W}{\sum_t g_t^S + \sum_t g_t^W}$$

- α = 0 means 100% solar, 0% wind
- α = 0.5 means 50% solar, 50% wind
- α = 1 means 0% solar, 100% wind

---

## Calculating Required Capacity (Problem 1.2)

### For Solar to Cover Load on Average

If demand is constant at D = 1 GW and you want solar to cover it on average:

$$\text{Average solar generation} = C_S \cdot \overline{CF_S}$$

For this to equal demand: $$C_S \cdot \overline{CF_S} = D$$

Therefore: $$C_S = \frac{D}{\overline{CF_S}} = \frac{1 \text{ GW}}{0.15} = 6.67 \text{ GW}$$

### For Wind to Cover Load on Average

Similarly: $$C_W = \frac{D}{\overline{CF_W}} = \frac{1 \text{ GW}}{0.25} = 4 \text{ GW}$$

---

## Storage Characteristics (Problem 1.2 parts c and d)

For a sinusoidal generation pattern $g_t = A + B\sin(\frac{2\pi}{T}t)$ with constant demand $d_t = A$:

### Mismatch Function

$$\Delta_t = g_t - d_t = B\sin(\frac{2\pi}{T}t)$$

### Storage Power Capacity

The maximum rate of charging or discharging: $$P_{storage} = B$$

This is the amplitude of the mismatch oscillation.

### Storage Energy Capacity

How much energy the storage must hold:

$$E_{storage} = \frac{BT}{\pi}$$

**Derivation:** $$E_r = \int_0^{T/2} B\sin(\frac{2\pi}{T}t)dt = \frac{BT}{2\pi}[-\cos(\frac{2\pi}{T} \cdot \frac{T}{2}) + \cos(0)] = \frac{BT}{\pi}$$

### Discharge Time

How long it takes to fully discharge the storage:

$$t_{discharge} = \frac{E_{storage}}{P_{storage}} = \frac{BT/\pi}{B} = \frac{T}{\pi}$$

### Numerical Examples

**For solar (T = 24 hours):**

- Discharge time = 24/π ≈ 7.6 hours

**For wind (T = 168 hours):**

- Discharge time = 168/π ≈ 53 hours

---

## Dominant Frequencies

Understanding the time scales of variability is crucial:

### Solar PV

- **Daily cycle (24 hours)**: Strong diurnal pattern (sun rises/sets)
- No generation at night
- Peak generation at midday

### Wind Power

- **Weekly cycle (168 hours)**: Weather systems typically last several days
- More variable than solar
- Can generate day and night
- Less predictable than solar

### Electricity Demand

- **Daily cycle**: Morning and evening peaks
- **Weekly cycle**: Weekday vs. weekend patterns
- **Seasonal cycle**: Higher in winter (heating) or summer (cooling)

---

## Data Analysis Techniques (Problem 1.1)

### 1. Time Series Plots

Plot capacity factors over different time periods:

- Two weeks in January
- Two weeks in July
- Full year

**What to look for:**

- Daily patterns (solar)
- Multi-day patterns (wind)
- Seasonal differences

### 2. Average Aggregation

Calculate and plot:

- Daily average capacity factors
- Weekly average capacity factors
- Monthly average capacity factors

**Method:**

```python
# Daily averages
daily_avg = df.groupby(df.index.date).mean()

# Weekly averages  
weekly_avg = df.resample('W').mean()

# Monthly averages
monthly_avg = df.resample('M').mean()
```

### 3. Fast Fourier Transform (FFT)

Identifies the dominant frequencies in the time series.

**Interpretation:**

- High power at 24-hour period → daily cycle
- High power at 168-hour period → weekly cycle
- Confirms visual observations from step 2

### 4. Duration Curve

Sort capacity factors from highest to lowest and plot.

**Purpose:**

- Shows how often high/low generation occurs
- Useful for calculating curtailment impact

**Curtailment calculation:**

```python
# Sort in descending order
sorted_cf = cf.sort_values(ascending=False)

# Energy lost if top 10 hours curtailed
energy_10h = sorted_cf.iloc[:10].sum()
total_energy = sorted_cf.sum()
percent_lost_10h = (energy_10h / total_energy) * 100
```

### 5. Ramp Analysis

Calculate hour-to-hour changes:

$$\text{ramp}_t = CF_t - CF_{t-1}$$

**In Python:**

```python
ramps = cf.diff()
```

**Interpretation:**

- Positive ramp: generation increasing
- Negative ramp: generation decreasing
- Larger absolute values = more variability
- Compare distributions for wind vs. solar

### 6. Interannual Variability

Calculate annual capacity factor for each year:

$$\overline{CF}_{year} = \frac{1}{8760}\sum_{t \in year} CF_t$$

**Then calculate:**

- Mean across all years: $\mu = \frac{1}{N}\sum_{i=1}^{N} \overline{CF}_i$
- Variance: $\sigma^2 = \frac{1}{N}\sum_{i=1}^{N} (\overline{CF}_i - \mu)^2$

---

## Regional Integration and Correlation (Problem 1.5)

### Pearson Correlation Coefficient

Measures how correlated generation is between two locations:

$$\rho_{g_{i},g_{j}} = \frac{\text{cov}(g_i^W, g_j^W)}{\sigma_{g_i^W} \cdot \sigma_{g_j^W}}$$

where:

- cov = covariance
- σ = standard deviation

### Correlation Length

The correlation typically decreases exponentially with distance:

$$\rho_{g_i,g_j} = \exp\left(-\frac{1}{\xi_c}d_{i,j}\right)$$

where:

- $d_{i,j}$ = distance between locations i and j
- $\xi_c$ = correlation length

**Interpretation:**

- Smaller ξc → generation decorrelates quickly with distance → need less transmission
- Larger ξc → generation stays correlated over long distances → need more transmission

**Typical values:**

- Wind: ξc ≈ 400-600 km
- Solar: ξc ≈ 100-200 km (decorrelates faster)

---

## Sinusoidal Approximations (Problems 1.2 and 1.3)

### Solar Generation Model

$$g_S(t) = C_S \cdot \overline{CF_S}(1 + \sin(\frac{2\pi}{24}t)) \text{ GW}$$

**Properties:**

- Period T = 24 hours (daily cycle)
- Oscillates around average $C_S \cdot \overline{CF_S}$
- Amplitude = average value
- Given: $\overline{CF_S} = 0.15$

### Wind Generation Model

$$g_W(t) = C_W \cdot \overline{CF_W}(1 + 0.9\sin(\frac{2\pi}{168}t)) \text{ GW}$$

**Properties:**

- Period T = 168 hours (weekly cycle)
- Oscillates around average $C_W \cdot \overline{CF_W}$
- Amplitude = 0.9 × average value
- Given: $\overline{CF_W} = 0.25$

### Why These Forms?

The factor of (1 + sin(...)) ensures:

- Generation ≥ 0 (sine varies from -1 to +1)
- Average over one period equals $C \cdot \overline{CF}$

**Verification for solar:** $$\frac{1}{24}\int_0^{24} C_S \cdot 0.15(1 + \sin(\frac{2\pi}{24}t))dt = C_S \cdot 0.15$$

The sine integral over a complete period is zero.

---

## Problem-Solving Workflow

### For Problem 1.1 (Data Analysis)

1. Load data using pandas
2. Select one country
3. Create time series plots (Jan, July, full year)
4. Calculate and plot aggregated averages (daily, weekly, monthly)
5. Perform FFT analysis
6. Create and analyze duration curves
7. Calculate and plot ramps
8. Analyze interannual variability
9. Repeat for demand data

### For Problem 1.2 (Analytical Calculations)

1. Calculate required solar capacity: $C_S = D/\overline{CF_S}$
2. Calculate required wind capacity: $C_W = D/\overline{CF_W}$
3. For solar storage:
    - Power capacity = amplitude of mismatch
    - Energy capacity = $\frac{BT}{\pi}$ with T=24
    - Discharge time = $T/\pi$
4. Repeat for wind storage with T=168

### For Problem 1.3 (Parametric Study)

1. Part (a): Use 0.5× capacities from 1.2, calculate EB, CB, EC
2. Part (b): Set γ=1, vary α from 0 to 1
    - For each α, calculate capacities CS and CW
    - Calculate time series gt = gtS + gtW
    - Calculate Δt = gt - dt
    - Calculate EB, CB, EC
    - Plot vs. α
3. Part (c): Vary both α (0 to 1) and γ (0 to 1)
    - Create 2D plots or contour plots
    - Identify optimal combinations

### For Problem 1.4 (Comparing Locations)

1. Download data for country and city
2. Create duration curves for both
3. Compare shapes
4. Discuss differences in resource quality and variability

### For Problem 1.5 (Correlation Analysis)

1. Load ERA5 data
2. Select a reference location
3. Calculate distances to all other grid cells
4. Calculate Pearson correlation coefficients
5. Plot correlation vs. distance
6. Fit exponential curve to find ξc
7. Repeat for solar irradiation

---

## Key Python Libraries

### For Problem 1.1

```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
from scipy import fft
```

**Essential pandas operations:**

- `pd.read_csv()` - load data
- `.groupby()` - aggregate data
- `.resample()` - time-based aggregation
- `.diff()` - calculate ramps
- `.sort_values()` - create duration curves

**Essential numpy/scipy operations:**

- `np.fft.fft()` - Fast Fourier Transform
- `np.abs()` - magnitude of FFT
- `np.mean()` - calculate averages
- `np.var()` - calculate variance

---

## Important Insights for Discussion

### Solar vs. Wind Balancing

- **Solar**: Predictable daily cycle, but zero at night
    - Requires daily storage (~8 hours)
    - Good match with daily demand patterns
- **Wind**: Less predictable, multi-day variability
    - Requires longer-duration storage (~50+ hours)
    - Can generate 24/7
    - Complements solar seasonally

### Optimal Mix

- Pure solar (α=0) or pure wind (α=1) typically requires MORE backup/storage
- Mixed systems benefit from:
    - Temporal complementarity (daily vs. weekly)
    - Seasonal complementarity (summer solar, winter wind)
    - Reduced backup energy and capacity

### Trade-offs

As γ increases (more renewables):

- Curtailed energy increases
- Backup energy decreases
- Backup capacity may not decrease proportionally
- System becomes more complex to balance

---

## Common Mistakes to Avoid

1. **Mixing up CF and generation**: CF is dimensionless (0-1), generation has units (GW or GWh)
    
2. **Forgetting time units**:
    
    - Make sure hours match (8760 hours per year, not 365 days)
    - t ranges from 0 to 8759, not 1 to 8760
3. **Sign conventions**:
    
    - Mismatch Δt = generation - demand
    - Backup energy uses negative mismatch
    - Curtailment uses positive mismatch
4. **Averaging**:
    
    - Annual CF ≠ average of hourly CFs (they're the same!)
    - Be careful with weighted averages
5. **Storage calculations**:
    
    - Energy capacity ≠ power capacity
    - Don't forget the π in $\frac{BT}{\pi}$

---

## Summary: Main Balancing Strategies

1. **Backup generation and curtailment**
    
    - Dispatchable generators cover shortfalls
    - Excess renewable energy is wasted
2. **Storage**
    
    - Moves energy in time
    - Requires both power and energy capacity
3. **Regional integration**
    
    - Moves energy in space
    - Exploits spatial decorrelation of wind/solar
4. **Demand-side management**
    
    - Shift flexible loads to match generation
5. **Sector coupling**
    
    - Use electricity for heat, transport, hydrogen
    - Adds demand but also flexibility

**The optimal solution combines all strategies through integrated optimization!**