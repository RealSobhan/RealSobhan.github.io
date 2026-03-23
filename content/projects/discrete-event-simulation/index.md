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

# تنظیمات عکس هدر (نمودار بازه اطمینان را به عنوان عکس اصلی می‌گذاریم)
image:
  caption: 'Steady-State Waiting Time Analysis with 95% Confidence Intervals'
  focal_point: 'Smart'
  filename: 'steady-state-ci.png'
---

## Project Overview
This project focuses on the stochastic modeling and optimization of patient flow within a multi-department hospital. Rather than using commercial simulation software or standard libraries, **I developed a custom Discrete-Event Simulation (DES) engine entirely from scratch using Python.** The core objective was to model the complex routing of Elective and Non-Elective (Emergency) patients through various stages—including Pre-Surgical wards, Laboratories, Operating Rooms (OR), and Post-Surgical units (Ward, ICU, CCU)—to identify bottlenecks and improve overall system stability.

{{< figure src="hospital-arrival-flowchart.svg" title="Event-Driven Logic: Patient Arrival and Process Flow" caption="This flowchart illustrates the core patient arrival event that triggers the simulation logic. It maps the subsequent stochastic routing, priority-based queuing for non-elective (emergency) cases, and the step-by-step transition of entities through the hospital's constrained resources." >}}

## Mathematical & Statistical Framework
Building a custom simulation engine from the ground up requires rigorous mathematical validation, particularly for analyzing stochastic processes, estimating steady-state parameters, and comparing alternative configurations.

**1. Output Analysis & Confidence Intervals:**
To ensure the reliability of Key Performance Indicators (KPIs) such as the average waiting time, I implemented steady-state statistical analysis. For $n$ independent simulation replications, the $(1 - \alpha)100\%$ confidence interval for the expected waiting time $\mathbb{E}[W]$ is calculated as:

$$ \bar{W} \pm t_{\alpha/2, n-1} \frac{S}{\sqrt{n}} $$

Where $\bar{W}$ is the sample mean of waiting times, $S$ is the sample standard deviation across replications, and $t_{\alpha/2, n-1}$ represents the critical value from the Student's t-distribution. This rigorous bounding was dynamically computed in Python and visualized using R.

{{< figure src="sensitivity-analysis.png" title="Sensitivity & Output Analysis: Resource Utilization" caption="Point and interval estimation (95% Confidence Interval) for Key Performance Indicators across 30 independent replications. This analysis reveals critical bottlenecks in the Pre-Surgical and Ward units, while highlighting the underutilization of Operating Room (OR) capacities." >}}

**2. Time-Average Queue Evaluation:**
The core logic of the simulation continuously evaluates the expected queue length over time $T$. The time-average number of patients in the queue, a critical metric for capacity planning, is computed as the integral of the system state:

$$ \hat{L}_q = \frac{1}{T} \int_{0}^{T} Q(t) dt $$

where $Q(t)$ is a discrete-state, continuous-time stochastic process representing the exact number of patients waiting at time $t$. 

**3. Statistical Comparison of Alternative Systems:**
To rigorously validate the superiority of the proposed resource reallocation scenario (System 2) over the baseline (System 1), a Two-Sample Welch's T-test for unequal variances was implemented. The confidence interval for the difference in expected performance metrics ($\mu_1 - \mu_2$) is constructed as:

$$ (\bar{W}_1 - \bar{W}_2) \pm t_{\alpha/2, \nu} \sqrt{\frac{S_1^2}{n_1} + \frac{S_2^2}{n_2}} $$

Where the adjusted degrees of freedom ($\nu$) is calculated using the robust Welch-Satterthwaite approximation:

$$ \nu = \frac{(S_1^2/n_1 + S_2^2/n_2)^2}{\frac{(S_1^2/n_1)^2}{n_1-1} + \frac{(S_2^2/n_2)^2}{n_2-1}} $$

If this confidence interval strictly excludes zero, it mathematically guarantees that the observed improvements (e.g., reduced waiting times) are due to the strategic changes in system capacity, rather than mere stochastic noise.

{{< figure src="warmup-analysis.png" title="Warm-up Period Determination (Moving Average Method)" caption="Moving average analysis (140-frame window) of pre-surgical waiting times across 50 independent replications. The vertical dashed line identifies the truncation point (day 412.5), marking the end of the transient phase. This ensures initialization bias is eliminated before conducting the steady-state evaluation over a 2,500-day horizon." >}}

## System Analysis & Optimization Results
By comparing the baseline system with proposed what-if scenarios, the simulation provided actionable insights for hospital management:
* **Bottleneck Identification:** Identified the Pre-Surgical unit for Non-Elective patients and the General Ward as the primary constraints.
* **Strategic Resource Reallocation:** Discovered significant underutilization of Operating Room (OR) beds. Proposed a strategic reduction in OR capacity to reallocate financial resources toward expanding the bottlenecks (Pre-Surgical and Ward beds).
* **Performance Improvement:** The optimized scenario demonstrated a statistically significant reduction in maximum queue lengths and average waiting times.

*(Note: The full Python source code, R visualization scripts, and the comprehensive project report are available upon request.)*
