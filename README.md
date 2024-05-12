# Data Description

Each row in the dataset represents the sourcing of one unit of a particular product combination. A unique product combination is defined by attributes in columns A, B, C, D, E, and F. Therefore, multiple rows with the same combination may exist in the training dataset, where each row represents the purchase of one unit.

### Dataset Split

The training dataset spans from July 2020 to May 2021, while the test dataset consists of June 2021 data. The goal is to forecast or predict the sourcing quantities for June 2021 based on the 11 months of training data (from June 2020 to May 2021). In the test set, June 2021 contains only one value for each combination, serving as the target for prediction.

# Methodology and Approaches

## 1. Data Understanding and Exploratory Data Analysis (EDA)
- **Explored dataset dimensions/shape**, checked for null values, and examined the data types of the features.
- **Identified anomalies** such as the min value of the 'Sourcing Cost' column being negative.
- Conducted **Univariate and Multivariate Analysis**, including frequency counts, box plots for outlier detection, and histplots for the distribution of the 'Sourcing Cost' column.
- **Investigated negative values** in 'Sourcing Cost' and considered optimum strategies for either removing or retaining them.
- **Identified the presence of outliers** in the 'Sourcing Cost' column.

## 2. Pre-processing

### Outlier Detection and Handling
- Employed the **IQR (Inter Quartile Range) method** for outlier detection with two approaches:
  - **Removal/Trimming** of the outliers from the data.
  - **Clipping outliers** to lower and upper bounds.
- Resulted in four sets of data for further analysis.

### Handling Negative Sourcing Cost Values
- Considered two approaches: **removal of negative values** or **taking the absolute values** of them.
- Since the number of negative values was relatively small compared to the total dataset size, removing them did not significantly affect model performance.

### Post-Preprocessing Data Sets
1. Dataset with outliers removed and negative values removed.
2. Dataset with outliers removed but with the absolute values of the negative sourcing values.
3. Dataset with outliers clipped to the upper and lower bound, and negative values removed.
4. Dataset with outliers clipped to the upper and lower bound, but with the absolute values of the negative sourcing values.

## 3. Analysis of Association Between Different Features
- To ensure robustness in our regression model, I examined relationships between independent variables and between independent variables and the dependent variable 'Sourcing Cost':
  - **Kruskal-Wallis test** was used to evaluate whether there are statistically significant differences in 'Sourcing Cost' across the categories of each categorical variable.
  - **Cramer's V** was utilized to measure the strength of association among categorical variables.
  - Removed the 'Area Code' column after identifying its strong association with other categorical variables to mitigate issues similar to multicollinearity.

## 4. Modelling and Evaluation on Test Data

### 1st Approach
- Implemented regression models such as **Linear Regression (LR)** and ensemble models like **Random Forest Regressor (RFC)** and **XGBoost Regressor (XGB)**.
- Employed **K-Fold cross-validation with 5 splits** to ensure model generalizability.
- **Model Performance**:
  - **LR**: Exhibited poor performance with an R2 score of 0.37 on training and 0.20 on test data.
  - **RFC and XGB**: Performed better with higher R2 scores and lower RMSE values than LR.

  #### Results Table
  | Model | Train R2 Score | Train RMSE | Test R2 Score | Test RMSE |
  |-------|----------------|------------|---------------|-----------|
  | LR    | 0.37           | 42.78      | 0.20          | 46.3      |
  | RFC   | 0.56           | 37.02      | 0.375         | 41.04     |
  | XGB   | 0.55           | 37.03      | 0.36          | 41.05     |

### 2nd Approach
- **Aggregated Sourcing Costs** by summing them for each group of duplicate rows based on categorical columns and created a new feature 'Average Sourcing Cost per Unit'.
- Achieved a **r2_score of 0.95 on the training set** but only **0.40 on the test set**.

### 3rd Approach
- Combined **Time Series analytics with regression** using the 'Month of Sourcing' as the dataframe index and creating additional features like lagged values and rolling statistics.
- **Random Forest Algorithm** predicted sourcing cost values with an R2 score of 0.99 and RMSE of 1.78 on training data and R2 score of 0.98 and RMSE of 5.21 on test data.


# Final Approach: Time Series Analytics and Regression

### Data Preprocessing for Time Series Analysis:

- Transformed dataset into time series format by setting index to 'Month of Sourcing'.
- Created lagged features for 'Sourcing Cost' to capture historical values.
- Calculated rolling statistics to understand trend and variability over time.
- Extracted additional temporal features to capture seasonal patterns and trends.

### Model Selection and Training:

- Applied Random Forest Algorithm for its effectiveness in handling nonlinear relationships and good generalisation capabilities
- Trained the model on a preprocessed dataset containing lagged features, rolling statistics, and temporal attributes.

### Model Evaluation and Performance:

- Achieved a high R2 score and low RMSE on both training and test datasets, indicating strong fit and generalization. 
`R2_score` of `0.99` and  `RMSE` of `1.78` on the training data and `R2_score` of `0.98` and `RMSE` of `5.21` on test data. The plot of the original sourcing values and the predicted also shows that this time the model was able to capture the relationship of the data, hence performing well.
- Accurate predictions based on historical data and temporal patterns provided valuable insights for decision-making.

### Interpretation and Insights:

Overall, the adoption of a Time Series-based approach combined with Random Forest Regression proved highly effective in predicting sourcing costs and understanding the true relationship of the data.

### Why Time Series Analytics and Regression?

1. **Sequential Nature of Data**: Each row represented sourcing of a product over different months, making it suitable for time series analysis and applying the time series techniques.
   
2. **Temporal Patterns and Trends**: Leveraged the temporal order of data which helped to capture underlying patterns and trends influencing the sourcing costs.
   
3. **Historical Insights**: The use of lagged features captured historical sourcing costs, allowing the model to learn from past observations.
   
4. **Seasonality and Cyclicality**: Extraction of temporal features like month, year, and quarter enabled capturing seasonal patterns and trends at different granularities.
   
5. **Model Selection**: Random Forest Regression was chosen for its robustness in handling nonlinear relationships and it being a ensemble method it handles overfitting and is good in generalisation to unseen data.


Using the rolling statistics, lagged sourcing cost values, quantile values and the month, year resulted in an effective model which was able to predict the Sourcing Cost in the test data as close to the original data and therfore making this approach suitable for adoption.


# Plots

<div style="display: flex; justify-content: center; align-items: center;">
    <div style="margin-right: 20px;">
        <p>Linear Regression</p>
        <img width="500" src="https://github.com/k-Rohit/AP_Moller_Maersk_Assignment/assets/93335681/5ddcbb06-677c-4d70-913a-549ea4e77aec">
    </div>
    <div style="margin-right: 20px;">
        <p>Random Forest</p>
        <img width="500" src="https://github.com/k-Rohit/AP_Moller_Maersk_Assignment/assets/93335681/bed239f5-f8ad-4b82-b7a8-d90b1ccbf1fd">
    </div>
    <div style="margin-right: 20px;">
        <p>Xg Boost</p>
        <img width="500" src="https://github.com/k-Rohit/AP_Moller_Maersk_Assignment/assets/93335681/3c2c0e61-828f-464c-a48d-5567f74cfce1">
    </div>
    <div>
        <p>Random Forest with Time Series features</p>
        <img width="500" src="https://github.com/k-Rohit/AP_Moller_Maersk_Assignment/assets/93335681/cfdbe215-cf6c-40a4-92f4-c4e2c5623db6">
    </div>
</div>


   
