
# Beach Weather Sensors data Analysis

## Executive Summary

Our analysis examined beach sensor data from Chicago beaches along Lake Michigan, covering 196,367 hourly measurements from 2015-04-25 09:00:00 to 2025-12-04 13:00:00 across three weather stations. A complete 9-phase data science workflow was employed to understand temporal and seasonal patterns in beach weather conditions using Air Temperature as our target and build its predictive models.We have included key findings such as strong seasonal temperature patterns, significant daily cycles, and successful prediction models comparing the performance of each predictive model(Linear Regression, Rand Forest and XGBoost). The XGBoost model emerged as the best performer, with a test R² of 0.9748  and RMSE of 1.61 °C, demonstrating that air temperature can be predicted with good accuracy from temporal features, rolling windows of predictor variables, and weather variables.

## Phase-by-Phase Findings

### Phase 1-2: Exploration

Initial exploration revealed a dataset of **196,367 records** with 18 columns including temperature measurements (air and wet bulb), wind speed and direction, humidity, rain intensity, interval rain, total rain, precipitation type, battery life, station name , barometric pressure, solar radiation, and sensor metadata. The data spans from April 25, 2015 to November 18, 2025, with measurements from three different weather stations: 63rd Street Weather Station, Foster Weather Station, and Oak Street Weather Station. The total duration from April 2025 to November 2025 was 10 years, 7 month, 16 day, 4 hours.

**Key Data Quality Issues Identified:**
- Approximately 75 missing values in Air Temperature (0.0%)
- Approximately 146 missing values in Barometric Pressure (0.01%)
- Missing values in Rain Intensity, Total Rain, Precipitation Type, and Heading (same 75,974 records (38.7%))
- Approximately 75,974 missing values in Wet Bulb Temperature (38.7%) - significant portion of data
- 6 outliers were detected using the IQR methods, Bounds: [7.10, 19.90]
- Outliers were handled using the capping method to preserve the efficiency of the dataset to avoid removing multiple rows which would have biased our estimates. 
- Data collected at hourly intervals with some gaps
- No duplicates were found.

Initial visualizations showed:
- Monthly Air temperature ranging from approximately -21.50°C to 37.60°C
- Clear seasonal patterns visible in temperature data
- Relatively even distribution of records across the three stations based on the histogram for air temperature.



![Figure 1: Initial Data Exploration](q1_visualizations.png)
*Figure 1: Initial exploration visualizations showing distributions of air temperature.*

### Phase 3: Data Cleaning

Our data cleaning approach addressed missing values, outliers, and data type validation. We have handled missing values in numeric columns using forward-fill (appropriate for time series data) followed by median imputation for any remaining gaps. This approach preserved temporal continuity while ensuring complete datasets for modeling.

**Cleaning Results:**
- Rows before cleaning: ***196367***

Missing Data Handling:
- Air Temperature: 75 missing values (0.0%)
  Method: Forward-fill (time series)
  Result: All missing values filled

- Wet Bulb Temperature: 75974 missing values (38.7%)
  Method: Forward-fill (time series)
  Result: All missing values filled

- Rain Intensity: 75974 missing values (38.7%)
  Method: Forward-fill (time series)
  Result: All missing values filled

- Total Rain: 75974 missing values (38.7%)
  Method: Forward-fill (time series)
  Result: All missing values filled

- Precipitation Type: 75974 missing values (38.7%)
  Method: Forward-fill (time series)
  Result: All missing values filled

- Barometric Pressure: 146 missing values (0.1%)
  Method: Forward-fill (time series)
  Result: All missing values filled

- Heading: 75974 missing values (38.7%)
  Method: Forward-fill (time series)
  Result: All missing values filled

Outlier Handling:
- Air Temperature: Detected 97 outliers using IQR method (3�IQR)
  Method: Capped outliers
  Bounds: [-21.50, 47.30]
  Result: 97 value capped

- Wet Bulb Temperature: Detected 279 outliers using IQR method (3�IQR)
  Method: Capped outliers
  Bounds: [-18.40, 39.20]
  Result: 279 value capped

- Humidity: Detected 185 outliers using IQR method (3�IQR)
  Method: Capped outliers
  Bounds: [22.50, 114.50]
  Result: 185 value capped

