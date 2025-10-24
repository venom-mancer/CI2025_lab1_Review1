# Knapsack Notebook Review

## Weaknesses

1. **Solution model & feasibility checks**
   - Representation (`-1` or knapsack index) is fine, but feasibility is recomputed from scratch every time. This slows evaluation.
   - No *repair* step for slightly infeasible tweaks; potentially good states are discarded.

2. **Neighborhood design (local search power)**
   - Hill Climbing explores only single-item tweaks. Missing:
     - Pairwise swaps between knapsacks
     - Composite moves (move + insert)
     - Value/weight-density guidance
   - In multi-dimensional cases, such limited neighborhoods easily stall on local optima.

3. **Simulated Annealing tuning**
   - Simplistic temperature schedule (`temp *= 0.99`), no calibration.
   - No restarts or reheating; acceptance unscaled to fitness range.

4. **Objective & constraints**
   - Fitness = total value only; no penalty function to allow exploration through mild infeasibility.

5. **Performance**
   - Frequent `weights[solution == kp].sum(axis=0)` calls are costly.
   - Incremental updates per move (O(D)) would be faster than full recomputation (O(N·D)).

6. **Evaluation & reporting**
   - Missing per-dimension usage summaries, per-knapsack totals, or multi-run statistics.
   - Lacks small test cases with known optima for validation.

---

## Suggested Improvements

### A. Core algorithmic upgrades

1. **Add reproducibility**
   - Set a fixed seed: `np.random.seed(42)`.

2. **Use incremental feasibility & objective tracking**
   - Maintain `used[k, :]` and total value. Update only changed parts after each move.

3. **Expand neighborhood**
   - Add pairwise swaps and composite moves.
   - Include first-improvement option for speed on large instances.

4. **Guided heuristics**
   - Order candidate items by value/normalized weight density relative to remaining capacity.

5. **Parameterization**
   - Make steps, temperature, seed, and neighborhood size configurable at the top.

6. **Reporting**
   - Print per-knapsack totals, per-dimension usage, selected items, and global best.
   - Add fitness-over-time plots comparing SA vs. HC.

---

## Overall Evaluation

**Strengths**
- Clean, understandable code.
- Good use of classic metaheuristics (HC and SA).
- Simple experiment structure with logging.

**Limitations**
- Narrow neighborhood → limited exploration.
- Weak cooling schedule in SA → behaves like HC.
- No reproducibility and heavy recomputation.
- Sparse reporting and diagnostics.

