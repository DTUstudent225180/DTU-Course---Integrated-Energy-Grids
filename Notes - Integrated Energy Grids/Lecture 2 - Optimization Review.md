## Why Optimization in Energy Systems?

From Lecture 1, we learned that balancing renewable generation requires combining multiple flexibility options:

- Backup generation and curtailment
- Storage
- Regional integration (transmission)
- Demand-side management
- Sector coupling

**Optimization determines the best combination of these options to minimize total system cost while meeting all physical constraints.**

---

## Standard Form of Optimization Problems

For consistency in this course, **we always formulate minimization problems** (not maximization).

### General Standard Form

**Minimize:** $f(x)$ (objective function)

**Subject to:**

- Equality constraints: $h_{i}(x) = c_{i}$ for all $i$
- Inequality constraints: $g_{j}(x) \ge d_{j}$ for all $j$

**Key terminology:**

- $f(x)$ = objective function (what we want to minimize)
- $x$ = decision variables (primary variables) - what we're optimizing
- $h_{i}(x) = c_{i}$ = equality constraints (must be satisfied exactly)
- $g_{j}(x) \ge d_{j}$ = inequality constraints (must be satisfied, allows slack)
- $\lambda_{i}$ = Lagrange multipliers for equality constraints (dual variables)
- $\mu_{j}$ = KKT multipliers for inequality constraints (dual variables)

---

## Converting Between Max and Min (Problem 2.1a)

**Rule:** Maximizing $f(x)$ is the same as minimizing $-f(x)$

**Example:**

Original: $\max_{x,y} 2x + 5y$

Converted: $\min_{x,y} (-2x - 5y)$

**For inequality constraints:** The standard form uses $g(x) \ge d$. Keep this direction when converting from max to min problems.

---

## The Lagrangian Function

The Lagrangian combines the objective function and constraints into a single function.

### For our standard form:

$$\mathcal{L}(x, \lambda, \mu) = f(x) - \sum_{i} \lambda_{i}[h_{i}(x) - c_{i}] - \sum_{j} \mu_{j}[g_{j}(x) - d_{j}]$$

**Sign convention note:** We use negative signs so that $\lambda_{i}$ represents positive sensitivity $\partial f/\partial c_{i}$.

### Finding the Optimum

To find the optimal solution, take partial derivatives and set them equal to zero:

- $\frac{\partial \mathcal{L}}{\partial x} = 0$ (stationarity)
- $\frac{\partial \mathcal{L}}{\partial \lambda_{i}} = 0$ (recovers equality constraints)
- $\frac{\partial \mathcal{L}}{\partial \mu_{j}} = 0$ (recovers inequality constraints)

This gives us the **first-order optimality conditions** (also called stationarity).

---

## KKT Conditions (Karush-Kuhn-Tucker)

For a point $x^{\ast}$ to be optimal, it must satisfy the KKT conditions:

### 1. Stationarity (First-Order Condition)

$$\frac{\partial f(x^{\ast})}{\partial x} - \sum_{i} \lambda_{i}^{\ast} \frac{\partial h_{i}(x^{\ast})}{\partial x} - \sum_{j} \mu_{j}^{\ast} \frac{\partial g_{j}(x^{\ast})}{\partial x} = 0$$

### 2. Primal Feasibility

Equality constraints: $h_{i}(x^{\ast}) = c_{i}$ for all $i$

Inequality constraints: $g_{j}(x^{\ast}) \ge d_{j}$ for all $j$

(The solution must satisfy all constraints)

### 3. Dual Feasibility

$$\mu_{j}^{\ast} \ge 0 \quad \text{for all } j$$

(KKT multipliers for inequality constraints must be non-negative)

### 4. Complementary Slackness

$$\mu_{j}^{\ast} (g_{j}(x^{\ast}) - d_{j}) = 0 \quad \text{for all } j$$

**This means:** Either the constraint is binding with $g_{j}(x^{\ast}) = d_{j}$ and $\mu_{j}^{\ast} \ge 0$, OR the constraint is not binding with $g_{j}(x^{\ast}) > d_{j}$ and $\mu_{j}^{\ast} = 0$.

---

## Binding vs Non-Binding Constraints (Problem 2.1b)

### Binding Constraint

- The constraint is **active** at the optimal solution
- $g_{j}(x^{\ast}) = d_{j}$ (constraint holds with equality)
- $\mu_{j}^{\ast} > 0$ (typically)
- Relaxing this constraint would improve the objective

### Non-Binding Constraint

