---
title: 'Optimization & Demand Forecasting of NYC Taxi Services'
summary: 'A data-driven approach combining spatial-temporal analysis, Gravity Models for demand forecasting, and a Mixed-Integer Linear Program (MILP) to optimize taxi fleet allocation and maximize revenue.'
tags:
  - Operations Research
  - Mathematical Optimization
  - Machine Learning
  - Transportation Planning
date: "2024-12-01"

# Optional external URL for project (replaces project detail page).
external_link: ''

image:
  caption: 'NYC Taxi Transportation Planning'
  focal_point: Smart

links:
  - icon: brands/github
    icon_pack: fab
    name: Code & Models
    url: https://github.com/SobhanKasaei/Transportation-Planning-Project
---

## 🚖 Project Overview
This project focuses on analyzing, forecasting, and optimizing taxi services in New York City. The primary goal was to process massive historical trip data, predict future demand between zones, and formulate a mathematical optimization model to allocate the taxi fleet efficiently while maximizing total revenue.

---

## 🧹 Data Preprocessing & Exploratory Data Analysis (EDA)
The initial phase involved cleaning the 2021 NYC trip dataset. Invalid records (e.g., negative costs, zero-distance trips) were removed. Missing values for highly skewed features like `Tip Amount` and `Total Amount` were imputed using the **median** rather than the mean to avoid extreme outlier effects. Since the scope of the project was limited to working days, weekend data was filtered out.

### Spatial-Temporal Insights
A comprehensive Heatmap analysis of trip counts by hour and weekday allowed us to categorize the day into three distinct temporal segments:
* **00:00 - 06:00 (Midnight):** Lowest trip volume.
* **07:00 - 19:00 (Day):** Peak working hours with the highest trip density.
* **20:00 - 23:00 (Night):** Moderate trip volume, with a notable sustained demand on Friday nights.

The Origin-Destination (OD) matrix revealed that intra-zone trips dominated the dataset, particularly within **Manhattan, Brooklyn, and Queens**. Based on these insights, we recommended dynamic pricing models to balance supply and unpredictable demand surges effectively.

---

## 📈 Demand Forecasting: The Gravity Model
To predict the OD matrix for the upcoming year, we filtered the data to the top 25 high-traffic zones. We applied the **Gravity Model** and evaluated two distinct deterrence functions:

**1. Exponential Deterrence Function:**
$$f(c_{ij}) = \alpha \cdot exp(-\beta c_{ij})$$

**2. Log-normal Deterrence Function:**
$$f(c_{ij}) = \alpha \cdot exp(-\beta \cdot \ln(c_{ij}+1))$$

With parameters $\alpha = 1$ and $\beta = 0.08$, the **Exponential Model** significantly outperformed the Log-normal model. It achieved an $R^2$ of $0.85$ and an RMSE of $452.58$ (with only a $2.11\%$ total error). The exponential decay perfectly captured the real-world sensitivity of passengers: trip demand drops sharply and exponentially as travel costs increase.

---

## 🧮 Mathematical Programming Model
The core of the project is a linear programming model designed to maximize the transportation company's revenue, considering fixed costs, travel costs, and stochastic tip probabilities derived from a Machine Learning model developed in Phase I.

### Indices & Parameters
* $t$: Time period segment 
* $i, j$: Origin and destination zones 
* $d_{ijt}$: Forecasted demand from $i$ to $j$ in period $t$ 
* $c_{ijt}$: Average trip cost from $i$ to $j$ in period $t$ 
* $l_{ij}$: Distance between $i$ and $j$ 
* $r$: Probability of receiving a tip ($0.9734$) 
* $\delta_{ijt}$: Ratio of trips from $i$ to $j$ in $t$ that resulted in a tip (ML prediction) 
* $\pi$: Average tip amount ($\$5$ per trip) 
* $C$: Fixed cost per trip ($\$40$) 
* $K$: Maximum daily allowable distance constraint ($1000 \text{ km}$) 

### Decision Variables
* $x_{ijt}$: Number of trips executed from origin $i$ to destination $j$ in time period $t$.

### Objective Function
Maximize the total revenue, which includes fixed costs, expected tips, and trip-specific costs:
$$Max \sum_{t=1}^{T} \sum_{i} \sum_{j} x_{ijt} \cdot (C + \pi \cdot r \cdot \delta_{ijt} + c_{ijt})$$

### Constraints
**1. Daily Distance Limit:** The total distance covered by the fleet must not exceed the capacity limit $K$.
$$\sum_{t=1}^{T} \sum_{i} \sum_{j} x_{ijt} \cdot l_{ij} \le K$$

**2. Demand Satisfaction:** The number of executed trips cannot exceed the forecasted demand.
$$x_{ijt} \le d_{ijt}, \quad \forall i, j, t$$

**3. Non-negativity:**
$$x_{ijt} \ge 0, \quad \forall i, j, t$$

---

## 🚀 Implementation & Results
The mathematical model was implemented in **Python** using the `PuLP` optimization library. The solver yielded an optimal objective value of **$72,872.25$** monetary units. As expected, due to fleet capacity and market constraints, several decision variables were optimized to zero, guiding the system to strictly prioritize the most profitable and high-demand routes. 

*Note: All datasets, models, and optimization scripts (including `model.lp` and `modelresults.txt`) are available in the project repository.* ```
