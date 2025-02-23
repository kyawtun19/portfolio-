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
      
  ![image](https://github.com/user-attachments/assets/9f90cc9b-b3c9-4957-9ec3-7128ff007885)

![image](https://github.com/user-attachments/assets/94038d3b-685e-46bd-a896-e458d9823464)

![image](https://github.com/user-attachments/assets/72b4e661-30bc-44bc-9e46-0d59a18dca3c)

![image](https://github.com/user-attachments/assets/5bcd3b95-d391-48ee-bde7-91669e796b77)

 5. **Bayesian MMM (PyMC):**
    *   A Bayesian hierarchical linear regression model is built using PyMC.
    *   **Adstock Transformation:** A geometric adstock function models the lagged effect of advertising, incorporating a decay rate.
    *   **Saturation (Hill Function):** A Hill function models diminishing returns, using parameters for maximum effect, shape, and half-saturation point.
    *   **Priors:** Normal priors are assumed for coefficients, and a Half-Normal prior for the noise term.
![image](https://github.com/user-attachments/assets/5830cb8f-12ef-4e19-9423-ccb76197ef5b)


6.  **Linear Regression (Statsmodels):**
    *   A standard Ordinary Least Squares (OLS) regression model is fit using Statsmodels.
    *   This model uses raw spend data directly (no adstock or saturation).
  
![image](https://github.com/user-attachments/assets/14404ae2-cc60-41b8-abee-8c83fbb74ce8)

7. Comparing Model Results: Bayesian MMM vs. Linear Regression

   ![image](https://github.com/user-attachments/assets/941b1e97-bccb-40d7-948a-cbda6a34ef85)

   ## Statistical Explanation

- **Lowest Mean Cost**: Google shows the smallest average cost (~71.88) compared to Meta (~72.02) and Snap (~72.06).
- **Credible Interval Overlap**: All three intervals are wide and overlap, but if forced to pick one winner statistically, Google edges out slightly on the mean.

Marketing Rationale

- **High‐Intent Traffic**: Google users often search with immediate needs, boosting conversions.
- **Scalability**: Google supports larger budgets without steep cost inflation.
- **Advanced Optimization**: Automated bidding fine‐tunes spend for maximum returns.

**Conclusion**: Despite broad intervals, Google’s slightly lower mean plus these marketing advantages suggest it could be the most cost‐effective option for a big booking platform.

![image](https://github.com/user-attachments/assets/33bf4a3d-1a13-4bc2-ad8f-526dbd3ce427)

**Google_Spend (0.0415)**  
For every \$1 spent, the model suggests ~0.04 additional bookings. Google stands out as the only platform with a *positive* coefficient, implying it’s the likeliest to drive incremental bookings.

**Meta_Spend (–0.0033)**  
For each \$1 spent, bookings drop slightly (~0.003). This minor negative coefficient suggests Meta may be less efficient than Google.

**Snap_Spend (–0.0110)**  
For each \$1 spent, bookings decrease by ~0.011, indicating Snap could be even less efficient.

**Bottom Line**:  
Based on sign and magnitude alone, **Google** appears the best bet for boosting bookings among these three channels.








    
