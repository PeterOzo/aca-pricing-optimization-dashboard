# Real-Time Pricing Optimization Engine for Auto Loans

Analyzing and optimizing auto-loan APRs using machine-learning risk models, live market intelligence, and multi-objective optimization. The Streamlit dashboard empowers lenders to balance profitability, competitive positioning, regulatory compliance, and fair-lending requirements—all in sub-millisecond decision times.

---

## Business Question  
How can auto-loan originators continuously set borrower-specific APRs that maximize risk-adjusted profit, maintain competitive market positioning, comply with regulatory rate bounds, and uphold fair-lending standards?

## Business Case  
In today’s fast-moving auto-finance market, static interest-rate tiers lead to suboptimal profitability, inconsistent risk management, and potential compliance gaps. By integrating predictive risk scoring with live competitor data and optimization, lenders can capture an estimated **\$5.1 M** in additional annual profit, improve default‐prediction accuracy by **11 percentage points** (from 62.5 % to 73.5 % AUC), and deliver individualized pricing with < 1 ms latency—driving both top- and bottom-line impact.

## Analytics Question  
Can we develop and deploy a machine-learning–driven pricing engine that:  
1. Predicts borrower default probability in real time  
2. Ingests and validates live market-rate data  
3. Solves a multi-objective optimization under rate, profit, and fair-lending constraints  
4. Returns an APR recommendation in under 1 ms  

## Outcome Variable of Interest  
- **Optimized APR (r\*)** per loan request  

## Key Predictors & Inputs  
- **Customer:** Loan amount (L), term (T), annual income, debt-to-income ratio (DTI), credit grade  
- **Risk Model:** XGBoost-predicted default probability \(P(D\mid X)\) trained on 250 K historical loans  
- **Market Data:** Competitor APRs from Bankrate, Yahoo Finance treasuries, FRED economic series, custom scrapers  
- **Regulatory & Fair-Lending Bounds:** APR ∈ [6 %, 29.9 %]; expected profit ≥ \$500; demographic variance limits  

## Data Set Description  
- **Historical Loans:** 250 K records (2007–2020) from LendingClub, 185 engineered features (demographics, credit, macro)  
- **Market Rates:** Live snapshots of auto-loan APRs (9–12 sources) refreshed every 5 minutes, cleaned and cross-validated  
- **System Metrics:** API latency, model AUC, data-source health, throughput logs  

## Methodology & Transformations  
1. **Risk Assessment**  
   - XGBoost classification with hyperparameter tuning, early stopping, and SMOTE balancing  
   - Validated at 73.47 % AUC vs. 62.53 % logistic baseline  

2. **Market Intelligence**  
   - Z-score filtering and cross-source correlation for outlier removal  
   - Time-series consistency checks on treasury yields and competitor rates  

3. **Multi-Objective Optimization**  
   \[
   r^* = \arg\max_{r}\Bigl[
     \alpha\,\text{Profit}(r)
     + \beta\,\text{Competition}(r)
     - \gamma\,\text{Risk}(r)
   \Bigr]
   \]  
   - **Profit(r):** \(r\,L\,T - P(D\mid X)\,L\,\mathrm{LGD} - \mathrm{OpCost}\)  
   - **Competition(r):** Penalty for deviation from target percentile (62.5 th)  
   - **Risk(r):** Penalty for high DTI, low income, large loan size  
   - **Constraints:** APR bounds, profit minimums, fair-lending variance  

4. **Dashboard & Delivery**  
   - Streamlit UI for input, results, and interactive charts  
   - Intelligent caching (TTL=300 s) and parallel pipelines → < 1 ms response  

## Descriptive & Diagnostic Analysis  
- **APR Distribution vs. Market:** Histograms and box plots compare optimized APRs to live competitor rates  
- **Profit & Risk Waterfall:** Financial breakdown of revenue, expected loss, operating & funding costs  
- **Performance Trends:** 7-day API latency and health-score time series  

## Results & Impact  
| Metric                        | Baseline      | Optimized System | Δ Improvement          |
|-------------------------------|---------------|------------------|------------------------|
| Default-Model AUC             | 62.53 %       | 73.47 %          | +10.94 pp              |
| Profit per Loan               | \$2,630       | \$3,855          | +\$1,225               |
| Annual Profit Uplift          | –             | \$5.1 M          | –                      |
| Market Percentile Retained    | static tier   | 62.5 th          | dynamic adaptation     |
| API Response                  | batch (hrs)   | 0.83 ms          | 99.98 % faster         |
| Fair-Lending Compliance Score | 85–95 %       | 100 %            | +5–15 pp               |

## Installation & Execution  
```bash
git clone https://github.com/YourUsername/AutoLoanPricingOpt.git
cd AutoLoanPricingOpt
python3 -m venv venv && source venv/bin/activate
pip install -r requirements.txt
streamlit run app.py
