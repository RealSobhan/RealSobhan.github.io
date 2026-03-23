---
title: 'Causal Inference in Econometrics: Income Inequality and Crime Rates'
summary: 'An advanced panel data econometric study investigating the causal impact of income inequality on crime rates across U.S. states using 2SLS regression and a custom Shift-Share Instrumental Variable.'
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

## 1. Introduction and Literature Review
Criminal activities impose severe social, economic, and security costs on society. To design effective crime-prevention policies, understanding the underlying drivers of crime is essential. One of the most prominent factors is **income inequality**. 

The foundation of this research relies on **Becker’s (1968)** economic model of crime, which posits that criminal acts are rational choices made by utility-maximizing individuals comparing expected returns from legal versus illegal activities. Sociologically, this is complemented by **Merton’s Strain Theory (1938)** and **Agnew's General Strain Theory**, which argue that individuals facing blocked opportunities to achieve societal goals (like wealth accumulation) may experience frustration, driving them toward illegal methods. 

Empirical studies robustly support these theories. Ehrlich (1973), Kelly (2000), and Choe (2008) established strong links between income inequality and specific crimes (like property crime, burglary, and violent crimes) across U.S. regions. Similarly, Machin and Meghir (2004) demonstrated that worsening labor market opportunities for low-skill workers directly increased property crime rates in the UK. Building on this literature, this study investigates the causal impact of income inequality on various crime rates across 52 U.S. states from 2010 to 2018.

---

## 2. Theoretical Framework
Following Becker and Ehrlich, the supply of offenses ($O_j$) by an individual is modeled as a function of the probability of conviction ($p_j$), the penalty ($f_j$), and other socio-economic variables ($u_j$):
$$ O_{j} = O_{j}(p_{j}, f_{j}, u_{j}) $$

An individual chooses between the expected return from legal work ($W_l$) and illegal work ($E(W_i)$). The expected return from legal work is an increasing function of working time ($t_l$):
$$ W_{l}(t_{l}) = E(W_{l}) $$

Conversely, the expected return from illegal work considers the probability of apprehension ($p_i$) and the penalty ($F_i$):
$$ E(W_{i}) = (1 - p_{i}) + p_{i}(W_{i}(t_{i}) - F_{i}(t_{i})) $$

As societal income inequality widens—specifically when the share of individuals with very low expected legal returns increases alongside the presence of high-income targets—the expected return from illegal work outweighs legal work. This simultaneously increases the **supply** of criminals (due to low legal wages) and the **demand/opportunity** for crime (due to available wealth to steal).

---

## 3. Data and Descriptive Statistics
The study utilizes a balanced panel dataset (2010-2018). 
* **Crime Data (FBI UCR):** The average total reported crimes per 100,000 people is $372,189.71$. Sub-categories average $163,143.75$ for property crimes, $24,219.93$ for violent crimes, and $6,557.06$ for robberies.
* **Income Inequality (U.S. Census Bureau):** Inequality is measured by the ratio between the income shares of the top three income brackets (>$150k) and the bottom three brackets (<$25k). 
* **Control Variables:** Demographic controls include the share of males aged 15-24 (mean: $14.25\%$), the percentage of foreign-born individuals (mean: $9.04\%$), and the percentage of male householders with no spouse/partner (mean: $4.61\%$).

---

## 4. The Instrumental Variable (Shift-Share Construction)
Estimating the impact of inequality on crime via Ordinary Least Squares (OLS) is highly susceptible to **Reverse Causality**. Wealthy individuals may migrate away from high-crime areas, artificially altering the state's income distribution. 

To resolve this endogeneity, a **predicted ratio of income shares** is constructed as an Instrumental Variable (IV). To ensure exogeneity, a "leave-one-out" national growth rate is utilized.

First, the total sum of the population share in the bottom income bracket across all $m$ states ($S_{b,t}$) is calculated:
$$ S_{b,t} = \sum_{i=1}^{m} W_{bi,t} $$

To prevent local state shocks from influencing the instrument, the state's own share ($W_{bi,t}$) is subtracted to calculate the exclusive national average ($\overline{W}_{bi,t}$):
$$ \overline{W}_{bi,t} = \frac{S_{b,t} - W_{bi,t}}{m - 1} $$

