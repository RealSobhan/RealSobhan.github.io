---
title: 'Custom Stochastic Simulation Engine for Hospital Patient Flow Optimization'
summary: 'Developed a bespoke Discrete-Event Simulation (DES) framework from scratch in Python to analyze complex patient flows, prioritize emergency admissions, and optimize bed capacity allocation.'
tags:
  - Simulation
  - Operations Research
  - Python (From Scratch)
  - Data Analysis (R & Pandas)
  - Healthcare
date: '2025-12-01'

# Image Setting
image:
  caption: 'Statistical comparison of waiting times across different system configurations.'
  focal_point: 'Smart'
---

## Project Overview
This project focuses on the stochastic modeling and optimization of patient flow within a multi-department hospital. Rather than using commercial simulation software or standard libraries, **I developed a custom Discrete-Event Simulation (DES) engine entirely from scratch using Python.** The core objective was to model the complex routing of Elective and Non-Elective (Emergency) patients through various stages—including Pre-Surgical wards, Laboratories, Operating Rooms (OR), and Post-Surgical units (Ward, ICU, CCU)—to identify bottlenecks and improve overall system stability.

## Mathematical & Statistical Framework
Building a custom simulation engine from the ground up requires rigorous mathematical validation, particularly for analyzing stochastic processes and estimating steady-state parameters. 

**1. Output Analysis & Confidence Intervals:**
To ensure the reliability of Key Performance Indicators (KPIs) such as the average waiting time, I implemented steady-state statistical analysis. For $n$ independent simulation replications, the $(1 - \alpha)100\%$ confidence interval for the expected waiting time $\mathbb{E}[W]$ is calculated as:

$$ \bar{W} \pm t_{\alpha/2, n-1} \frac{S}{\sqrt{n}} $$

Where $\bar{W}$ is the sample mean of waiting times, $S$ is the sample standard deviation across replications, and $t_{\alpha/2, n-1}$ represents the critical value from the Student's t-distribution. This rigorous bounding was dynamically computed in Python and visualized using R.

**2. Time-Average Queue Evaluation:**
The core logic of the simulation continuously evaluates the expected queue length over time $T$. The time-average number of patients in the queue, a critical metric for capacity planning, is computed as the integral of the system state:

$$ \hat{L}_q = \frac{1}{T} \int_{0}^{T} Q(t) dt $$

where $Q(t)$ is a discrete-state, continuous-time stochastic process representing the exact number of patients waiting at time $t$. This continuous tracking allows for highly precise bottleneck identification compared to simple discrete averages.

## Custom Methodology & Model Development
* **Custom Simulation Core:** Programmed the event calendar, state variables, and time-advance mechanisms using core Python and Object-Oriented Programming (OOP) principles.
* **Complex Routing & Prioritization:** Incorporated strict priority logic for Non-Elective patients and modeled complex probabilistic transition matrices between hospital departments.
* **Statistical Output Analysis (R & Python):** Developed custom statistical classes in Python to compute mean, standard deviation, and confidence intervals. Used **R** (`plotrix`) to generate rigorous statistical visualizations, including bounded confidence intervals across steady-state time frames.

## System Analysis & Optimization Results
By comparing the baseline system with proposed what-if scenarios, the simulation provided actionable insights for hospital management:
* **Bottleneck Identification:** Identified the Pre-Surgical unit for Non-Elective patients and the General Ward as the primary constraints.
* **Strategic Resource Reallocation:** Discovered significant underutilization of Operating Room (OR) beds. Proposed a strategic reduction in OR capacity to reallocate financial resources toward expanding the bottlenecks (Pre-Surgical and Ward beds).
* **Performance Improvement:** The optimized scenario demonstrated a statistically significant reduction in maximum queue lengths and average waiting times.

---
### System Performance Visualization

*(Note: Image names below should we replaced with the exact file names you upload to GitHub)*

{{< figure src="r-plot-image.png" title="Steady-State Average Waiting Times with 95% Confidence Intervals" >}}

{{< figure src="kpi-comparison-image.png" title="Comparison of Maximum Queue Lengths: Baseline vs. Optimized Capacity Allocation" >}}
