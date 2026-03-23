---
title: 'Causal Inference in Econometrics: The Impact of Income Inequality on Crime Rates'
summary: 'An advanced panel data econometric study investigating the causal impact of income inequality on crime rates across U.S. states using 2SLS regression and Shift-Share Instrumental Variables.'
tags:
  - Econometrics
  - Causal Inference
  - Panel Data
  - Stata & Python
  - Instrumental Variables (IV)
date: '2025-02-01'

image:
  caption: 'Conceptual econometric visualization created via Google Gemini'
  focal_point: 'Smart'
  filename: 'featured.png'
---

## Project Overview
This comprehensive econometric research rigorously investigates the causal relationship between **Income Inequality** and various crime rates across 50 U.S. states over a 9-year period (2010 to 2018). While economic inequality is theoretically considered a significant catalyst for social tension and crime, establishing definitive **causality** in empirical research is notoriously difficult due to **reverse causality** and **omitted variable bias (OVB)**.

To overcome these endogeneity challenges, this study employs an advanced **Fixed Effects Panel Data** methodology coupled with a **Two-Stage Least Squares (2SLS) Instrumental Variable (IV)** approach. The instrument utilizes predicted income inequality based on national income growth rates (a Shift-Share/Bartik-style approach) to isolate exogenous variations in local inequality.

---

## Theoretical Framework: The Economics of Crime
The theoretical foundation of this empirical study is rooted in Gary Becker’s (1968) seminal economic model of crime. In this framework, a potential criminal is modeled as a rational economic agent who maximizes utility by weighing the expected costs and benefits of legal versus illegal activities.

The Expected Utility ($\mathbb{E}[U]$) of committing a crime is formalized as:
$$ \mathbb{E}[U] = p \cdot U(Y - f) + (1 - p) \cdot U(Y + G) $$

Where:
* $p$: The probability of being apprehended and punished.
* $f$: The monetary equivalent of the punishment (cost of crime).
* $Y$: The individual's current legal income or wealth.
* $G$: The illegal payoff or gain from the crime.
* $U(\cdot)$: The individual's utility function.

A rational individual will choose to engage in criminal activity only if the expected utility of the crime exceeds the utility derived from legal employment ($W$):
$$ \mathbb{E}[U] > U(W) $$

**The Mathematical Link to Inequality:** Widening income inequality directly exacerbates this inequality condition. In highly unequal societies, the legal wage ($W$) for lower-income deciles is suppressed, while the presence of high-income individuals substantially increases the potential illegal payoff ($G$). Thus, the income gap fundamentally alters the incentive structure, driving up property and violent crimes.

---

## Data & Variables
This study leverages a balanced panel dataset encompassing 50 U.S. states from 2010 to 2018. Extensive data preprocessing, cleaning, and merging were executed using **Python (Pandas)** (`Cleaning.ipynb`).

* **Dependent Variables (Crime Rates):** The natural logarithm of crime rates (per 100,000 inhabitants) sourced from the FBI Uniform Crime Reporting (UCR) program. The analysis covers 7 distinct categories:
  1. Total Violent Crime
  2. Total Property Crime
  3. Robbery
  4. Aggravated Assault
  5. Burglary
  6. Larceny
  7. Motor Vehicle Theft
* **Independent Variable:** Income Inequality ($Ineq_{it}$), calculated as the ratio of income shares between top and bottom deciles/quintiles.
* **Control Variables:** To mitigate omitted variable bias, key demographic controls were included: Share of males aged 15-24 (`share_of_male_aged_15_24`), percentage of foreign-born population (`foreign_born`), and the percentage of male householders with no spouse/partner present (`male_householder_no_spouse_partn`).

---

## Econometric Methodology & Modeling
The econometric estimations were performed using **Stata** (`Project.do`). To account for heteroskedasticity and within-state serial correlation, all models utilized **Clustered Standard Errors** at the state level (`vce(cluster id)`).

### 1. The Baseline Panel Data Model (Fixed Effects)
Initially, to control for unobserved, time-invariant state characteristics (e.g., local culture, strictness of state laws) and macroeconomic shocks affecting all states simultaneously, a Two-Way Fixed Effects (FE) model was specified:

