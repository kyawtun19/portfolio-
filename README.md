# Marketing Mix Modeling (MMM) Portfolio Project

This project implements Marketing Mix Modeling (MMM) using both Bayesian statistics (with PyMC) and traditional linear regression (with Statsmodels) to analyze the impact of different marketing channels on a synthetic bed booking dataset. The goal is to understand the contribution of each channel, account for adstock and saturation effects, and potentially optimize marketing spend allocation.

## Project Overview

The project uses a synthetic dataset that simulates the performance of three online advertising platforms (Google, Meta, and Snap) in driving bed bookings for a hypothetical accommodation business.  The dataset includes weekly data on impressions, clicks, spend, and bookings for each platform.  The project walks through several key steps:

1.  **Data Loading and Exploration:** Loading the CSV data into a Pandas DataFrame and performing initial data exploration (shape, info, describe, checking for missing values).

![image](https://github.com/user-attachments/assets/aff94d03-50f0-46ab-9c5f-4f315fc3e4ac)

2.  **Data Cleaning:**
    *   Replacing missing values represented by "NA" or empty strings.
    *   Converting relevant columns ("impression", "clicks", "Net+Agency Spend", "Bookings (No VTC)") to numeric types, handling non-numeric characters.
    *   Standardizing string casing (title case for "Language") and removing leading/trailing whitespace.
    *   Correcting any misspelled column names (e.g., "impressionz").
    *   Removing rows with `NaN` values after the numeric conversions.



3.  **Feature Engineering:**
    *   Calculating "cost\_per\_booking" by dividing "Net+Agency Spend" by "Bookings (No VTC)". Rows with infinite or missing values in this new column are dropped.
    *   Aggregating the data to a weekly level by platform (summing numeric columns).
    *   Implementing an **Adstock Transformation** (`adstock_transform` function): This simulates the lagged or carry-over effect of advertising, where the impact of spend in one period decays over subsequent periods.  A geometric decay rate (alpha) is used.

      
      ![image](https://github.com/user-attachments/assets/bc084d97-71d2-407a-860e-b742acb4c1a1)
    *   Implementing a **Saturation (Hill) Function** (`hill_saturation` function): This models diminishing returns, acknowledging that the incremental impact of ad spend decreases as spend increases. This is crucial for realistic modeling. The Hill function has parameters 'hill_a' (maximum effect), 'hill_b' (shape/slope), and 'hill_c' (half-saturation point).


      ![image](https://github.com/user-attachments/assets/07a47eb9-7950-4f7c-8cba-42ffb5f4bce8)

      

4.  **Exploratory Data Analysis (EDA):**
    *  Initial data inspection (`head()`, `info()`, `describe()`, etc.)
    *  Line plots of spend vs. total bookings for each platform (Google, Meta, Snap) to visualize relationships.
    *  Regression plots using `seaborn.regplot()`
      
  ![image](https://github.com/user-attachments/assets/96797a12-c716-4136-b66a-73335042cc30)

  ![image](https://github.com/user-attachments/assets/e5387f0a-5135-4a8c-99f0-a0144aebc295)

  ![image](https://github.com/user-attachments/assets/4f60fc4b-de2b-41e2-b7fb-44444ace9293)



 5. **Bayesian MMM (PyMC):**
    *   A Bayesian hierarchical linear regression model is built using PyMC.
    *   **Adstock Transformation:** A geometric adstock function models the lagged effect of advertising, incorporating a decay rate.
    *   **Saturation (Hill Function):** A Hill function models diminishing returns, using parameters for maximum effect, shape, and half-saturation point.

![image](https://github.com/user-attachments/assets/071a1789-ebfb-4237-8fec-f18e471de60c)


6.  **Linear Regression (Statsmodels):**
    *   A standard Ordinary Least Squares (OLS) regression model is fit using Statsmodels.
    *   This model uses raw spend data directly (no adstock or saturation).

![image](https://github.com/user-attachments/assets/2daaea50-0c6d-4424-8d64-2c35ea979b41)


## 7. Comparing Model Results

### Bayesian MMM
![image](https://github.com/user-attachments/assets/6aadd89b-91a8-4b22-8495-74e53f50307a)

### Linear Regression
![image](https://github.com/user-attachments/assets/2140ff55-8ff8-4513-bb51-61fcc0ef82bd)

### Bayesian MMM

**Model Purpose**  
It estimates how changes in advertising “effective exposure” (after accounting for delayed impact and diminishing returns) translate into bookings for each channel (Google, Meta, Snap).

**Key Metrics**  
- **β₀ (Intercept ~72)**: The baseline level of bookings when the transformed exposure is zero.  
- **β_google (~154)**: For every 1 unit increase in Google’s effective exposure score, bookings rise by about 154 units.  
- **β_meta and β_snap (each ~71)**: A 1 unit increase in their transformed exposure corresponds to around 71 more bookings.

**What “1 Unit Increase” Means**  
Raw ad spend is converted through ad stock and Hill saturation functions into a unitless “effective exposure” score. A 1 point jump on this scale represents a significant boost in effective exposure, *not* a \$1 increase.

**Interpretation**  
- Google’s higher coefficient indicates that as its effective exposure improves, it delivers a notably stronger boost in bookings compared to Meta and Snap, which reach a plateau faster.  
- **σ (~83,920)**: Reflects the overall variability in bookings across all channels.

**Conclusion**  
This summary demonstrates that for the same increase in effective exposure, **Google** produces a higher increase in bookings, highlighting its superior scalability.

### Linear Regression

**Model Purpose**  
This model estimates how advertising spend on Google, Meta, and Snap influences the total number of guest bookings.

**Key Findings**  
- **Overall Fit**: The model explains about 25% of the variation in bookings (R² = 0.25).  
- **Channel Effects**:
  - **Google Spend**: For every extra dollar spent on Google, the number of bookings increases by about 0.52 units. In practical terms, an extra \$100 in spend on Google is linked to roughly 52 more bookings. This effect is statistically significant (p=0.046).  
  - **Meta Spend**: Each extra dollar spent on Meta corresponds to an increase of about 0.07 bookings (so an extra \$100 would yield roughly 7 more bookings), which is highly significant (p=0.002).  
  - **Snap Spend**: The impact of spend on Snap is not statistically significant (p=0.507), meaning its effect on bookings is unclear.

**Business Implication**  
These results suggest that increasing spend on Google and Meta is strongly linked to higher guest bookings—with Google showing a particularly strong response. This supports a business case for focusing on Google, as every extra dollar spent there produces a much greater lift in bookings compared to the other channels.

# Why I Choose the Bayesian Model Over the OLS Model

I choose the Bayesian model because it offers deeper insight into uncertainty plus precise estimates of effect sizes in our context. While the OLS model produces simple point estimates, the Bayesian method:

- **Delivers complete posterior distributions** for each parameter, letting us gauge uncertainty via credible intervals.
- **Integrates prior knowledge**, which proves especially useful when differences between channels (e.g., Google, Meta, Snap) show minimal separation while data is noisy.
- **Supplies robust convergence diagnostics plus metrics** (for instance, effective sample size, r_hat) that build confidence in the model’s estimates.

In short, the Bayesian model’s statistical power, flexibility, plus richer insights make it the preferred option for our marketing mix evaluation, empowering more informed, data-driven decisions regarding channel effectiveness.

    