- Rain Intensity: Detected 6621 outliers using IQR method (3�IQR)
  Method: Capped outliers
  Bounds: [0.00, 0.00]
  Result: 6621 value capped

- Interval Rain: Detected 15851 outliers using IQR method (3�IQR)
  Method: Capped outliers
  Bounds: [0.00, 0.00]
  Result: 15851 value capped

- Total Rain: Detected 11621 outliers using IQR method (3�IQR)
  Method: Capped outliers
  Bounds: [-269.80, 479.00]
  Result: 11621 value capped

- Precipitation Type: Detected 12668 outliers using IQR method (3�IQR)
  Method: Capped outliers
  Bounds: [0.00, 0.00]
  Result: 12668 value capped

- Wind Direction: Detected 0 outliers using IQR method (3�IQR)
  Method: Capped outliers
  Bounds: [-365.00, 635.00]
  Result: 0 value capped

- Wind Speed: Detected 12221 outliers using IQR method (3�IQR)
  Method: Capped outliers
  Bounds: [-0.95, 5.85]
  Result: 12221 value capped

- Maximum Wind Speed: Detected 4024 outliers using IQR method (3�IQR)
  Method: Capped outliers
  Bounds: [-4.80, 11.20]
  Result: 4024 value capped

- Barometric Pressure: Detected 4611 outliers using IQR method (3�IQR)
  Method: Capped outliers
  Bounds: [977.60, 1011.20]
  Result: 4611 value capped

- Solar Radiation: Detected 29486 outliers using IQR method (3�IQR)
  Method: Capped outliers
  Bounds: [-196.50, 327.50]
  Result: 29486 value capped

- Heading: Detected 27532 outliers using IQR method (3�IQR)
  Method: Capped outliers
  Bounds: [340.50, 368.50]
  Result: 27532 value capped

- Battery Life: Detected 6 outliers using IQR method (3�IQR)
  Method: Capped outliers
  Bounds: [7.10, 19.90]
  Result: 6 value capped

Duplicates Removed: 0

Data Type Conversion:
- 'Measurement Timestamp' converted to datetime
- All numeric columns ensured to be numeric types

 Rows after cleaning: ***196367***

Our cleaning process preserved the full dataset size while improving data quality. Missing data and outliers have handled appropriately. The large number of missing values (75974, 38.7%) in Wet Bulb Temperature, Rain Intensity, Total Rain, Precipitation type and Heading  suggests that these sensor variables may not be available at all stations or during certain periods. The outliers observed could indicate errors in data entry or readability.  However, using forward-fill and median imputation approaches and capping methods ensured that we could still use them in our analysis.

### Phase 4: Data Wrangling

Datetime parsing and temporal feature extraction were critical for time series analysis. The `Measurement Timestamp` column was parsed from the format "format='%Y-%m-%d %H:%M:%S" and set as the DataFrame index, making time-based operations feasible.

**Temporal Features Extracted:**
- `hour`: Hour of day (0-23)
- `day_of_week`: 0-6 (0=Mon, 6=Sun)
- `month`: Month of year (1-12)
- `year`: 2015-2025
- `day_name`: Day name (Monday-Sunday)
- `is_weekend`: Binary indicator (1 if Saturday/Sunday), 

The dataset covers approximately 10 year, 7 month, 16 day, 4 hours of hourly measurements (April 2015 to November 2025), providing substantial data for robust temporal analysis.

### Phase 5: Feature Engineering

We used Feature engineering to create derived variables and rolling window statistics which are useful to capture relationships and temporal dependencies.

**Derived Features:**
- `wet_bulb_difference,wet_bulb_humidity_ratio,wet_bulb_humidity_interaction,rain_difference,rain_intensity_ratio,rain_humidity_interaction,Intervrain_humidity_interaction,rain_pressure_interaction,rain_wind_interaction,wind_range,wind_speed_ratio,wind_speed_interaction, wind_humidity_ratio, wind_pressure_interaction, pressure_humidity_ratio, pressure_humidity_interaction, solar_totalrain_interaction, solar_humidity_interaction, solar_pressure_ratio, solar_wind_interaction`
- Note: We excluded features related to Air Temperature from modeling to avoid data leakage which tends to make R square to be closer to perfect prediction.