- The constraint is **inactive** at the optimal solution
- $g_{j}(x^{\ast}) > d_{j}$ (constraint has slack)
- $\mu_{j}^{\ast} = 0$ (always)
- Relaxing this constraint has no effect on the solution

**How to identify binding constraints:**

1. Solve the optimization problem
2. Check which constraints have zero slack: $g_{j}(x^{\ast}) - d_{j} = 0$
3. Those are your binding constraints

### Understanding Complementary Slackness

From the complementary slackness condition: $\mu_{j}^{\ast} (g_{j}(x^{\ast}) - d_{j}) = 0$

Only two scenarios are possible:

**(a) Constraint is NOT binding:**

- Constraint has slack: $g_{j}(x^{\ast}) - d_{j} > 0$
- Therefore: $\mu_{j}^{\ast} = 0$
- No effect on objective: $\frac{\partial f(x^{\ast})}{\partial x} = 0$

**(b) Constraint IS binding:**

- Constraint is tight: $g_{j}(x^{\ast}) - d_{j} = 0$
- Therefore: $\mu_{j}^{\ast} > 0$ (can be positive)
- Objective gradient aligned with constraint

---

## Shadow Prices / Dual Variables

The Lagrange and KKT multipliers are called **shadow prices** because they represent the sensitivity of the optimal objective value to constraint changes:

For equality constraints: $\lambda_{i}^{\ast} \approx \frac{\partial f^{\ast}}{\partial c_{i}}$

For inequality constraints: $\mu_{j}^{\ast} \approx \frac{\partial f^{\ast}}{\partial d_{j}}$

**Interpretation:**

- If $\lambda^{\ast} = 50$ EUR/MWh for a demand constraint, increasing demand by 1 MWh increases total cost by approximately 50 EUR
- This is the marginal value of relaxing the constraint

**In energy systems:**

- The dual variable on the demand balance constraint equals the electricity price
- The dual variable on a capacity constraint equals the scarcity rent for that generator

---

## Economic Dispatch Problem

Economic dispatch finds the least-cost way to meet electricity demand using available generators.

### Problem Formulation

**Sets:**

- $s \in S$ = set of generators (solar, wind, gas, etc.)

**Parameters:**

- $o_{s}$ = variable cost of generator $s$ (EUR/MWh)
- $G_{s}$ = installed capacity of generator $s$ (MW)
- $CF_{s}$ = capacity factor of generator $s$ (dimensionless, 0 to 1)
- $d$ = electricity demand (MWh)

**Variables:**

- $g_{s}$ = generation from generator $s$ (MWh) - **this is what we optimize**

### Optimization Formulation

Minimize total generation cost:

$$\min_{g_{s}} \sum_{s} o_{s} g_{s}$$

Subject to:

**Supply equals demand:** $$\sum_{s} g_{s} = d$$ Associated dual variable: $\lambda$ (this becomes the electricity price)

**Non-negativity:** $$g_{s} \ge 0 \quad \text{for all } s$$ Associated dual variable: $\underline{\mu}_{s}$

**Capacity limit:** $$g_{s} \le G_{s} \cdot CF_{s} \quad \text{for all } s$$

Or in standard form: $-g_{s} + G_{s} \cdot CF_{s} \ge 0$

Associated dual variable: $\bar{\mu}_{s}$ (scarcity rent)

---

## Solving Economic Dispatch

### Lagrangian for Economic Dispatch

$$\mathcal{L} = \sum_{s} o_{s} g_{s} - \lambda\left(\sum_{s} g_{s} - d\right) - \sum_{s} \underline{\mu}_{s} g_{s} - \sum_{s} \bar{\mu}_{s}(-g_{s} + G_{s} \cdot CF_{s})$$

### First-Order Condition (Stationarity)

Taking $\frac{\partial \mathcal{L}}{\partial g_{s}} = 0$:

$$o_{s} - \lambda^{\ast} + \underline{\mu}_{s}^{\ast} + \bar{\mu}_{s}^{\ast} = 0$$

Rearranging: $$\lambda^{\ast} = o_{s} + \underline{\mu}_{s}^{\ast} + \bar{\mu}_{s}^{\ast}$$

**Interpretation:**

- $\lambda^{\ast}$ = electricity price (marginal cost of meeting demand)
- $o_{s}$ = variable cost of generator $s$
- $\bar{\mu}_{s}^{\ast}$ = scarcity rent when generator is at capacity limit
- The **most expensive generator that is not at its capacity limit sets the price**

---

