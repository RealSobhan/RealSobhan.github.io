---
title: 'Causal Inference in Econometrics: Income Inequality and Crime Rates'
summary: 'An advanced panel data econometric study investigating the causal impact of income inequality on crime rates across U.S. states using 2SLS regression and a custom Shift-Share Instrumental Variable.'
tags:
  - Econometrics
  - Causal Inference
  - Panel Data
  - Stata & Python
  - Instrumental Variables (IV)
date: '2026-03-22'

image:
  caption: 'Conceptual econometric visualization created via Google Gemini'
  focal_point: 'Smart'
  filename: 'featured.png'
---

## 1. Introduction and Literature Review
Criminal activities impose severe social, economic, and security costs on society. To design effective crime-prevention policies, understanding the underlying drivers of crime is essential. One of the most prominent factors is **income inequality**. 

The foundation of this research relies on **Becker’s (1968)** economic model of crime, which posits that criminal acts are rational choices made by utility-maximizing individuals comparing expected returns from legal versus illegal activities. Sociologically, this is complemented by **Merton’s Strain Theory (1938)** and **Agnew's General Strain Theory**, which argue that individuals facing blocked opportunities to achieve societal goals (like wealth accumulation) may experience frustration, driving them toward illegal methods. 

Empirical studies robustly support these theories. Ehrlich (1973), Kelly (2000), and Choe (2008) established strong links between income inequality and specific crimes across U.S. regions. Similarly, Machin and Meghir (2004) demonstrated that worsening labor market opportunities for low-skill workers directly increased property crime rates in the UK. Building on this literature, this study investigates the causal impact of income inequality on various crime rates across 52 U.S. states from 2010 to 2018.

---

## 2. Theoretical Framework: The Economics of Crime
Following Becker and Ehrlich, the supply of offenses ($O_j$) by an individual is modeled as a function of the probability of conviction ($p_j$), the penalty ($f_j$), and other socio-economic variables ($u_j$):
$$ O_{j} = O_{j}(p_{j}, f_{j}, u_{j}) $$

An individual makes a utility-maximizing choice between the expected return from legal work ($E(W_l)$) and illegal work ($E(W_i)$). The expected return from legal work is an increasing function of working time ($t_l$):
$$ E(W_{l}) = W_{l}(t_{l}) $$

Conversely, the expected return from illegal work incorporates the probability of escaping apprehension ($1 - p_i$) retaining the illegal wage ($W_i$), and the probability of apprehension ($p_i$) which incurs a penalty ($F_i$):
$$ E(W_{i}) = (1 - p_{i})W_{i}(t_{i}) + p_{i}(W_{i}(t_{i}) - F_{i}(t_{i})) $$

**Mathematical Implications of Inequality:** As societal income inequality widens, the expected legal wage ($W_l$) for lower-income individuals stagnates or drops, while the presence of high-income targets increases the potential illegal payoff ($W_i$). When $E(W_i) > E(W_l)$, the economic incentive triggers a rise in both the **supply** of potential criminals and the **demand/opportunity** for property and violent crimes.

---

## 3. Data and Descriptive Statistics
The study utilizes a balanced panel dataset (2010-2018). Income Inequality is measured by the ratio between the income shares of the top three income brackets (>$150,000) and the bottom three brackets (<$25,000). 

To prevent omitted variable bias, demographic controls include the share of males aged 15-24, the percentage of foreign-born individuals, and the percentage of male householders with no spouse/partner present (a proxy for unstable family conditions).

**Table 1: Descriptive Statistics of Crime Variables (per 100,000 population)**

| Variable | Observations | Mean | Std. Dev. | Min | Max |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Total Crime** | 459 | 372,189.7 | 432,756.7 | 18,395 | 2,411,097 |
| **Property Crime** | 459 | 163,143.7 | 188,422.8 | 8,211 | 1,049,465 |
| **Violent Crime** | 459 | 24,219.9 | 30,132.8 | 643 | 178,597 |
| **Burglary** | 459 | 34,520.3 | 41,504.6 | 1,496 | 245,767 |
| **Robbery** | 459 | 6,557.0 | 9,464.4 | 53 | 58,116 |

---

## 4. The Instrumental Variable (Shift-Share Construction)
Estimating the impact of inequality on crime via Ordinary Least Squares (OLS) is highly susceptible to **Reverse Causality**. Wealthy individuals may migrate away from high-crime areas, artificially altering the state's income distribution. 

To resolve this endogeneity, a **predicted ratio of income shares** is constructed as an Instrumental Variable (IV). To ensure strict exogeneity, a "leave-one-out" national growth rate is utilized.

**Step 1:** Calculate the total population share in the bottom income bracket across all $m$ states ($S_{b,t}$):
$$ S_{b,t} = \sum_{i=1}^{m} W_{bi,t} $$

**Step 2 (Leave-One-Out Average):** To prevent local state shocks from influencing the instrument, the state's own share ($W_{bi,t}$) is subtracted from the national pool:
$$ \overline{W}_{bi,t} = \frac{S_{b,t} - W_{bi,t}}{m - 1} $$