**Rolling Window Features:**
- `Wet Bulb Temperature_7h_mean` : 7-hour rolling mean of Wet Bulb Temperature
- `Humidity_7h_mean`:7-hour rolling mean of Humidity
- `Total Rain_7h_mean`: 7-hour rolling mean of Total Rain
- `Wind Speed_7h_mean`: 7-hour rolling mean of Wind Speed
- `Barometric Pressure_7h_mean`:7-hour rolling mean of Barometric Pressure
- `Solar Radiation_7h_mean`: 7-hour rolling mean of Solar Radiation
- `Wind Direction_7h_mean`: 7-hour rolling mean of Wind Direction
- `Precipitation Type_7h_mean`: 7-hour rolling mean of Precipitation Type
- `Rain Intensity_7h_mean`: 7-hour rolling mean of Rain Intensity
- `Interval Rain_7h_mean`: 7-hour rolling mean of Interval Rain
- `Battery Life_7h_mean`: 7-hour rolling mean of Battery life
- `Wet Bulb Temperature_24h_mean`: 24-hour rolling mean of Wet Bulb Temperature
- `Humidity_24h_mean`: 24-hour rolling mean of Humidity
- `Total Rain_24h_mean`: 24-hour rolling mean of Total rain
- `Wind Speed_24h_mean`: 24-hour rolling mean of Wind Speed
- `Barometric Pressure_24h_mean`: 24-hour rolling mean of Barometric pressure
- `Solar Radiation_24h_mean`: 24-hour rolling mean of Solar Radiation
- `Wind Direction_24h_mean`: 24-hour rolling mean of Wind Direction
- `Precipitation Type_24h_mean`: 24-hour rolling mean of Precipitation Type
- `Rain Intensity_24h_mean`: 24-hour rolling mean of Rain Intensity
- `Interval Rain_24h_mean`: 24-hour rolling mean of Interval Rain
- `Battery Life_24h_mean`: 24-hour rolling mean of Battery Life
- `Wet Bulb Temperature_30d_mean`: 30 days rolling mean of Wet Bulb Temperature
- `Humidity_30d_mean`: 30 days rolling mean of Humidity
- `Total Rain_30d_mean`: 30 days rolling mean of Total Rain
- `Wind Speed_30d_mean`: 30 days rolling mean of Wind Speed
- `Barometric Pressure_30d_mean`: 30 days rolling mean of Barometric Pressure
- `Solar Radiation_30d_mean`: 30 days rolling mean of Solar Radiation
- `Wind Direction_30d_mean`: 30 days rolling mean of Wind Direction
- `Precipitation Type_30d_mean`: 30 days rolling mean of Precipitation Type
- `Rain Intensity_30d_mean`: 30 days rolling mean of Rain Intensity
- `Interval Rain_30d_mean`: 30 days rolling mean of Interval Rain
- `Battery Life_30d_mean`: 30 days rolling mean of Battery Life

**Categorical Features:**

- Note: `temp_category` was excluded from modeling as it's derived from the target variable. 
- `Wet_bulb_temp_category`: classified as (cold, comfortable, hot, very_hot)
- `humidity_category`: classified as (dry, slightly_humid, humid, very_humid)
- `wind_speed_category`: classified as (very_slow, slow_or_light, moderate, strong)
- `solar_category`: classified as (night_time, low_intensity, medium_intensity, high_intensity)

**Important:** Only rolling windows of predictor variables were created, not the target variable. Creating rolling windows of the target variable (e.g., `air_temp_rolling_7h` when predicting Air Temperature) would cause data leakage. The rolling window features of predictor variables capture temporal dependencies essential for time series prediction.I created derived features by combining existing numeric columns through arithmetic operations like difference, ratio, and interaction terms.

***Decision choice:***
New features can help capture complex relationships in the data that may improve model performance.
While the difference in temperature features can capture sudden changes, interaction terms can reveal how two variables jointly influence our target variable(Air Temperature).
While the 7-hour rolling mean captures short-term trends, daily temporal changes less than 6hours might not be captured in the analysis. However, the 24-hour may help identify daily trends.
#For this analysis, I think weekly 24h rolling features will be most useful to capture both short-term and medium-term trends in the beach sensor data. It will smooth out daily fluctuations while preserving the underlying trend.