## Merit Order / Economic Dispatch Logic

Generators are dispatched in order of increasing marginal cost (merit order):

**Typical merit order:**

1. **Renewables (wind, solar):** $o_{s} = 0$ EUR/MWh → dispatch first
2. **Nuclear:** $o_{s} \approx 10$ EUR/MWh → dispatch second
3. **Coal:** $o_{s} \approx 40$ EUR/MWh → dispatch third
4. **Gas:** $o_{s} \approx 60$ EUR/MWh → dispatch last

**Algorithm:**

1. Sort generators by variable cost (lowest to highest)
2. Dispatch generators in order until demand is met
3. The marginal generator (last one needed) sets the price

**Example calculation for Problem 2.2:**

Given:

- Solar: 15 MW capacity, CF = 0.17, cost = 0 EUR/MWh → Available: 15 × 0.17 = 2.55 MW
- Wind: 20 MW capacity, CF = 0.33, cost = 0 EUR/MWh → Available: 20 × 0.33 = 6.6 MW
- Gas: 20 MW capacity, CF = 1.0, cost = 60 EUR/MWh → Available: 20 MW
- Demand: 13.2 MWh

**Solution:**

1. Dispatch all solar: 2.55 MW
2. Dispatch all wind: 6.6 MW
3. Remaining demand: 13.2 - 2.55 - 6.6 = 4.05 MW
4. Dispatch gas: 4.05 MW
5. **Price = 60 EUR/MWh** (gas is marginal)

---

## Curtailment

