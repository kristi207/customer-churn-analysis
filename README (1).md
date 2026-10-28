# Customer Churn Risk Analysis

An end-to-end churn analysis on real user data from a SaaS product's Intercom workspace. The goal is to find which engagement signals predict churn and to give the customer success team a ranked list of at-risk users to contact first.

> **Data privacy:** The raw export contains personal customer information, so it is not included in this repository, and all notebook outputs have been cleared. The code runs on any Intercom user export with the same columns.

## Dataset

- 2,286 users exported from Intercom (May 2026)
- **Churn definition:** a user who unsubscribed from emails or whose email address hard-bounced
- **Key data-quality finding:** most users were bulk-imported on a single date, which left fields like plan, monthly spend, and country mostly empty. The analysis therefore relies on engagement and timing signals rather than billing data.

## Approach

1. **Data quality review:** fill rate for every column, to decide which fields are usable
2. **Exploratory analysis:** churn rate overall, by email type (free vs business), and by whether a user ever replied
3. **Feature engineering:** six features built from the raw export
   - days since first seen
   - days since last contacted
   - engagement window (first seen to last contacted)
   - number of web sessions
   - whether the user ever replied
   - free email provider flag
4. **Modelling:** Logistic Regression (interpretable baseline) and Random Forest, both with class balancing, evaluated with 5-fold cross-validated ROC AUC, a confusion matrix, and ROC curves
5. **Risk scoring:** every user gets a churn probability and is placed in a High, Medium, or Low risk tier
6. **Export:** a list of high-risk users the support team can use for outreach

## Key findings

- Churn is rare (about 2.4% of users), so the models use class balancing and are judged on AUC rather than accuracy.
- Users who replied to the team at least once had **no churn at all**, making two-way conversation the strongest retention signal in the data.
- Users signed up with free email providers churned at roughly **twice the rate** of users with business email addresses.

## Recommendations for the support team

- Prioritise getting a first reply from new users, since any reply was linked to zero churn.
- Give extra onboarding attention to users with free email addresses.
- Review the High Risk tier regularly and contact those users before they disengage.

## Limitations

- Most behavioural and billing fields were empty, so the model uses only six features.
- With so few churned users, results can vary between runs and should be read as directional.
- "Churn" here means leaving email communication, which is a proxy for leaving the product.

## How to run

```bash
pip install -r requirements.txt
```

1. Put an Intercom user export in the `data/` folder.
2. Update the file name in the first cell of `notebooks/churn_analysis.ipynb`.
3. Run all cells in Jupyter or VS Code.

## Tools

Python, pandas, NumPy, scikit-learn, Matplotlib, seaborn, Jupyter
