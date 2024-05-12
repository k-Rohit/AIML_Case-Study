# Methodology and Approaches

## 1. Framing of the problem statement

The crux of the problem lies in forecasting the sourcing cost values for the month of June 2021, utilizing the dataset spanning from July 2020 to May 2021.

## 2. Dataset Information and understanding

The dataset comprises sourcing information for various product combinations, spanning from July 2020 to May 2021. Each row represents the sourcing of a single unit of a specific product combination, delineated by attributes denoted in columns A to F. Notably, multiple rows may feature identical product combinations, reflecting individual sourcing instances.

## 3. Exploratory Data Analysis (EDA)
- Explored the dataset dimensions/shape, checked for null values, and examined the data types of the features of the dataset.
- Checked for the features’ data types, identifying anomalies like the min value of Sourcing Cost column being negative.
- Conducted Univariate analysis and Multivariate Analysis, including frequency counts, box plots for outlier detection, and hist plots for distribution of the Sourcing Cost column. The multivariate analysis was guided by the following considerations:
  1. What is the comparison of total sourcing costs for powder and liquid products across different product types (NTM1, NTM2, and NTM3)?
  2. How do the total sourcing costs fluctuate monthly, and which month has the highest sourcing activity or cost?
  3. How do the sourcing costs compare between different manufacturers (X1, X2, X3) for both liquid and powder product types?
  4. What are the sourcing costs associated with different distribution channels (Direct, E-commerce, Retail, Wholesale), and which channel incurs the highest cost?
  5. How do the sourcing costs associated with different product types (liquid, powder) vary across different sourcing channels?
  6. How do manufacturers X1, X2, and X3 utilize different sourcing channels, and what are the comparative sourcing costs for each manufacturer across these channels?
- Investigated negative values in 'Sourcing Cost' and considered the optimum strategies to either remove or keep them.
- Found the presence of outliers in the Sourcing Cost column.

## 4. Pre-processing:
### Outlier Detection and Handling:
For Outlier detection, the IQR (InterQuartile Range) method was used, with two approaches:
  - Removal/Trimming of the outliers from the data.
  - Clipping outliers to lower and upper bound.
  - This Resulted in four sets of data for further analysis. And for both of these two approaches, I handled the negative sourcing cost   values as follows:

#### Handling Negative Sourcing Cost Values:
- For handling the negative values, two approaches were considered: removal of the negative values or taking the absolute values of them.
- Since the number of negative values was relatively small compared to the total dataset size, removing them also didn't have much of an   effect on the performance of the models.

#### So after pre-processing, I had four sets of data:
1. Dataset with outliers removed and negative values removed as well.
2. Dataset with outliers removed but with the absolute values of the negative sourcing values.
3. Dataset with outliers clipped to the upper and lower bound, and the negative values removed.
4. Dataset with outliers clipped to the upper and lower bound, but with the absolute values of the negative sourcing values.

## 5. Analysis of association between different features:
- To ensure robustness in the regression model, I examined the relationships between independent variables, and between independent variables and the dependent variable 'Sourcing Cost':
  - The Kruskal-Wallis test was used to evaluate whether there are statistically significant differences in 'Sourcing Cost' across the categories of each categorical variable, helping to understand how different groups affect the dependent variable.
  - Cramer's V was utilized to measure the strength of association among categorical variables, identifying strong associations that might affect model interpretation.
  - I removed the 'Area Code' column after identifying its strong association with other categorical variables, which could lead to issues similar to multicollinearity when categorical variables are converted to dummy variables for regression analysis.

## 6. Modeling
### Data Preparation for modeling:
After removing the outliers and the negative sourcing values, I dropped the Area Code and the Month of Sourcing Column. The reason for dropping area code was as previously mentioned that it was strongly associated with the other categorical variables which might have hampered  the model’s performance. The reason behind removing the Month of Sourcing Column was that in the training data we had 11 months but in test data we just have 1 month so if we consider one hot encoding it the number of features in the train set would be greater than in the test set which would give error when predicting on the test set. Also if Label Encoder is used there would be 11 different values in the train set but a single value in the test set which could again cause discrepancy and that will hamper the performance of the model.

Now lets see the different approaches adopted - 