The national growth rate for the bottom bracket applicable to state $i$ ($g_{bi,t}$) is then:
$$ g_{bi,t} = \frac{\overline{W}_{bi,t}}{\overline{W}_{bi,t-1}} $$

Using the initial share in year $t=0$, the predicted share for subsequent years is iteratively generated:
$$ \widehat{W}_{bi,t=1} = W_{bi,t=0} \times g_{bi,t=1} $$
$$ \widehat{W}_{bi,t} = \widehat{W}_{bi,t-1} \times g_{bi,t} $$

Finally, the **Predicted Ratio** (the instrument) is the ratio of the predicted top-bracket share to the predicted bottom-bracket share:
$$ Predicted\_Ratio_{i,t} = \frac{\widehat{W}_{ti,t}}{\widehat{W}_{bi,t}} $$

Because this instrument relies strictly on initial local distributions and national growth trends (excluding the state itself), it satisfies the **exogeneity** condition while remaining highly **relevant** to actual local inequality.

---

## 5. Empirical Specifications (2SLS Model)

**First Stage Regression:**
$$ I_{it} = \pi_{0} + \pi_{1}Predicted\_Ratio_{it} + \pi_{2}X_{it} + \lambda_{t} + \gamma_{i} + u_{it} $$
Where $I_{it}$ is the actual inequality, $X_{it}$ are controls, $\lambda_{t}$ is year fixed effects, and $\gamma_{i}$ is state fixed effects.

**Reduced Form:**
$$ Crime_{it} = \gamma_{0} + \gamma_{1}Predicted\_Ratio_{it} + \gamma_{2}X_{it} + \lambda_{t} + \gamma_{i} + u_{it} $$

**Second Stage (2SLS):**
$$ Crime_{it} = \beta_{0} + \beta_{1}\widehat{I}_{it} + \beta_{2}X_{it} + \lambda_{t} + \gamma_{i} + u_{it} $$
Where $\widehat{I}_{it}$ is the instrumented inequality derived from the first stage. To isolate accurate dynamics, the sample is restricted to predicted ratios of less than 1, analyzing how closing the income gap reduces crime.

---

## 6. Results and Interpretations

### 6.1. First Stage Results
The first-stage regression confirms the validity and relevance of the instrument. The coefficient for the predicted ratio is **$0.623^{***}$** (Standard Error = $0.0998$) in the fully specified model with state and year fixed effects. This demonstrates a strong, statistically significant positive relationship: a 1-unit increase in the predicted ratio is associated with a 0.623-unit increase in actual inequality.

### 6.2. Reduced Form Results
The reduced form estimations reveal heterogeneous impacts across crime types. Without logarithmic transformations (Panel A), an increase in the predicted income ratio is positively correlated with total crimes and property crimes. However, when utilizing logarithmic rates (Panel B), the predictive ratio shows weak or slightly negative associations with certain violent crimes, indicating the complex mechanisms by which income distribution shocks permeate different criminal categories.

### 6.3. Instrumental Variable (2SLS) Results
The final 2SLS estimations isolate the causal impact. 
* **Panel A (Level Data):** The results robustly indicate that a 1-unit increase in income inequality causes a statistically significant surge in total crimes by **$29,347$** incidents per 100,000 population. This underscores the massive societal cost of wealth disparity.
* **Panel B (Logarithmic Data):** While total crime effects are less significant in log-form, the coefficient for `log_violent_crime` yields a highly significant result ($\beta = -0.253^{***}$). Within the restricted sample mechanics, this confirms that mitigating income inequality significantly alters the elasticity of violent criminal activities.

Ultimately, these econometric findings firmly support the theoretical premise: income inequality is a direct, causal driver of criminal activity, and economic policies addressing wealth distribution serve as fundamental tools for crime prevention.

---
### Econometric Outputs

{{< figure src="first-stage-fstat.png" title="First Stage Regression Results" caption="Stata output demonstrating the robust relevance of the predicted Shift-Share instrumental variable." >}}

{{< figure src="2sls-results.png" title="2SLS Instrumental Variable Estimation" caption="Second-stage fixed-effects IV regression output." >}}
