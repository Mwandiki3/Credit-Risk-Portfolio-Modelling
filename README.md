# Credit-Risk-Portfolio-Modelling
End-to-end data pipeline and risk modeling infrastructure analyzing 2.3M credit records. Computes volume-weighted PAR, FICO stratification, LGD severity, Loss Given Default, Loss Acceleration Velocity (LAV), Risk Interaction Multipliers (RIM), and historical Credit Drift Acceleration (CDA).

# Credit Risk Portfolio Modelling

A production-grade data pipeline and stress-testing system that analyzes **2.3 million consumer loans**. This project connects cloud storage, an optimized SQL database engine, and Python visualization tools to track portfolio risk from end to end.

Executive Summary
Traditional credit models often look only at the number of loans that default, which can be misleading. This infrastructure builds a volume-weighted framework. By focusing on the actual dollar amounts at risk,it uncovers exactly where capital is lost, how long-term loans amplify risk, and how credit quality shifts across historical economic cycles.

The 6 Core Risk Analytics Layers

1. Portfolio at Risk (PAR %)
What it does:Measures the true velocity of capital loss.
How it works:Instead of counting defaulted loans, it calculates the percentage of total funded cash written off in each credit tier. This prevents large loans from hiding behind small loan averages.

2. Loss Given Default (LGD %)
What it does:Quantifies the actual capital wiped out after a borrower defaults.
How it works:It tracks the post-default recovery lifecycle, measuring real cash recovered against the original unpaid principal. ($LGD = 100\% - \text{Recovery Rate}$).

3. Loss Acceleration Velocity (LAV)
What it does:Measures how much more dangerous long-term loans are compared to short-term ones.
How it works:A duration multiplier that divides 60-Month loan default rates by 36-Month loan default rates across different risk grades ($LAV = \frac{\text{60M PAR\%}}{\text{36M PAR\%}}$).

4. Capital Impairment Velocity (CIV)
What it does:Benchmarks how sensitive borrowers are to high debt.
How it works:It establishes a "safe-haven" baseline using low-debt borrowers (Debt-to-Income < 10%). It then tracks how fast default rates multiply as a borrower's debt load increases.

5. FICO × DTI Risk Interaction Multiplier (RIM)
What it does:Uncovers compounding risks when low credit scores meet high debt.
How it works:A 20-cell risk matrix that combines 5 FICO bands with 4 debt tiers. Every single risk intersection is indexed against the safest possible profile (Super Prime + Low Debt) to capture severe risk compounding.

6. Credit Drift Acceleration (CDA)
What it does:Warns the institution when credit standards are slipping over time.
How it works:A time-series metric that measures year-over-year changes in loan default performance, catching portfolio deterioration early.


## 🛠️ Data Pipeline & Technology Architecture