### 1st approach:
The first approach involved implementing regression models such as Linear Regression (LR) and ensemble models like Random Forest Regressor (RFR) and XGBoost Regressor (XGB) . These models were trained on the training data with the aim of predicting the Sourcing Costs. To ensure better generalizability of the models and to prevent overfitting, K-Fold cross-validation with 5 splits is applied. Cross-validation involves dividing the dataset into k subsets, training the model on k-1 subsets, and evaluating it on the remaining subset. This process is repeated k times, with each subset used as the test set exactly once. The reason for choosing models like Random Forest and XGBoost is that they have the ability to capture non-linear relationships and complex patterns in the data , which may not be captured effectively by simple regression models. These models combine multiple base models to improve predictive performance and robustness.

#### Model Performance:
- Linear Regression (LR):

    The Linear Regression model exhibited poor performance both on the training and test datasets, with an R2 score of 0.37 on the training data and only 0.20 on the test data. Additionally, it had a relatively high RMSE of 42.78 on the training data and 46.3 on the test data. This suggests that the LR model struggled to capture the underlying relationship between the features and the target variable, and this is evident from the plot of the original Sourcing cost value and the predicted ones, where it can be seen that the model is not able to capture the underlying pattern and is largely underfitting.

- Random Forest Regressor (RFR) and XGBoost Regressor (XGB):

    In contrast, both Random Forest Regressor and XGBoost Regressor models performed significantly better than Linear Regression. They demonstrated higher R2 scores on both the training and test datasets as compared to the Linear Regression model, indicating a better fitting to the data. RFC achieved an R2 score of 0.56 on the training data and 0.375 on the test data, with corresponding RMSE values of 37.02 and 41.04. Similarly, XGB achieved R2 scores of 0.55 on the training data and 0.36 on the test data, with RMSE values of 37.03 and 41.05, respectively. Although they performed better than the LR model still the results on the test data are not satisfactory, and the plot also conveys the same.
  
#### Results Table:
- Train Data:

   | Model | R2 Score | RMSE  |
   |-------|----------|-------|
   | LR    | 0.37     | 42.78 |
   | RFC   | 0.56     | 37.02 |
   | XGB   | 0.55     | 37.03 |

- Test Data:

   | Model | R2 Score | RMSE  |
   |-------|----------|-------|
   | LR    | 0.20     | 46.3  |
   | RFC   | 0.375    | 41.04 |
   | XGB   | 0.36     | 41.05 |

    It can be clearly seen that the model is overfitting its performance drops on the test data drastically.

    Both Random Forest and XGB models were applied on all the four sets of data that were mentioned above in the pre-processing section and the results displayed are the average of the metrics that I obtained from the four datasets, but there was no significant change in the metrics on any particular set of data, so for further approaches, I considered the dataset in which the outliers and the negative Sourcing Cost values had been removed as outliers and negative Sourcing Values can distort the underlying distribution of the data and introduce noise into the model training process.

    In this approach, I also performed Hyperparameter tuning of Random Forest Algorithm (max_depth, min_samples_leaf, min_samples_split, n_estimators) but even after that the results were not very significant. The best `r2_score` and `rmse` was 0.56 and 35.86 respectively which are almost the same as before doing it.

    Overall, the ensemble models, particularly Random Forest and XGBoost, outperformed the Linear Regression model in predicting the sourcing costs. However, even the best-performing models (RFC and XGB) showed limitations in accurately predicting the sourcing costs, as evidenced by the low R2 scores and relatively high RMSE values on the test dataset indicating that another approach to the problem is required.

### 2nd approach:
 In the second approach, I grouped the duplicate rows based on all the categorical columns. For each group, I aggregated the sourcing costs, summing them to obtain the total sourcing cost for that group. Additionally, I counted the quantity of each product within the group. This was achieved by counting the number of rows corresponding to each group and storing the counts in a new column named "Quantity." Finally, I calculated and created another feature as the average sourcing cost per unit by dividing the total sourcing cost by the quantity for each group which was then used in training of the model, Otherwise directly using the summed up sourcing cost column would have largely deviated from the original values.

  This approach led to a very good `r2_score` of `0.95` on the training set and a `rmse` of `40.2` but again when applied on test data the model failed to perform well and the `r2_score` dropped down to `0.40`. The plot displays that the predictions and the original data points are not overlapping indicating the failure of the model to capture the true relationship of data.

### 3rd approach
In this approach I again didn’t consider the Area code but as this approach includes a time series approach so the Month of Sourcing column was effectively used in this case.

