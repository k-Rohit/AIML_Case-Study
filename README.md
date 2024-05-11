### Data Description

Each row in the dataset represents the sourcing of one unit of a particular product combination. A unique product combination is defined by attributes in columns A, B, C, D, E, and F. Therefore, multiple rows with the same combination may exist in the training dataset, where each row represents the purchase of one unit.

### Dataset Split

The training dataset spans from July 2020 to May 2021, while the test dataset consists of June 2021 data. The goal is to forecast or predict the sourcing quantities for June 2021 based on the 11 months of training data (from June 2020 to May 2021). In the test set, June 2021 contains only one value for each combination, serving as the target for prediction.

# Methodology and Approach

## 1. Data Understanding and Exploratory Data Analysis (EDA):
- Explored the dataset dimensions/shape, checked for null values, and examined the data types of the features of the dataset.
- Used the pandas describe() method to understand feature datatypes, identifying anomalies like the min value of Sourcing Cost column being negative.
- Conducted Univariate analysis and Multivariate Analysis, including frequency counts, box plots for outlier detection, and histplots for distribution of the Sourcing Cost column.
- Investigated negative values in 'Sourcing Cost' and considered removal or replacement strategies.

## 2. Pre-processing:
### Outlier Detection and Handling:
- For Outlier detection, the IQR method was used, with two approaches:
  - Removal/Trimming of the outliers from the data.
  - Clipping outliers to lower and upper bound.
- Resulted in four sets of data for further analysis. And for both of these two approaches, I handled the negative sourcing cost values as follows:

### Handling Negative Sourcing Cost Values:
- For handling the negative values, two approaches were considered: removal of the negative vlaues or taking the absolute value fo them.
- Since the number of negative values was relatively small compared to the total dataset size, removing them was chosen as it had minimal impact.

So after pre-processing, I had four sets of data:

1. Dataset with outliers removed and negative values removed as well.
2. Dataset with outliers removed but with the absolute values of the negative sourcing values.
3. Dataset with outliers clipped to the upper and lower bound, and the negative values removed.
4. Dataset with outliers clipped to the upper and lower bound, but with the absolute values of the negative sourcing values.

## 3. Correlation and Association Analysis:
- Multicollinearity is a significant concern in regression tasks, so I checked the correlation between the independent variables and the association between the independent variables and the dependent variable:
  - Used Kruskal test to assess 'Sourcing Cost' association with categorical variables.
  - Applied Cramer's V for measuring categorical variable associations, identifying strong correlations.
  - Identified and removed the 'Area' column due to strong correlations with other categorical variables, mitigating multicollinearity concerns.

## 4. Modelling 
   - ### 1st approach - 
   The first approach involved implementing regression models such as Linear Regression (LR) and ensemble models like Random Forest Regressor (RFC) and XGBoost Regressor (XGB). These models were trained on the training data with the aim of predicting the sourcing costs. To ensure better generalizability and prevent overfitting, K-Fold cross-validation with 5 splits was applied. The reason for choosing the ensemble methods was because there were non - linearities in the data which couldn't be captured by simple regression models.

   #### Model Performance:
   - Linear Regression (LR):

    The LR model exhibited poor performance both on the training and test datasets, with an R2 score of 0.37 on the training data and only 0.20 on the test data. Additionally, it had a relatively high RMSE of 42.78 on the training data and 46.3 on the test data. This suggests that the LR model struggled to capture the underlying relationship between the features and the target variable.

- Random Forest Regressor (RFC) and XGBoost Regressor (XGB):

    In contrast, both RFC and XGB models performed significantly better than LR. They demonstrated higher R2 scores on both the training and test datasets, indicating better fitting to the data. RFC achieved an R2 score of 0.56 on the training data and 0.375 on the test data, with corresponding RMSE values of 37.02 and 41.04. Similarly, XGB achieved R2 scores of 0.55 on the training data and 0.36 on the test data, with RMSE values of 37.03 and 41.05, respectively.
  
   ### Results Table - 

  #### Train Data:

      | Model | R2 Score | RMSE  |
      |-------|----------|-------|
      | LR    | 0.37     | 42.78 |
      | RFC   | 0.56     | 37.02 |
      | XGB   | 0.55     | 37.03 |

  #### Test Data:

      | Model | R2 Score | RMSE  |
      |-------|----------|-------|
      | LR    | 0.20     | 46.3  |
      | RFC   | 0.375    | 41.04 |
      | XGB   | 0.36     | 41.05 |

    Overall, the ensemble models, particularly Random Forest and XGBoost, outperformed the Linear Regression model in predicting the sourcing costs. However, even the best-performing models (RFC and XGB) showed limitations in accurately predicting the sourcing costs, as evidenced by the moderate R2 scores and relatively high RMSE values on the test dataset

   - ### 2nd approach - 
   In the second approach, I grouped the duplicate rows based on all the categorical columns. For each group, I aggregated the sourcing costs, summing them to obtain the total sourcing cost for that group. Additionally, I counted the quantity of each product within the group. This was achieved by counting the number of rows corresponding to each group and storing the counts in a new column named "Quantity." Finally, I calculated and created another feature as the average sourcing cost per unit by dividing the total sourcing cost by the quantity for each group which was then used in training of the model, Otherwise directly using the summed up sourcing cost column would have largely deviated from the original values.

   This approach led to very good `r2_score` of `0.95` on the training set and a `rmse` of `40.2` but again when applied on test data the model failed to perform well and the `r2_score` dropped down to `0.40`. The plots below display that the predictions and the original data points are not overlapping indicating the failure of the model to capture the true relationship of data.

   - ### 3rd approach
   This approach included a combination of Time Series analytics and regression. As we had a datetime column in the dataset, I thought to make use of it. So first of all as we do in a time series problem made the index of the dataframe to `Month of Sourcing` column and then created additional features for modelling
    - Lagged features for the 'Sourcing Cost' considering the past three months
      - To capture the historical values of sourcing costs.These lagged features will allow the model to consider the effect of past sourcing costs on the current value, which can improve prediction accuracy in time series analysis.

    - Rolling mean and standard deviation for the past 3 months
      - To capture the trend and variability in sourcing costs over a rolling window of 3 months, providing additional information for the model to learn from.

    - Additional rolling quantiles
      - For understanding  the spread and central tendency of the data.

    - Month, Year, and Quarter from the index
      -  For seasonal analysis and trend identification at different time granularities, aiding in understanding the temporal patterns and cyclicality present in the data.
    
    After this data preperation Random Forest Algorithm for applied for predicting the sourcing cost value. This approach gave the best reuslt amongst all. A `r2_score` of `0.99` and  `rmse` of `1.78` on the training data and `r2_score` of `0.98` and `rmse` of `5.21` on test data. The plot of the original sourcing values and the predicted also shows that this time the model was able to capture the relationship of the data, hence performing well.

## Final Approach: Time Series Analytics and Regression



### Data Preprocessing for Time Series Analysis:

- Transformed dataset into time series format by setting index to 'Month of Sourcing'.
- Created lagged features for 'Sourcing Cost' to capture historical values.
- Calculated rolling statistics to understand trend and variability over time.
- Extracted additional temporal features to capture seasonal patterns and trends.

### Model Selection and Training:

- Applied Random Forest Algorithm for its effectiveness in handling nonlinear relationships and good generalisation capabilities
- Trained the model on preprocessed dataset containing lagged features, rolling statistics, and temporal attributes.

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


   
