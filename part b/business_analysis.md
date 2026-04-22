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

Additionally, this model can be used in a **what-if analysis framework**, where different promotion types can be simulated to identify the optimal strategy for each store.

---

### (b) Why Items Sold is a Better Target than Revenue

Using **items sold** instead of revenue provides a more reliable measure of promotion effectiveness.

* Revenue can be influenced by pricing strategies, discounts, and product mix
* A promotion may increase the number of items sold but reduce total revenue due to heavy discounts

**Key Insight:**
Items sold directly captures **customer demand and response**, which is the primary objective of promotional campaigns.

**Broader Principle:**
In real-world machine learning projects, the target variable should align closely with the **true business objective** and should not be distorted by external factors. Choosing an inappropriate target variable can lead to misleading insights and suboptimal decisions.

---

### (c) Alternative Modelling Strategy

Instead of using a single global model, a more effective approach would be:

* **Segmented Modelling**

  * Train separate models for:

    * Urban stores
    * Semi-urban stores
    * Rural stores

**OR**

* Use a global model enriched with **store-level features and interaction terms**

**Justification:**
Different store types respond differently to promotions due to variations in customer demographics, purchasing power, and competition. This approach captures **localized behavior patterns**, improving model accuracy and relevance.

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
* Join promotion details using promotion identifiers or transaction dates
* Join calendar data using `transaction_date`

**Grain of Final Dataset:**
One row per **store per month**

**Aggregations:**

* Total items sold per store per month
* Average basket size
* Monthly footfall
* Promotion applied during the month
* Festival and weekend indicators

**Important Consideration:**
Care must be taken to ensure that only information available up to the prediction period is used, avoiding **data leakage** from future data into the model.

---

### (b) Exploratory Data Analysis (EDA)

The following analyses should be performed:

1. **Promotion vs Sales (Bar Chart)**
   Compare average items sold across promotion types to identify the most effective promotions

2. **Sales Trends Over Time (Line Chart)**
   Analyze monthly sales patterns to detect seasonality and trends

3. **Correlation Heatmap**
   Identify strong predictors of `items_sold`, which can guide feature selection and model design

4. **Sales Distribution (Histogram / Boxplot)**
   Detect skewness and outliers, which may require transformation or robust modelling techniques

**Impact on Modelling:**

* Helps identify key drivers of sales
* Guides feature engineering decisions
* Detects anomalies and data quality issues
* Improves model reliability

---

### (c) Handling Promotion Imbalance

Since 80% of transactions occur without promotions:

**Impact:**

* The model may become biased toward non-promotion scenarios
* Reduced ability to accurately learn promotion effects

**Solutions:**

* Apply resampling techniques (oversampling/undersampling)
* Introduce a binary feature indicating promotion presence
* Use weighting strategies during model training
* Evaluate model performance separately for promotion and non-promotion cases

---

## B3. Model Evaluation and Deployment

### (a) Train-Test Split and Metrics

**Train-Test Strategy:**

* Use a **time-based split**
* Train on historical data and test on the most recent period

**Why Not Random Split:**

* Random splitting can lead to **data leakage**
* It allows future information to influence training, resulting in unrealistic performance estimates

**Evaluation Metrics:**

* **RMSE (Root Mean Squared Error):**
  Penalizes large prediction errors more heavily, which is important when large forecasting mistakes can lead to inventory or staffing issues

* **MAE (Mean Absolute Error):**
  Measures average prediction error and provides an easily interpretable metric for business stakeholders

**Interpretation:**

* Lower RMSE indicates fewer large prediction errors
* Lower MAE indicates consistent prediction accuracy across stores

---

### (b) Explaining Model Recommendations

To understand why different promotions are recommended for the same store in different months:

* Use **feature importance** from models such as Random Forest
* Analyze the impact of:

  * Seasonal factors (month)
  * Festival indicators
  * Historical sales trends

**Example:**

* December → High demand → Loyalty Points Bonus encourages repeat purchases
* March → Lower demand → Flat Discount attracts price-sensitive customers

Additionally, **SHAP (SHapley Additive exPlanations)** values can be used to provide detailed, instance-level explanations of model predictions.

**Communication Strategy:**

Explain to stakeholders that:

> The model adapts recommendations based on seasonal demand patterns, customer behavior, and store characteristics, ensuring context-specific decision-making.

---

### (c) Deployment Strategy

**1. Model Saving**

* Save the trained model using tools such as `joblib` or `pickle`

**2. Monthly Prediction Pipeline**

* Collect new monthly data
* Apply the same preprocessing pipeline used during training
* Generate predictions for each store

**3. Automation**

* Schedule predictions at the beginning of each month using automated workflows

**4. Monitoring**

Track:

* Prediction errors (RMSE, MAE)
* Data drift (changes in feature distributions)
* Promotion effectiveness trends

**5. Retraining Triggers**

* Significant drop in model performance
* Changes in customer behavior or market conditions
* Introduction of new promotion strategies

**6. Model Versioning**

* Maintain version control for models and datasets to ensure reproducibility and traceability over time

---

## Final Conclusion

This approach enables the company to make **data-driven promotion decisions**, optimizing sales performance while adapting to regional differences, customer behavior, and seasonal patterns.