$$ \ln(Crime_{it}) = \beta_0 + \beta_1 Ineq_{it} + \mathbf{X}_{it}^\prime \gamma + \alpha_i + \delta_t + \epsilon_{it} $$

Where:
* $i$ denotes the state and $t$ denotes the year.
* $\mathbf{X}_{it}$ is the vector of demographic control variables.
* $\alpha_i$ represents State Fixed Effects.
* $\delta_t$ represents Time Fixed Effects.

### 2. The Endogeneity Problem & Instrumental Variable (IV)
Estimating the baseline model via Ordinary Least Squares (OLS) or standard FE yields biased and inconsistent coefficients due to **Reverse Causality** ($Cov(Ineq_{it}, \epsilon_{it}) \neq 0$). High crime rates can drive wealthy residents and businesses out of an area (capital flight), which artificially alters the local income distribution and inequality metrics.

To resolve this, a **Shift-Share Instrumental Variable** (`predicted_ratio_it`) was constructed. This instrument interacts initial state-level income distributions with national-level income growth rates. It satisfies two critical IV conditions:
1. **Relevance:** It is highly correlated with the actual observed inequality.
2. **Exogeneity:** It is driven by national trends and is orthogonal to local state-level crime shocks.

### 3. Two-Stage Least Squares (2SLS) Estimation
To extract the pure causal effect, a 2SLS approach was implemented using Stata's `xtivreg` and `ivregress 2sls` commands:

**First Stage:** Regressing the endogenous inequality variable on the exogenous instrument and controls:
$$ Ineq_{it} = \pi_0 + \pi_1 \widehat{Ratio}_{it} + \mathbf{X}_{it}^\prime \theta + \alpha_i + \delta_t + \nu_{it} $$

**Second Stage:** Regressing the crime rates on the predicted inequality ($\widehat{Ineq}_{it}$) obtained from the first stage:
$$ \ln(Crime_{it}) = \beta_0 + \beta_{IV} \widehat{Ineq}_{it} + \mathbf{X}_{it}^\prime \gamma + \alpha_i + \delta_t + \eta_{it} $$

---

## Empirical Results & Analysis

1. **Validity of the Instrument (First Stage Strength):** The first-stage regression outputs demonstrated a highly significant, positive relationship between the predicted instrument and actual inequality. Crucially, the F-statistic robustly exceeded the standard threshold ($F > 10$), confirming the absence of a "weak instrument" problem.

2. **Direction of the OLS Bias:** Comparing the $\beta_{OLS}$ and $\beta_{IV}$ coefficients revealed that standard OLS estimations severely **underestimate** the true impact of inequality on crime. The IV approach proved that once reverse causality is eliminated, the detrimental effect of the income gap on crime is substantially larger than simple correlations suggest.

3. **Causal Impact on Specific Crime Categories:**
   * **Violent & Property Crimes:** The 2SLS regressions indicated a statistically significant (at the 1% level) and positive causal relationship between income inequality and the logarithmic rates of both total property and violent crimes.
   * **Highest Elasticities:** The most profound causal impacts were observed in **Robbery** and **Aggravated Assault**. This empirical finding strongly validates the Becker theoretical model, indicating that visible, extreme disparities in wealth directly incentivize confrontational and financially motivated crimes.

---

## Conclusion
Through rigorous causal identification strategies, this econometric study proves that income inequality is not merely a correlational byproduct of high-crime areas, but a **direct causal driver** of criminal activity. From a public policy perspective, these findings imply that economic interventions aimed at wealth redistribution, closing the income gap, and supporting vulnerable demographics are not just matters of social welfare, but serve as highly effective, preventative anti-crime measures.

---
### Econometric Outputs & Visualizations

{{< figure src="first-stage-fstat.png" title="First Stage Regression Results" caption="Stata output demonstrating the strength and validity of the instrumental variable (Relevance condition)." >}}

{{< figure src="2sls-results.png" title="2SLS Instrumental Variable Estimation" caption="Second-stage FE-IV regression (xtivreg) output showing the causal impact of inequality on violent crime rates." >}}
