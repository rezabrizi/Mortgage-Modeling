# Mortgage Modeling

This project was conducted to address the [YQFM x WITG QIS Mortgage Modeling Challenge 2024](https://docs.google.com/document/d/1RrLuIcKe6JaP6ZEULQqufj1-yCM3m66jiDWPU0kaL9A/edit#heading=h.l3ggro4goodk). Given data regarding 5,000 mortgages and their actual cash flow over a 30-year fixed period, we were tasked with making bids on an additional 500 mortgages to maximize our profit. 
* All the code related to data analysis, model training, and bid calculation is in the notebook `model.ipynb`.

## Analyzing Data
Initially, I determined which mortgages defaulted and in which year they defaulted. Predicting default probability is crucial to make an accurate bid.

To understand the impact of various features on the likelihood of a mortgage defaulting, I used various graphs to plot the relationships between different variables. For example, the boxplot of relative total cash flow based on education level suggests a direct correlation between higher education and default rate.

After analyzing the data, I performed minor preprocessing for additional insights based on the provided data: 
1. Added column `adjusted_income` by adding 30% of the `coapplicant_income` to the `annual_income` of the borrower.
2. Created columns `['default_1', 'default_2', ..., 'default_30']` to indicate whether a specific year was a default, to dynamically predict default probability.
3. Created one-hot encoding for the education column to better represent the education level of the borrower.

## Testing out different models
I developed four different models:
1. A simple default predictor using logistic regression to calculate the default probability for each mortgage.
2. A simple default predictor using Random Forest Regressor.
3. A yearly default predictor using logistic regression to calculate the default probability for each year for each mortgage.
4. A yearly default predictor using Random Forest Regressor.

Based on the predicted default probabilities, I calculated four different present values to account for default risk. I used two different present value calculator functions for simple and yearly default probabilities:
1. `calculateSimpleDefaultPresentValue`: This function calculated `adjusted_annuity` = `pmt` * (1 - `predicted default probability`).
2. `calculateYearlyDefaultPresentValue`: This function calculated a yearly `A_i` = `pmt` * (1 - `predicted default probability year_i`).

The present value calculations for both regression and classification models resulted in four different values, compared to the true present value. The results show that the **yearly default risk using classification** generated the most accurate present value.

## Bidding
First, the present values using the yearly default risk with the classification model were calculated. To place bids with a profit margin, I used the simple default probability from the classification model to adjust a 10% margin: 
$$\text{margin}=0.1 * \text{predicted default probability}$$  
Following this the bids can be calculated using the formula:
$$\text{bid}= \text{presentValueYearlyDefault} * (1 - \text{margin})$$  