### Phase 6: Pattern Analysis

Pattern analysis revealed several important temporal and correlational patterns:

***TEMPORAL TRENDS:***
- Clear seasonal patterns in Air Temperature and Wet Bulb Temperature
- Higher temperatures observed in : August for Air Temperature
- Lower temperatures observed in : January for Air Temperature
- Monthly temperature range for Air Temperature was: -21.50°F to 37.60°F

***SEASONAL PATTERNS:***
- The day of the week with higher temperature was : Thursday
-  Weekly seasonal patterns show peak temperatures : 37.6°F in week number : 34
-  Monthly seasonal patterns show peak temperatures : 37.6°F in September
-  Overall, there was a drecrese trend in the Air Temperatures weekly and monthly

***KEY CORRELATIONS:***
- wind_speed_interaction ↔ Maximum Wind Speed: 0.929 (Suggesting a strong correlation)
- Wet Bulb Temperature ↔ Air Temperature: 0.828 (Strong correlation with the target variable)
- wind_speed_interaction ↔ Wind Speed: 0.779
- Wind Speed ↔ Maximum Wind Speed: 0.597

***Decision choice:*** 
Overall, there is a decrease trend from the time series plot.Both weekly and monthly aggregations  also revaled a decreased pattern for Air Temperature.
Wind_speed_interaction and Maximum Wind Speed are highly correlated with a correlation coefficient of 0.929 suggesting a strong positive correlation. On the other hand, Wet Bulb Temperature was stongly correlated with Air Temperature(0.828), suggesting a strong positive correlation. 

![Figure 2: Pattern Analysis](q5_patterns.png)
*Figure 2: Advanced pattern analysis showing monthly temperature trends, seasonal patterns by week, daily patterns by hour, and correlation heatmap of key variables.*

### Phase 7: Modeling Preparation

Our modelling preparation involved selecting a target variable, performing temporal train/test splitting (ratio 80/20), and preparing features. We identified important features based on the strength of their correlations with the target and included them in the models for prediction. Additionally, we checked for available numeric variables and missing values, using median imputation to address the latter. Air temperature was chosen as the target variable, as it is a key indicator of beach conditions and exhibits predictable patterns.

**Temporal Train/Test Split:**
- Split method: Temporal (80/20 split by time, NOT random)
- Training set: **157093 samples** (earlier data: 25 April 2015 at 09:00 to 06 July 2023 20:00)
- Test set: **39274 samples** (later data: 06 July 2023 21:00:00 to 04 Dec 2025 13:00:00)
- Number of features: 24
- Rationale: Time series data needs temporal splitting to prevent data leakage and achieve realistic evaluation. Randomly splitting time series would result in the model training on future data and testing on past data. This causes data leakage, as the model would see future data during training, leading to artificially inflated performance metrics.
The strength of the correlation with the target was used to identify important features. Then, features with strong, moderate, and weak correlations were included in the models. Temporal features, including 24-hour rolling features for variables interaction that were important, were considered during feature selection.

**Feature Preparation:**
- Features selected (excluding target, non-numeric columns, and features derived from target)
- **Critical:** Excluded features derived from target variable:
- Excluded features with >0.95 correlation to target (No featuress had >0.95)
- Categorical variables (wind_speed_category,solar_category,Station Name,Wet_bulb_temp_category,humidity_category) one-hot encoded
- All features standardized and missing values handled
- Infinite values replaced with NaN then filled with median
- No data leakage: future data excluded from training set, and features derived from target excluded
- Total dataset: **196,367 rows** before split

### Phase 8: Modeling

Three models were trained and evaluated: Linear Regression, Random Forest and XGBoost.

**Model Performance:**

| Model | R² Score | RMSE | MAE |
|-------|----------|------|-----|
| Linear Regression | 0.9359 |2.57 °C |1.93 °C |
| Random Forest | 0.9685 | 1.80 °C | 0.92°C |
| XGBoost |0.9748 | 1.61 °C | 0.90°C |


