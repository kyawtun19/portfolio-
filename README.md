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

