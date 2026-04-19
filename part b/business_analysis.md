# Business Case Analysis — Promotion Effectiveness

---

## B1. Problem Formulation

### (a) Machine Learning Problem Definition

The objective is to determine the most effective promotion strategy for each store to maximise sales performance.

* **Target Variable:**
  `items_sold` (number of items sold per store per month)

* **Input Features:**

  * Store attributes: store_size, location_type, competition_density
  * Customer behavior: footfall, historical sales
  * Promotion type: Flat Discount, BOGO (Buy-One-Get-One), Free Gift, Category-Specific Offer, Loyalty Points Bonus
  * Temporal features: month, seasonality, festivals, weekends
  * Historical performance metrics

* **Type of Problem:**
  This is a **supervised regression problem**, as we are predicting a continuous numerical value (`items_sold`) based on historical data.

**Justification:**
The goal is to estimate expected sales under different promotional strategies. Since the output is continuous and depends on input features, regression is the most appropriate approach.

---

### (b) Why Items Sold is a Better Target than Revenue

Using **items sold** instead of revenue provides a more reliable measure of promotion effectiveness.

* Revenue can be influenced by pricing strategies, discounts, and product mix
* A promotion may increase the number of items sold but reduce total revenue due to heavy discounts

**Key Insight:**
Items sold directly captures **customer demand and response**, which is the primary goal of promotions.

**Broader Principle:**
In real-world machine learning projects, the target variable should align closely with the **business objective** and should not be distorted by external factors. Choosing the wrong target can lead to misleading model outcomes.

---

### (c) Alternative Modelling Strategy

Instead of using a single global model, a better approach would be:

* **Segmented Modelling**

  * Train separate models for:

    * Urban stores
    * Semi-urban stores
    * Rural stores

**OR**

* Use a global model with **store-level features and interaction terms**

**Justification:**
Different store types respond differently to promotions due to variations in customer demographics, purchasing power, and competition. This approach captures **localised behaviour patterns** and improves model accuracy.

---

## B2. Data and EDA Strategy

### (a) Data Joining and Dataset Design

The data is provided in four tables:

* Transactions
* Store attributes
* Promotion details
* Calendar data

**Joining Strategy:**

* Join transactions with store attributes using `store_id`
* Join promotion details using promotion identifier or date
* Join calendar data using `transaction_date`

**Grain of Final Dataset:**
One row per **store per month**

**Aggregations:**

* Total items sold per store per month
* Average basket size
* Monthly footfall
* Promotion applied during the month
* Festival and weekend indicators

This ensures consistency and aligns the dataset with the prediction objective.

---

### (b) Exploratory Data Analysis (EDA)

The following analyses should be performed:

1. **Promotion vs Sales (Bar Chart)**
   Compare average items sold across promotion types to identify the most effective promotions

2. **Sales Trends Over Time (Line Chart)**
   Analyze monthly sales patterns to detect seasonality and trends

3. **Correlation Heatmap**
   Identify relationships between numerical features and the target variable

4. **Sales Distribution (Histogram / Boxplot)**
   Detect skewness and outliers, which may require transformation

**Impact on Modelling:**

* Helps in feature selection
* Identifies key drivers of sales
* Detects anomalies and data quality issues

---

### (c) Handling Promotion Imbalance

Since 80% of transactions occur without promotions:

**Impact:**

* Model may become biased toward non-promotion scenarios
* Reduced sensitivity to promotional effects

**Solutions:**

* Apply resampling techniques
* Introduce a binary feature indicating promotion presence
* Use weighting strategies
* Evaluate model performance separately for promotion and non-promotion cases

---

## B3. Model Evaluation and Deployment

### (a) Train-Test Split and Metrics

**Train-Test Strategy:**

* Use a **time-based split**
* Train on earlier data and test on the most recent period

**Why Not Random Split:**

* Random splitting can cause **data leakage**
* It uses future data to predict past outcomes, which is unrealistic

**Evaluation Metrics:**

* **RMSE (Root Mean Squared Error):**
  Penalizes large errors more heavily

* **MAE (Mean Absolute Error):**
  Measures average prediction error

**Interpretation:**

* Lower RMSE indicates fewer large mistakes
* Lower MAE indicates consistent prediction accuracy

---

### (b) Explaining Model Recommendations

To understand why different promotions are recommended:

* Use **feature importance** from models like Random Forest
* Analyze the impact of:

  * Month (seasonality)
  * Festival indicators
  * Historical sales patterns

**Example:**

* December → High demand → Loyalty Points encourage repeat purchases
* March → Lower demand → Flat Discounts attract price-sensitive customers

**Communication:**
Explain to stakeholders that:

> The model adapts recommendations based on seasonal demand patterns and customer behavior across different time periods.

---

### (c) Deployment Strategy

**1. Model Saving**

* Save the trained model using tools like `joblib` or `pickle`

**2. Monthly Prediction Process**

* Collect new monthly data
* Apply the same preprocessing pipeline
* Generate predictions for each store

**3. Automation**

* Schedule predictions at the beginning of each month

**4. Monitoring**
Track:

* Prediction errors (RMSE, MAE)
* Changes in data distribution (data drift)
* Promotion performance trends

**5. Retraining Trigger**

* Significant drop in model performance
* Changes in customer behavior
* Introduction of new promotion strategies

---

## Final Conclusion

This approach enables the company to make **data-driven promotion decisions**, improving sales performance while adapting to regional and seasonal variations.

