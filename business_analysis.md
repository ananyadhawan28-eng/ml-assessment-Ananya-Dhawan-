# Part B — Business Case Analysis: Promotion Effectiveness at a Fashion Retail Chain

---

## B1. Problem Formulation

### B1(a) — ML Problem Formulation

Based on my understanding, the goal is to predict how many items will be sold under a specific promotion at a store in a given month.

**Target Variable:**
`items_sold` — number of items sold

**Input Features:**

* Store details like store size, location type (urban, semi-urban, rural), monthly footfall, competition density
* Promotion type (Flat Discount, BOGO, Free Gift, etc.)
* Time-related features like month, weekend, festival, month-end
* Past sales performance of store

**Type of Problem:**
This is a **supervised learning regression problem** because we already have past data with actual sales values, and the output (items_sold) is a continuous number.

The idea is to use past data to predict future sales so that the marketing team can choose the best promotion for each store.

---

### B1(b) — Why Items Sold is Better than Revenue

In this case, using revenue can be misleading. For example, if a flat discount is applied, the price of items reduces, so even if more items are sold, revenue may not increase much.

On the other hand, `items_sold` directly shows customer response to a promotion, without being affected by price changes.

So, items sold is a better measure of how effective a promotion is.

**Important learning:**
The target variable should match the actual business goal. If we choose the wrong target, the model may optimise the wrong thing.

---

### B1(c) — Alternative to One Global Model

Using one single model for all stores may not work well because different stores behave differently.

For example, urban stores may respond better to some promotions while rural stores may respond differently.

So instead of one model, I would:

* Group similar stores based on location, size, and past sales
* Train separate models for each group

This way, the model can learn patterns specific to each type of store.

---

## B2. Data and EDA Strategy

### B2(a) — Joining the Tables

The data comes from multiple tables, so we need to combine them properly.

* transactions + store attributes → using `store_id`
* transactions + promotion details → using promotion info
* transactions + calendar → using date

**Final dataset:**
One row represents → one store + one month + one promotion

Before modelling, I would aggregate:

* Total items_sold per store per month per promotion
* Number of active days in that month
* Average competition density if needed

---

### B2(b) — EDA Plan

Before building the model, I would first explore the data to understand patterns.

1. **Sales by promotion type**
   This helps to see which promotions generally perform better and how consistent they are.

2. **Monthly sales trend**
   This helps to identify seasonality (for example, higher sales during festivals).

3. **Location vs promotion performance**
   This shows if certain promotions work better in specific locations.

4. **Correlation between features**
   This helps to check if some features are strongly related and may cause issues in modelling.

These insights will help in feature selection and better modelling decisions.

---

### B2(c) — Handling Promotion Imbalance

Since 80% of data has no promotion, the model may become biased and not learn the effect of promotions properly.

To handle this, I would:

* Give more importance (weight) to promotion data
* Create a feature like `has_promotion`
* Also, try training a separate model for promotion vs no-promotion cases

This will help the model learn promotion impact better.

---

## B3. Model Evaluation and Deployment

### B3(a) — Train-Test Split Strategy

Since this is time-based data, we should not use random split.

Instead, I would:

* Use first ~80% of time data for training
* Use last ~20% for testing

Random split can mix past and future data, which gives unrealistic results.

**Metrics I would use:**

* **RMSE** → penalises large errors
* **MAE** → easy to understand average error
* **R² score** → shows how well model explains data

---

### B3(b) — Understanding Model Recommendations

If the model suggests different promotions for the same store in different months, I would try to understand why.

For this, I would:

* Check feature importance
* Use tools like SHAP to see which features influenced the prediction

For example:

* In December, festival season may increase effectiveness of loyalty-based promotions
* In March, discounts may work better due to lower demand

This can be explained to the marketing team in simple terms.

---

### B3(c) — Deployment Process

After training, the model can be saved using joblib.

Each month:

* New data will be prepared in the same format
* Model will predict expected sales for each promotion
* Best promotion will be selected based on highest predicted value

For monitoring:

* Compare predicted vs actual sales monthly
* If error increases significantly, retrain the model

Also, in real-world scenarios, business decisions may also depend on budget and feasibility, not just model output.

---