**Key Findings:**
- Linear Regression achieved good performance (R² = 0.9359), though the model RMSE of 2.57°C indicated that linear relationships alone might still be insufficient for accurate temperature prediction.
- Random Forest achieved a good performance( R² = 0.9685). 
- XGBoost demonstrated a strong performance (R² = 0.9748), highlighting the significance of non-linear modelling and gradient boosting techniques.
- XGBoost significantly outperforms Linear Regression and Random Forest, with RMSE of 1.61°C compared to 2.57°C for Linear Regression and 1.80°C for Random Forest.
- Additionally, XGBoost had the lowest MAE of 0.90 °C compared to 1.93 °C for linear regression and 0.92 °C for Random Forest. This indicates that Xgboost was more accurate on average.


**Feature Importance (XGBoost):**
Top features by importance:
1. `Wet Bulb Temperature_24h_mean` (54.36% importance) - by far the most important, capturing seasonal patterns
2. `wet_bulb_humidity_ratio_24h_mean` (10.35% importance)
3. `wet_bulb_humidity_interaction_24h_mean` (8.62%% importance)
4. `wet_bulb_humidity_ratio` (7.21% importance)
5. `Solar Radiation` (4.31% importance)
6.	`Wet Bulb Temperature`(3.45% importance)
7.	`Station Name_Foster Weather Station`(2.60% importance)
8.	`wet_bulb_humidity_interaction`(2.47% importance)
9.	`month`(1.91% importance)
10.	`solar_category_night_time`(~1.0%)

The wet Buld Temperature_24h_mean feature dominates feature importance, accounting for 54.36% of total importance. This makes intuitive sense - seasonal patterns are the strongest predictor of air temperature. 24h_Rolling windows of predictor variables are important than derived features. The top 10 features account for 97.5% of total importance.

![Figure 3: Model Performance](q8_final_visualizations.png)
*Figure 3: Final visualizations showing model performance comparison, predictions vs actual values, feature importance, and residuals plot for the best-performing XGBoost model.*

### Phase 9: Results

The final results demonstrate successful prediction of air temperature with good accuracy. The XGBoost model achieves a strong performance on the test set, with predictions within 1.61°C on average.

**Summary of Key Findings:**
1. **Model Performance:** XGBoost achieves R² = 0.9748, indicating that 97.53% of variance in air temperature can be explained by the features. All models show reasonable performance (R² > 0.7 for tree-based models).XGBoost achieved lowest RMSE: 1.61°C, MAE = 0.90°C
2. **Feature Importance:** Wet Bulb temperature_24h_mean feature is overwhelmingly the most important predictor (54.36 % importance), highlighting the critical role of seasonal patterns.Top 10 features account for 97.48 % of total importance. Temporal features (hour, month) are important features.
3. **Temporal Patterns:** clear seasonal patterns are critical for accurate prediction.Clear seasonal patterns were identified for Air Temperature. Weekly and monthly cycles are important predictors.
4. **Data Quality:** Cleaning process maintained full dataset while improving reliability(Dataset cleaned: 196,367 → 196,367 rows)
Missing values handled via forward-fill and median imputation
Outliers capped using IQR method and dropped duplicated if any. 
5. **Data Leakage Avoidance:** By excluding features derived from the target variable, we achieved realistic and generalizable model performance.

The residuals plot shows relatively uniform distribution around zero, suggesting the model performs reasonably well across the full temperature range. The predictions vs actual scatter plot shows points mostly distributed around the perfect prediction line with some scatter, indicating good but not perfect accuracy - which is realistic for weather prediction.

## Visualizations

![Figure 1: Initial Data Exploration](q1_visualizations.png)
*Figure 1: Initial exploration showing distributions and time series of key variables.*

![Figure 2: Pattern Analysis](q5_patterns.png)
*Figure 2: Advanced pattern analysis revealing temporal trends, seasonal patterns, daily cycles, and correlations.*

![Figure 3: Model Performance](q8_final_visualizations.png)
*Figure 3: Final results showing model comparison, prediction accuracy, feature importance, and residual analysis.*

## Model Results