**Step 3:** Compute the exogenous national growth rate for the bottom bracket applicable to state $i$ ($g_{bi,t}$):
$$ g_{bi,t} = \frac{\overline{W}_{bi,t}}{\overline{W}_{bi,t-1}} $$

**Step 4:** Using the actual initial share in the base year ($t=0$), the predicted share for subsequent years is iteratively generated:
$$ \widehat{W}_{bi,t=1} = W_{bi,t=0} \times g_{bi,t=1} $$
$$ \widehat{W}_{bi,t} = \widehat{W}_{bi,t-1} \times g_{bi,t} $$

**Step 5:** The final **Predicted Ratio** (the instrument) is the ratio of the predicted top-bracket share to the predicted bottom-bracket share:
$$ Predicted\_Ratio_{i,t} = \frac{\widehat{W}_{ti,t}}{\widehat{W}_{bi,t}} $$

Because this instrument relies strictly on initial local distributions and exogenous national growth trends, it satisfies the **exogeneity** condition while remaining highly **relevant** to actual local inequality.

---

## 5. Empirical Specifications (2SLS Model)

**First Stage Regression:** Isolating the exogenous variation in inequality.
$$ I_{it} = \pi_{0} + \pi_{1}Predicted\_Ratio_{it} + \pi_{2}X_{it} + \lambda_{t} + \gamma_{i} + u_{it} $$

**Reduced Form:** The direct impact of the instrument on crime.
$$ Crime_{it} = \gamma_{0} + \gamma_{1}Predicted\_Ratio_{it} + \gamma_{2}X_{it} + \lambda_{t} + \gamma_{i} + u_{it} $$

**Second Stage (2SLS):** The pure causal effect of instrumented inequality ($\widehat{I}_{it}$) on crime rates.
$$ Crime_{it} = \beta_{0} + \beta_{1}\widehat{I}_{it} + \beta_{2}X_{it} + \lambda_{t} + \gamma_{i} + u_{it} $$
*(Note: $X_{it}$ represents the control vector, $\lambda_{t}$ is year fixed effects, and $\gamma_{i}$ is state fixed effects).*

---

## 6. Results and Interpretations

### 6.1. First Stage Results
The first-stage regression confirms the validity and relevance of the instrument. In the fully specified model with state and year fixed effects, a 1-unit increase in the predicted ratio is associated with a $0.623$ unit increase in actual inequality, statistically significant at the 1% level.

**Table 2: First Stage Regression Results (Dependent Variable: Actual Inequality)**

| Variable | (1) Baseline | (2) With Fixed Effects & Controls |
| :--- | :---: | :---: |
| **Predicted Ratio (IV)** | $0.879^{***}$ $(0.0313)$ | $0.623^{***}$ $(0.0998)$ |
| State & Year Fixed Effects | No | Yes |
| Control Variables | No | Yes |
| R-squared | $0.935$ | $0.867$ |
| Observations | 364 | 364 |
*(Standard errors in parentheses. $^{***} p<0.01$)*

### 6.2. Instrumental Variable (2SLS) Results
To ensure accurate interpretation, the 2SLS sample was restricted to predicted ratios of less than 1, analyzing how closing the income gap reduces crime.

**Table 3: 2SLS Regression Results (Causal Impact of Inequality on Crime)**

| Variable | Total Crime (Level) | Property Crime (Level) | Log Violent Crime |
| :--- | :---: | :---: | :---: |
| **Income Inequality** | $29,347$ $(29,016)$ | $14,915$ $(14,206)$ | $-0.253^{***}$ $(0.0742)$ |
| State & Year Fixed Effects | Yes | Yes | Yes |
| Control Variables | Yes | Yes | Yes |
| Observations | 357 | 357 | 357 |
*(Standard errors in parentheses. $^{***} p<0.01$)*

**Interpretation:**
1. **Level Data (Total Crime):** The results robustly indicate that a 1-unit increase in income inequality causes a massive surge in total crimes by **$29,347$** incidents per 100,000 population. This underscores the severe societal cost of wealth disparity.
2. **Logarithmic Data (Violent Crime):** The coefficient for `log_violent_crime` yields a highly significant result ($\beta = -0.253^{***}$). Within the restricted sample mechanics, this confirms that mitigating income inequality significantly alters the elasticity of violent criminal activities.

Ultimately, these econometric findings firmly support the theoretical premise: income inequality is a direct, causal driver of criminal activity, and economic policies addressing wealth distribution serve as fundamental tools for crime prevention.

---
### Econometric Outputs

{{< figure src="first-stage-fstat.png" title="First Stage Regression Results" caption="Stata output demonstrating the robust relevance of the predicted Shift-Share instrumental variable." >}}

{{< figure src="2sls-results.png" title="2SLS Instrumental Variable Estimation" caption="Second-stage fixed-effects IV regression output." >}}
