# 🎯 ACA Pricing Optimization Pro

**Team Member:** Peter Chika Ozo-Ogueji ([po3783a@american.edu](mailto:po3783a@american.edu))  
**Live Demo:** https://aca-pricing-optimization-dashboard-tmcdvlrjildupmaracljvv.streamlit.app/  
**Project Type:** Production-Ready Streamlit Dashboard  
**Key Artifact:** Real_Time_Pricing_Optimization_for_Auto_Loans_fina_paper-2.pdf :contentReference[oaicite:5]{index=5}  

---

## Business Question  
How can auto-loan originators set individualized APRs in real time that maximize risk-adjusted profit, maintain competitive market position, comply with regulatory and fair-lending constraints, and deliver sub-millisecond decision times?

## Business Case  
Static, grade-based pricing forfeits revenue and exposes lenders to unmanaged risk. By integrating a high-fidelity ML risk model with live market intelligence and a multi-objective optimizer, lenders can capture an estimated **\$5.1 M** annual uplift, improve default-prediction AUC by **11 pp**, and retain competitive positioning at the 62.5th percentile—all while ensuring **100 %** uptime and sub-millisecond response :contentReference[oaicite:6]{index=6}.

## Analytics Question  
Can we build and deploy an end-to-end system that:  
1. Predicts borrower default probability in real time (XGBoost, **73.47 % AUC**)  
2. Ingests and validates live competitor APRs (Bankrate, Yahoo, FRED, scrapers)  
3. Solves a constrained, multi-objective optimization for APR  
4. Returns a production-grade recommendation in **< 1 ms**

## Outcome Variable  
- **Optimized APR (r\*)** recommended per application

## Key Inputs & Predictors  
- **Customer Data:** Loan Amount (L), Term (T), Annual Income, DTI, Credit Grade  
- **Risk Score:** \(P(D\mid X)\) from XGBoost trained on 250 K historical loans  
- **Market Data:** Live auto-loan rates (8 sources), treasury yields (3 benchmarks), refreshed every 5 min  
- **Constraints:**  
  - APR ∈ [6.0 %, 29.9 %]  
  - Expected profit ≥ \$500/loan  
  - Demographic rate-variance limits for fair lending  

## Data & Transformations  
- **Loan History:** 250 K samples from Lending Club (2007–2020), 185 engineered features (demographics, financials, macro)  
- **Market Intelligence:**  
  - Z-score filtering, cross-source correlation for outlier removal  
  - Time-series consistency checks on Fed and treasury data  
- **Feature Engineering:** SMOTE for imbalance, recursive elimination to 24 top predictors

## Methodology & Architecture  

### 1. Risk Assessment Model  
- **Algorithm:** XGBoost binary classifier  
- **Hyperparameters:** `eta=0.1`, `max_depth=6`, `subsample=0.9`, early stopping  
- **Performance:** 73.47 % AUC vs. 62.53 % logistic baseline :contentReference[oaicite:7]{index=7}

### 2. Market Data Module  
- **Sources:** Bankrate (auto-loan), Yahoo Finance (treasuries), Federal Reserve APIs, custom scrapers  
- **Refresh:** Every 5 minutes, TTL caching (300 s) for stability  

### 3. Multi-Objective Optimization  
\[
r^* = \arg\max_{r}\Bigl[
  \alpha\,\mathrm{Profit}(r)
  + \beta\,\mathrm{Competition}(r)
  - \gamma\,\mathrm{Risk}(r)
\Bigr]
\]  
- **Profit:** \(rLT - P(D\mid X)L\,\mathrm{LGD} - \mathrm{OpCost}\)  
- **Competition:** Penalty for deviation from 62.5th percentile  
- **Risk:** Penalty scaling with DTI, income shortfall, loan size  
- **Modes:**  
  - **standard** (market-aware)  
  - **competitive** (cap at market_avg + 0.5 %)  
  - **profit-optimized** (+0.5 % uplift)

### 4. Streamlit Dashboard  
- **Pages:**  
  - Executive Command Center (system health, live KPIs)  
  - Real-Time Pricing Engine (input form & metrics)  
  - Market Intelligence (histograms, box plots, scatter)  
  - System Health Monitor (latency, uptime, AUC)  
  - Business Impact Analysis (ROI charts, scenario bar & scatter)  
- **Performance:**  
  - **Latency:** 0.83 ms avg (ThreadPoolExecutor, caching)  
  - **Uptime:** 100 % since launch  
  - **Throughput:** ~150 req/s  

## Results & Business Impact  

| Metric                          | Baseline    | This System   | Improvement      |
|---------------------------------|-------------|---------------|------------------|
| Risk AUC                        | 62.53 %     | 73.47 %       | +10.94 pp        |
| Expected Profit/Loan            | \$2,630     | \$3,855       | +\$1,225         |
| Annual Revenue Impact           | —           | \$5.1 M       | —                |
| Market Position Percentile      | static      | 62.5th        | dynamic adaptive |
| API Response Time               | 2–4 hrs     | 0.83 ms       | 99.98 % faster   |
| Fair-Lending Compliance Score   | 85–95 %     | 100 %         | +5–15 pp         |

## Installation & Execution  
```bash
git clone https://github.com/YourUsername/ACA-PricingOpt.git
cd ACA-PricingOpt
python3 -m venv venv && source venv/bin/activate
pip install -r requirements.txt
streamlit run app.py