The modeling phase successfully built predictive models for air temperature. The performance metrics from this analysis demonstrated that XGBoost performs well, although Linear Regression and random Forest showed good predictions. However, XGBoost were sufficient for this task.

**Performance Interpretation:**
- **R² Score:** Measures proportion of variance explained. XGBoost's R² of 0.9748 means the model explains 97.48% of variance in air temperature - a strong but realistic result.
- **RMSE (Root Mean Squared Error):** Average prediction error in original units. XGBoost's RMSE of 1.61°C indicates that predictions are typically within 1.61°C of actual values - reasonable for weather prediction.
- **MAE (Mean Absolute Error):** Average absolute prediction error. XGBoost's MAE of 0.90°C indicates good predictive accuracy.

**Model Selection:** XGBoost is selected as the best model due to:
1. Highest R² score (0.9748)
2. Lowest RMSE (1.61°C)
3. Lowest MAE (0.90°C)
4. Excellent generalization (train R² = 0.9843, test R² = 0.9748 - reasonable)

**Feature Importance Insights:**
The feature importance analysis reveals that:
- The wet BulB Temperature_24h_mean feature is overwhelmingly the most important predictor (54.36% importance)
- This suggests that temporal and seasonal patterns are the strongest predictors of air temperature
- Wet Bulb_humidity ratio, solar radiation, wet bulb temperature, Station Name_Foster Weather Station are important but secondary to 24h_rolling window features.
- Month contribute but is less important than temporal rolling window features.
- solar_category_night_time has minimal impact. 

**Note on Data Leakage Avoidance:** By excluding features derived from the target variables, we achieved realistic model performance. This demonstrates the importance of careful feature selection to avoid circular logic.

## Time Series Patterns

The analysis revealed several important temporal patterns:

**Long-term Trends:**
- Stable long-term trends over the 10.7-year period
- There is decreasing trend both monthly and weekly
- Consistent seasonal cycles year over year. There is similar oscillating pattern.

**Seasonal Patterns:**
- **Monthly:** Clear seasonal cycle with temperatures peaking in September and reaching minima in December
- Monthly air temperature range: -21.50°C to 37.60°C
- Higher temperatures observed in : August for Air Temperature
- Lower temperatures observed in : January for Air Temperature
- **Weekly:** The day of the week with higher temperature was : Thursday
-  Weekly seasonal patterns show peak temperatures : 37.6°C in week number : 34
- Weekly patterns are consistent across seasons, though amplitude varies
-  Overall, there was a drecrese trend in the Air Temperatures weekly and monthly

**Temporal Relationships:**
- Air temperature and Wet Bulb Temperature show strong seasonal patterns (month is the most important predictor)
- Wet Bulb Temperature_24h_mean showed strong predictive correlation with temperature (0.5436)
- Rolling windows of predictor variables (Wet Buld Temperature_24h_mean, wet_bulb_humidity_ratio_24h_mean, wet_bulb_humidity_ratio_24h_mean) capture temporal dependencies. 
- solar_totalrain_interaction_24h_mean,rain_difference_24h_mean,Total Rain_24h_mean,Total Rain,rain_difference showed a moderate predictive correlation with air temperature. 
- Solar radiation shows very weak correlation with temperature (0.007)

**Anomalies:**
- Large gap in Wet Bulb Temperature data (75974 missing values, 38.7% of dataset)
- This likely represents periods when certain sensors might have not been operational
- Some sensor dropouts identified in 2020 and 2021. This refers to gaps identified in time series plots.
- No major anomalies in temporal patterns beyond expected seasonal variation

Since temporal patterns are essential for accurate prediction, as shown by the high importance of temporal features, especially rolling windows of 24h in the model, it is important to account for gaps that might affect the prediction accuracy. However, our analytical approach included over 196 thousand observations and forward fillind and median imputation for importance faetures, which may have helped in addressing these gaps.. 

## Limitations & Next Steps

**Limitations:**

1. **Data Quality:**
   - Large number of missing values in Wet Bulb Temperature (38.7%) required imputation, which may introduce bias.
   - Sensor dropouts in 2020 and 2021 create gaps in time series that could affect pattern detection
   - Outlier capping may have removed some valid extreme events which might also bias the results
   - Only 3 weather stations - limited spatial coverage

