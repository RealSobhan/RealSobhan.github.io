---
title: 'Optimization & Demand Forecasting of NYC Taxi Services'
summary: 'An end-to-end data science and operations research project. It combines Machine Learning (RF, XGBoost) for demand and tip forecasting with a Gravity Model for spatial distribution, concluding with a MILP to optimize taxi fleet allocation.'
tags:
  - Operations Research
  - Mathematical Optimization
  - Machine Learning
  - Transportation Planning
date: "2024-12-01"

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

## Project Overview & Philosophy
Urban mobility is a highly stochastic environment. Fleet managers face a dual challenge: predicting where and when passengers will need rides, and strategically positioning vehicles to maximize revenue while minimizing idle travel time. 

This project tackles these challenges through a two-phase methodology:
1. **Phase I (Predictive Modeling):** Leveraging Machine Learning to forecast hourly zone-level demand and the probability of receiving tips.
2. **Phase II (Prescriptive Analytics):** Using Gravity Models to distribute the forecasted demand spatially, and formulating a Mixed-Integer Linear Program (MILP) to allocate the taxi fleet optimally based on the ML predictions.

---

## Phase I: Machine Learning & Predictive Modeling
To formulate a robust mathematical model in Phase II, we first needed deterministic parameters to represent uncertain real-world variables. 

* **Demand Forecasting:** We engineered spatial-temporal features from multi-year NYC taxi trip data. Advanced ensemble learning models, specifically **Random Forest (RF)** and **XGBoost**, were trained to forecast the hourly trip demand for each zone. 
* **Tip Probability Classification:** In the real world, tips constitute a significant portion of a driver's revenue, but they are highly uncertain. We trained a predictive model to determine $\delta_{ijt}$, which represents the ratio of trips from origin $i$ to destination $j$ at time $t$ that are likely to yield a tip. This critical ML output bridges the gap between raw data and the objective function of our optimization model.

---

## Phase II: Data Preprocessing & Exploratory Data Analysis (EDA)
The dataset utilized was the 2021 NYC Green Taxi trip records. Data cleaning is not just about removing errors; it's about preparing the data to reflect realistic operational constraints.

* **Outlier Handling & Imputation:** Invalid records (e.g., negative costs, zero-distance trips) were dropped. When analyzing financial variables like `Tip Amount` and `Total Amount`, the histograms revealed a heavily right-skewed distribution with a long tail. Therefore, we used the **median** rather than the mean to impute missing values, preventing extreme outliers from skewing our cost matrices.
* **Temporal Discretization:** To make the mathematical model computationally tractable, we used Heatmap analysis to cluster continuous hours into three distinct operational shifts:
  * **Midnight (00:00 - 06:00):** Lowest demand.
  * **Day (07:00 - 19:00):** Peak working hours requiring maximum fleet capacity.
  * **Night (20:00 - 23:00):** Moderate volume, with specific anomalies (e.g., sustained high demand on Friday nights due to weekend eve activities).
* **Spatial Analysis:** The Origin-Destination (OD) matrix showed that the vast majority of trips are intra-zone (specifically within Manhattan, Brooklyn, and Queens). This insight justified our recommendation for localized taxi hubs rather than city-wide random roaming.

---

## Spatial Distribution: The Gravity Model
While ML predicts *how many* trips will originate from a zone, the **Gravity Model** predicts *where* those trips will go. Inspired by Newton's law of gravitation, this model assumes that trips between two zones increase with their "mass" (total trips generated/attracted) and decrease with the "distance" or "cost" between them.

We evaluated two deterrence functions for the top 25 high-traffic zones:

**1. Exponential Deterrence Function:**
$$f(c_{ij}) = \alpha \cdot exp(-\beta c_{ij})$$

**2. Log-normal Deterrence Function:**
$$f(c_{ij}) = \alpha \cdot exp(-\beta \cdot \ln(c_{ij}+1))$$

**Why the Exponential Model Won:** Through iterative calibration ($\alpha = 1$, $\beta = 0.08$), the Exponential Model achieved an outstanding $R^2$ of $0.85$ and a minimal total error of $2.11\%$. Behaviorally, this makes sense: the exponential decay perfectly captures the sharp, non-linear drop in passenger willingness to travel as costs or distances increase. The Log-normal model was too smooth to capture this real-world friction.

---

## Operations Research: The Mathematical Programming Model
The final stage integrates all previous insights into a deterministic Linear Programming model to optimize fleet routing and maximize the company's expected revenue.

### Mathematical Formulation
Let $x_{ijt}$ be our primary decision variable: the number of trips executed from origin $i$ to destination $j$ during time period $t$.

**The Objective Function:**
$$Max \sum_{t=1}^{T} \sum_{i} \sum_{j} x_{ijt} \cdot (C + \pi \cdot r \cdot \delta_{ijt} + c_{ijt})$$
*Why this structure?* The revenue per trip is not just a static fare. It is the sum of a fixed base cost ($C$), the average travel cost ($c_{ijt}$), and the Expected Value of the tip ($\pi \cdot r \cdot \delta_{ijt}$). Here, $\pi$ is the average tip amount ($\$5$), $r$ is the baseline probability of receiving a tip ($0.9734$), and $\delta_{ijt}$ is the specific tip ratio predicted by our Machine Learning model in Phase I.

**The Constraints:**
**1. Daily Distance Capacity Limit:** The total distance ($l_{ij}$) covered by the entire fleet must not exceed the physical or contractual capacity limit $K$ ($1000 \text{ km}$).
$$\sum_{t=1}^{T} \sum_{i} \sum_{j} x_{ijt} \cdot l_{ij} \le K$$

**2. Demand Satisfaction (Upper Bound):** We cannot serve more trips than the market demands. The allocated trips must be less than or equal to the forecasted demand ($d_{ijt}$) derived from the Gravity Model.
$$x_{ijt} \le d_{ijt}, \quad \forall i, j, t$$

**3. Logical Non-negativity:**
$$x_{ijt} \ge 0, \quad \forall i, j, t$$

---

## Optimization Results & Strategic Insights
The model was implemented and solved using the `PuLP` library in Python. The optimal solution yielded a maximized objective value of **$72,872.25$**. 

**Key Business Takeaways:**
1. **Strategic Scarcity:** The solver aggressively optimized several decision variables ($x_{ijt}$) to zero. Because fleet capacity ($K$) is limited, the model correctly identified and ignored low-margin or extremely long-distance routes, strictly prioritizing high-yield, short-distance intra-zone trips.
2. **Dynamic Pricing Recommendation:** During peak hours (07:00 - 19:00), the forecasted demand consistently outstrips fleet capacity. We recommended implementing dynamic (surge) pricing. This not only filters out low-priority trips but also incentivizes more drivers to enter the network during critical hours.

*Note: All datasets, spatial-temporal analysis notebooks, and optimization scripts (`model.lp`, `modelresults.txt`) are available in the attached GitHub repository.*