So this approach included a combination of Time Series analytics and regression. As we had a datetime column in the dataset, I thought to make use of it and see if that would improve the model’s performance. So first of all as we do in a time series problem I made the index of the dataframe to the `Month of Sourcing` column, sorted the data frame according to the index and then created additional features for modeling. They include - 
  - Lagged features for the 'Sourcing Cost' considering the past three months - To capture the historical values of sourcing costs.These lagged features will allow the model to consider the effect of past sourcing costs on the current value, which can improve prediction accuracy in time series analysis.

  - Rolling mean and standard deviation for the past 3 months - To capture the trend and variability in sourcing costs over a rolling window of 3 months, providing additional information for the model to learn from.

  - Additional rolling quantiles - For understanding  the spread and central tendency of the data.

  - Month, Year, and Quarter from the index  -  For seasonal analysis and trend identification at different time granularities, aiding in understanding the temporal patterns and cyclicality present in the data.
    
  After this data preparation and feature engineering, Random Forest Algorithm and XGBoost Algorithm were applied for predicting the sourcing cost value. This approach gave the best result amongst all the approaches and models training. For Random Forest, the `R2 score` was `0.998` and `RMSE `was `1.78` on the training data, while on the test data, the `R2 score` was `0.98` and RMSE was `5.21`. The plot of the original sourcing values and the predicted values also shows that the Random Forest model was able to capture the relationship of the data, hence performing well. Similarly, for XGBoost, the `R2 score` was `0.993` and `RMSE` was `4.19` on the training data, while on the test data, the R2 score was `0.994` and `RMSE` was `3.89`. The plot of the original Sourcing Cost values and the predicted values also shows that the XGBoost model more prominently captured the patterns in the data. Another interesting observation was the time both alorithm took - XgBoost was way faster than Random Forest while training both.


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

1. **Sequential Nature of Data**: Each row represented the sourcing cost of a product over different months, making it suitable for time series analysis and applying the time series techniques.
   
2. **Temporal Patterns and Trends**: Leveraged the temporal order of data which helped to capture underlying patterns and trends influencing the sourcing costs.
   
3. **Historical Insights**: The use of lagged features captured historical sourcing costs, allowing the model to learn from past observations.
   
4. **Seasonality and Cyclicality**: Extraction of temporal features like month, year, and quarter enabled capturing seasonal patterns and trends at different granularities.
   
5. **Model Selection**: Random Forest Regression was chosen for its robustness in handling nonlinear relationships and it being a ensemble method it handles overfitting and is good in generalisation to unseen data.


Using the rolling statistics, lagged sourcing cost values, quantile values and the month, and year resulted in an effective model which was able to predict the Sourcing Cost in the test data as close to the original data and therefore making this approach suitable for adoption.


# Plots showing how different models are performing on test data.

<div style="display: flex; flex-wrap: nowrap; justify-content: center; align-items: center; overflow-x: auto;">
    <div style="flex: 0 0 auto; margin-right: 20px;">
        <p>Linear Regression</p>
        <img width="500" src="https://github.com/k-Rohit/AP_Moller_Maersk_Assignment/assets/93335681/5ddcbb06-677c-4d70-913a-549ea4e77aec">
    </div>
    <div style="flex: 0 0 auto; margin-right: 20px;">
        <p>Random Forest</p>
        <img width="500" src="https://github.com/k-Rohit/AP_Moller_Maersk_Assignment/assets/93335681/bed239f5-f8ad-4b82-b7a8-d90b1ccbf1fd">
    </div>
    <div style="flex: 0 0 auto; margin-right: 20px;">
        <p>Xg Boost</p>
        <img width="500" src="https://github.com/k-Rohit/AP_Moller_Maersk_Assignment/assets/93335681/3c2c0e61-828f-464c-a48d-5567f74cfce1">
    </div>
    <div style="flex: 0 0 auto;">
        <p>Random Forest with Time Series features</p>
        <img width="500" src="https://github.com/k-Rohit/AP_Moller_Maersk_Assignment/assets/93335681/cfdbe215-cf6c-40a4-92f4-c4e2c5623db6">
    </div>
  <div style="flex: 0 0 auto;">
        <p>Xg Boost with Time Series features</p>
        <img width="500" src="https://github.com/k-Rohit/AP_Moller_Maersk_Assignment/assets/93335681/73c6b50d-2e34-42db-a396-eedb045f8a06">
    </div>
</div>



   