2. **Model Limitations:**
   - Despite the good performance of Linear Regression, it has a lower R square compared to XGBoost. Additionally, it has a larger RMSE of 2.57 °C versus 1.61 °C for XGBoost. This suggests that linear relationships are insufficient for this task and that XGBoost was more suitable for capturing non-linear relationships. 
   - XGBoost demonstrates excellent generalisation (train R² = 0.9845 vs test R² = 0.9685)
   - The model relies heavily on seasonal features (Wet Bulb Temperature_24h_mean (54.36% importance)), which limits its predictive power for same-season forecasts.
   - Our models were trained on historical data. However, they might not be generalisable to future climate conditions due to seasonality and external factors.
   - RMSE of 1.61°C, while indicating good predictive accuracy, may not be sufficient for applications requiring higher precision in different settings where low temperature variation is crucial. 


3. **Feature Engineering:**
   - Some potentially useful features may not have been created (e.g., lag features, square terms)
   - Initially, rolling window sizes (7h, 24h) were chosen arbitrarily. However, data exploration revealed that 24h rolling made more sense for our analysis to capture daily trends.
   - Features derived from target variable were correctly excluded to avoid data leakage
   - We did not incorporate external data in our analysis.

4. **Scope:**
   - Analysis focused on air temperature prediction; other targets (e.g., precipitation, total rain, maximum wind speed, humidity) not explored. 
   - Only one target variable analyzed; multi-target modeling could provide additional insights such as interdependencies between variables.
   - Our analysis reveal that spatial stations were also an important feature, though mininal.However, spatial relationships between stations were not analyzed

**Next Steps:**

1. **Model Improvement:**
   - Experiment with different rolling window sizes(12h, 48h, 72h), lag features and square terms to capture additional temporal patterns. 
   - Try additional models with total number of importance features to boost performance. 
   - Incorporate external data sources (weather forecasts, lake level data)
   - Try ensemble methods combining multiple models
   - Validate model on truly out-of-sample data (future dates)
   - Cross-feature interactions (e.g., temperature × humidity)
  -  Seasonal interaction terms (e.g., month × temperature patterns and year  x temperature) to address the gaps observed in time series plots in 2020 and 2021. 
2. **Feature Engineering:**
   - Add lag features (3h, 6h, 12h, 24h, 48h lags) 
   - Incorporate spatial features (distance between stations, station-specific effects)
   - Create weather condition categories

3. **Analysis Extension:**
   - Predict other targets (Maximum wind speed, humidity, total rain, precipitation)
   - Analyze station-specific patterns and differences (e.g: Geographic distance effects on temperature)
   - Investigate sensor reliability and data quality by location to avoid temperature outliers
   - Build forecasting models for future predictions
   - Analyze spatial relationships between stations

4. **Validation:**
   - Cross-validation with temporal splits (e.g., train on months 1-9, validate on 10-12, test on future year)
   - Validation on additional time periods 
   - Evaluate model performance across different seasons and weather conditions separately
   - Comparison with physical models (if available)
   - Sensitivity analysis on feature importance
   - Further investigation of feature engineering to improve Linear Regression performance

5. **Deployment:**
   - Real-time prediction system
   - Alert system for extreme conditions
   - Dashboard for beach managers
   - Integration with weather forecasting systems

## Conclusion

In our analysis, we successfully applied a complete 9-phase data science workflow to Chicago Beach Weather Sensors data, achieving good air temperature predictions (R² = 0.9748, RMSE = 1.61°C). The project demonstrated the importance of temporal feature engineering, particularly seasonal features (especially, Wet Bulb Temperature_24h_mean), which dominated feature importance (54.36% importance). Key insights include strong seasonal and weekly patterns, the critical role of temporal features in prediction, and the superior performance of ensemble tree-based models (Random Forest and XGBoost) over linear models. In our analysis, we demonstrated proper data leakage avoidance by excluding features derived from the target variable and those highly correlated with it, resulting in realistic and generalizable model performance. This provides a solid foundation for beach condition monitoring and prediction systems. However, future analysis is required to explore the full predictive ability of our model under different climatic conditions and requirements. 