**Curtailment** = wasted renewable energy (generation that could be produced but isn't used)

$$\text{Curtailment}_{s} = G_{s} \cdot CF_{s} - g_{s}^{\ast}$$

**When does curtailment occur?**

- When renewable generation exceeds demand
- When there's excess supply (all demand met, storage full, no export possible)

**In Problem 2.2d:** Calculate curtailment for each time period where renewables are constrained.

---

## Duration Curves (Problem 2.2e)

A **duration curve** shows values sorted from highest to lowest.

**Purpose:**

- Visualize how often high/low generation occurs
- Identify periods of excess/shortage
- Assess storage and transmission needs

**Net load duration curve:** $$\text{Net load}_{t} = d_{t} - (\text{solar}_{t} + \text{wind}_{t})$$

Sorted from highest to lowest shows:

- **High net load** → need backup generation or imports
- **Negative net load** → excess renewables, need storage or exports

---

## Adding Imports to Economic Dispatch (Problem 2.3)

Imports are treated as an additional generator with:

- Variable capacity (changes over time based on neighboring system)
- Variable price (dual variable from neighboring system's optimization)

### Updated Formulation

Add imports to the generator set:

- $s = {\text{solar, wind, gas, import}}$

For imports:

- $o_{\text{import},t}$ = price from neighboring system at time $t$ (from their $\lambda^{\ast}$)
- $G_{\text{import},t}$ = available import capacity at time $t$

The optimization now includes imports in the merit order, dispatching them when their price is competitive.

---

## Practical Implementation with Linopy (Problem 2.2)

### Basic Workflow

```python
import linopy
import pandas as pd

# 1. Create model
m = linopy.Model()

# 2. Define variables
g = m.add_variables(lower=0, name="generation")

# 3. Define objective
m.add_objective((costs * g).sum())

# 4. Add constraints
m.add_constraints(g.sum() == demand, name="supply_demand")
m.add_constraints(g <= capacity, name="generation_limit")

# 5. Solve
m.solve(solver_name='highs')

# 6. Extract results
generation = g.solution
price = m.constraints["supply_demand"].dual  # This is λ*
scarcity_rent = m.constraints["generation_limit"].dual  # This is μ*
total_cost = m.objective.value
```

### Key Linopy Concepts

**Variables:**

- `m.add_variables()` creates optimization variables
- `lower=0` enforces non-negativity
- Can specify dimensions for time series

**Objective:**

- `m.add_objective()` sets what to minimize
- Use `.sum()` to aggregate over dimensions

**Constraints:**

- `m.add_constraints()` adds constraints
- Use `==` for equality, `<=` or `>=` for inequality
- Name constraints to extract dual variables later

**Dual Variables:**

- `.dual` attribute on constraints gives $\lambda$ or $\mu$
- For supply-demand constraint: dual = electricity price
- For capacity constraints: dual = scarcity rent

---

## Key Results to Extract (Problem 2.2d)

### 1. Share of Renewable Generation

$$\text{Renewable share} = \frac{g_{\text{solar}} + g_{\text{wind}}}{d}$$

### 2. Curtailment

$$\text{Curtailment}_{s} = (G_{s} \cdot CF_{s} - g_{s}^{\ast}) \text{ if } g_{s}^{\ast} < G_{s} \cdot CF_{s}$$

### 3. Dual Variables (Prices)

- Extract from `m.constraints["supply_demand"].dual`
- Statistics: mean, median, min, max

### 4. Total System Cost

- `m.objective.value`

### Time Series Analysis

```python
# Calculate for each time step
results_df = pd.DataFrame({
    'demand': demand,
    'solar_gen': g_solar.solution,
    'wind_gen': g_wind.solution,
    'gas_gen': g_gas.solution,
    'price': dual_values,
    'renewable_share': (g_solar.solution + g_wind.solution) / demand
})

# Summary statistics
print(results_df.describe())
```

---

## Convexity (Brief Overview)

**Why it matters:** Convex optimization problems are guaranteed to have a global optimum and can be solved efficiently.

### Convex Function

A function $f(x)$ is convex if it lies above all its tangent lines: $$f(x_{2}) \ge f(x_{1}) + \frac{\partial f(x_{1})}{\partial x} \cdot (x_{2} - x_{1})$$

Graphically: "bowl-shaped" - no local minima, only one global minimum.

### Convex Optimization Problem

An optimization problem is convex if:

1. Objective function $f(x)$ is convex
2. Equality constraints $h_{i}(x)$ are affine (linear + constant)
3. Inequality constraints $g_{j}(x)$ are convex

**Economic dispatch is a linear program (LP)** → convex → guaranteed global optimum!

---

## Problem-Solving Strategy

### Problem 2.1 (Graphical)

1. Plot all constraints on x-y axes
2. Shade feasible region (satisfies all constraints)
3. Plot objective function contours
4. Find corner of feasible region with best objective value
5. Check which constraints are active (binding) at that point
6. Calculate $\lambda$ and $\mu$ using KKT conditions

### Problem 2.2 (Linopy Economic Dispatch)

1. Load data (capacity factors, demand, costs)
2. Set up linopy model with generation variables
3. Add supply-demand constraint
4. Add capacity constraints for each generator
5. Solve and extract:
    - Generation levels
    - Electricity prices (dual of supply-demand)
    - Curtailment (available - generated for renewables)
6. Repeat for different data scenarios (a, b, c)
7. Compare statistics across scenarios

### Problem 2.3 (Adding Imports)

1. Load import availability and prices from neighbor's dispatch
2. Add import as new generator in model
3. Set capacity = available import for each time step
4. Set cost = neighbor's price for each time step
5. Re-solve with imports included
6. Compare results with and without imports

---

## Common Mistakes to Avoid

1. **Wrong sign in Lagrangian:** Make sure you understand the sign convention being used
    
2. **Forgetting complementary slackness:** If a constraint is not binding, its dual must be zero
    
3. **Mixing up primal and dual:**
    
    - Primal = decision variables (like $g_{s}$)
    - Dual = shadow prices (like $\lambda$, $\mu$)
4. **Not checking units:**
    
    - Capacity (MW) vs Energy (MWh)
    - Must multiply by time (hours) to convert
5. **Forgetting capacity factors:** For renewables, available capacity = $G_{s} \cdot CF_{s}$, not just $G_{s}$
    
6. **Extracting wrong dual variables:** Make sure you're getting duals from the right constraint name
    

---

## Summary: Key Formulas

**Standard form:**

Minimize $f(x)$ subject to $h_{i}(x) = c_{i}$ and $g_{j}(x) \ge d_{j}$

**Lagrangian:**

$$\mathcal{L}(x, \lambda, \mu) = f(x) - \sum_{i} \lambda_{i}[h_{i}(x) - c_{i}] - \sum_{j} \mu_{j}[g_{j}(x) - d_{j}]$$

**Stationarity:**

$$\frac{\partial \mathcal{L}}{\partial x} = 0$$

**Complementary slackness:**

$$\mu_{j}^{\ast}(g_{j}(x^{\ast}) - d_{j}) = 0$$

**Economic dispatch objective:**

$$\min \sum_{s} o_{s} g_{s}$$

**Supply-demand balance:**

$$\sum_{s} g_{s} = d \quad (\lambda = \text{price})$$

**Curtailment:**

$$\text{Curtailment}_{s} = G_{s} \cdot CF_{s} - g_{s}^{\ast}$$