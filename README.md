# Production Planning and Lot Sizing Optimization 📦📈

## Overview
This project addresses a production planning problem for a factory using mixed-integer linear programming (MILP). The objective is to determine the optimal production plan for multiple products over a 24-month horizon, with the goal of satisfying demand at minimum production cost. In addition to the basic production model, several extensions were analyzed, including production capacity constraints, outsourcing options, minimum outsourcing quantities, and reducing warehouse capacity.

**Team:**  
Zoe Borrone, Luca Mazzarello, Ignacio Pardo and Olivier Saint Nom
**Project:** TP2 – Production Planning and Lot Sizing

## Problem Description
The production planning problem is defined as follows:
- **Products:** 5 products (labeled A, B, C, D, E)
- **Time Horizon:** 24 months (planning for two years)
- **Demand:** Estimated monthly demand for each product is provided (see Table 1 in the supplementary data file)
- **Production Constraints:**
  - Maximum production per product: 120 units per month.
  - Production must be done in lots of 10 units (possible production quantities: 0, 10, 20, …, 120).
- **Inventory Constraint:**  
  The total stock across all products cannot exceed 900 units.
- **Initial Condition:**  
  No initial inventory is available.
- **Production Cost:**  
  Each unit produced costs $370.

The primary goal is to minimize the total production cost while meeting the demand, using MILP.

## Model Formulation and Extensions
### 1. Basic Model
- **Decision Variables:**
  - `L[p,t]`: Number of lots produced for product *p* in month *t* (integer between 0 and 12).
  - `S[p,t]`: Inventory (stock) of product *p* at the end of month *t*.
- **Objective Function:**
  - Minimize total cost:  
    \[
    \text{Minimize } \sum_{p \in P}\sum_{t \in T} L[p,t] \times 10 \times 370
    \]
- **Constraints:**
  - **Stock Balance:**  
    For each product *p* and month *t*:
    \[
    S[p,t] = S[p,t-1] + L[p,t] \times 10 - D[t,p]
    \]
  - **Initial Stock:**  
    \( S[p,0] = 0 \) for all products.
  - **Inventory Capacity:**  
    For each month *t*:  
    \[
    \sum_{p \in P} S[p,t] \leq 900
    \]

### 2. Model with Production Capacity Limit
- **Additional Constraint:**  
  Total production across all products in each month must not exceed 300 units:
  \[
  \sum_{p \in P} \left( L[p,t] \times 10 \right) \leq 300 \quad \forall t \in T
  \]
- **Observation:**  
  Under this constraint, meeting total demand becomes infeasible.

### 3. Model with Outsourcing Option
- **New Variables:**
  - `F[p,t]`: Units outsourced (integer ≥ 0) for product *p* in month *t*.
  - `U[p,t]`: Units produced in-house (calculated as \( L[p,t] \times 10 \)).
- **Modified Objective Function:**
  \[
  \text{Minimize } \sum_{p \in P}\sum_{t \in T} \left( L[p,t] \times 10 \times 370 + F[p,t] \times 540 \right)
  \]
- **New Constraint:**  
  The total outsourced units in each month cannot exceed 200:
  \[
  \sum_{p \in P} F[p,t] \leq 200 \quad \forall t \in T
  \]
- **Requirement:**  
  All demand must be met in each month.

### 4. Model with Minimum Outsourcing Requirement
- **Additional Variable:**  
  - `ter[p,t]`: Binary variable indicating whether outsourcing is used (1) or not (0) for product *p* in month *t*.
- **New Constraints:**
  - If outsourcing is used, at least 20 units must be outsourced:
    \[
    F[p,t] \geq 20 \times ter[p,t] \quad \forall p, t
    \]
  - Total outsourced units constraint becomes:
    \[
    \sum_{p \in P} F[p,t] \leq 200 \times ter[p,t'] \quad \forall t \quad (\text{for any product } p')
    \]
  
### 5. Minimizing Warehouse Capacity
- **New Variable:**  
  - `stock_m`: Represents the minimum warehouse capacity required.
- **Objective Function:**  
  - Minimize `stock_m`.
- **Constraint:**  
  - Ensure that the inventory in each month does not exceed the chosen warehouse capacity:
    \[
    \sum_{p \in P} S[p,t] \leq stock_m \quad \forall t
    \]
- **Goal:**  
  Find the minimal warehouse capacity that still allows a feasible production plan.

## How to Run the Project
1. **Environment Setup:**
   - Create a new Python environment (e.g., using conda) and install required packages:
     ```bash
     conda create -n TP2 python=3.9
     conda activate TP2
     pip install -r requirements.txt
     ```
2. **Data Preparation:**
   - Ensure that the demand data is available (e.g., in `tp2.2023_fresco.dat` or similar) and properly formatted.
3. **Model Execution:**
   - Run the provided script (or Jupyter Notebook) to solve the MILP model using your preferred solver (e.g., CPLEX, Gurobi, or open-source alternatives like CBC).
4. **Review Results:**
   - The script will output the optimal production plan and the total production cost.
   - For the warehouse capacity extension, the script will compute the minimal capacity that ensures feasibility.

## Acknowledgements
We extend our sincere thanks to our instructors, peers, and mentors for their invaluable guidance and support throughout this project.

Happy optimizing and planning your production! 🚀📊
