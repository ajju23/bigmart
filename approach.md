# BigMart Sales Prediction: Approach Documentation & Business Insights

## 1. Business Problem Understanding

BigMart, a large retail chain, faces the classic challenge of predicting sales for thousands of products across diverse outlets. Accurate sales forecasting is essential for:

- **Inventory Management:** Overstock and understock are costly; precise predictions enable optimal stock levels.
- **Supply Chain Optimization:** Understanding demand patterns helps streamline logistics and reduce lead times.
- **Pricing & Promotion Strategies:** Insights into what drives sales at product and outlet level empower targeted offers.
- **Outlet Expansion & Planning:** Predictive analytics guide decisions on new outlets or reformatting existing ones.

**Business Objective:**  
Predict the sales of each product at BigMart outlets, using historical data, to improve operational efficiency and maximize revenue.

## 2. Data Exploration & Cleaning

### Data Overview:
- **Train:** 8523 rows, 12 columns (includes sales target)
- **Test:** 5681 rows, 11 columns (excludes target)

### Key Features:
- **Product Attributes:** Item_Identifier, Item_Weight, Item_Fat_Content, Item_Visibility, Item_Type, Item_MRP
- **Outlet Attributes:** Outlet_Identifier, Outlet_Establishment_Year, Outlet_Size, Outlet_Location_Type, Outlet_Type

### Cleaning Steps:
- **Categorical Consistency:** Standardized Item_Fat_Content (e.g., 'low fat', 'LF', 'reg' → 'Low Fat', 'Regular')
- **Missing Values:**
  - **Item_Weight:** Imputed by Item_Identifier median, then overall median.
  - **Outlet_Size:** Imputed by Outlet_Type mode.
- **Outlier Correction:** Item_Visibility zeros replaced by Item_Type median; capped at 99th percentile.
- **Data Types:** Ensured proper types for modeling.

## 3. Exploratory Data Analysis (EDA)

- **Sales Distribution:** Highly skewed; few products/outlets contribute most sales.
- **Categorical Analysis:** Outlets, Item_Type, and Outlet_Type have strong influence on sales.
- **Numerical Features:** Outliers and distribution shapes visualized via boxplots.
- **Relationship Exploration:** Boxplots of sales vs categorical features reveal key drivers (e.g., Outlet_Type, Item_Fat_Content).

## 4. Advanced Feature Engineering

### Business Motivation:
Feature engineering is the process of creating new inputs to maximize signal for the prediction task. In retail, this means translating raw data to business-relevant indicators.

#### Key Features Created:

1. **Item_Category:**  
   - Extracted from the first two letters of Item_Identifier (`FD`, `DR`, `NC`).
   - Used to classify as Food, Drink, Non-Consumable—critical for inventory & demand patterns.

2. **Is_Food, Is_Drink, Is_NonConsumable:**  
   - Binary flags for each major product type.
   - Business rationale: Food products typically have higher turnover; non-consumables may exhibit different seasonality.

3. **Outlet_Years:**  
   - Calculated as "years since establishment".
   - Older outlets often have established customer bases; newer outlets may still be ramping up.

4. **Item_Visibility_MeanRatio:**  
   - Ratio of item's visibility to its mean visibility.
   - Business logic: In-store placement affects sales; this feature normalizes visibility per item.

5. **Price_per_Weight:**  
   - Item_MRP divided by Item_Weight.
   - Indicates value perception for shoppers (e.g., higher price per unit weight may deter sales).

6. **Is_Supermarket:**  
   - Flag for outlet type (Supermarket vs Grocery).
   - Supermarkets generally have higher footfall and larger baskets.

7. **MRP_bin:**  
   - Binned Item_MRP into price ranges.
   - Shoppers respond differently to products in distinct price bands.

8. **Item_Type_Freq:**  
   - Frequency encoding of Item_Type (how often an item type appears).
   - Popular product types may drive bulk sales.

#### Encoding & Scaling:
- **Label Encoding:** For ordinal/categorical columns (e.g., Outlet_Size, Location_Type).
- **One-Hot Encoding:** For top 10 Item_Type values; avoids high cardinality.
- **Standard Scaling:** Normalizes numerical features for model stability.

## 5. Feature Selection

**Why?**  
Not all features contribute equally; some may introduce noise or collinearity. Feature selection improves model generalization.

**Method:**  
- Used Recursive Feature Elimination with Cross-Validation (RFECV) and RandomForestRegressor.
- Selected optimal subset of features based on RMSE.

## 6. Model Building & Selection

- **Algorithms Used:**  
  - RandomForestRegressor
  - GradientBoostingRegressor
  - XGBoost, LightGBM, CatBoost (for advanced ensemble methods)
  - StackingRegressor for blending top models

- **Hyperparameter Tuning:**  
  Exhaustive grid search to optimize parameters (trees, learning rate, etc.)

- **Evaluation:**  
  RMSE on cross-validation folds; focus on reducing overfitting.

## 7. Final Prediction & Submission

- **Prediction:**  
  Used the best-performing stacked/blended models to generate sales predictions on test data.
- **Submission File:**  
  Created as per competition requirements.

## 8. Conclusion & Business Insights

**Key Takeaways:**
- **Deep Feature Engineering:**  
  Translating domain knowledge into engineered features (e.g., outlet age, price bands, product categories) provided substantial lift in model performance.
- **Robust Data Cleaning:**  
  Addressed missing values and outliers with business logic (e.g., impute outlet size by type, visibility by product).
- **Modeling Strategy:**  
  Ensemble and stacking approaches mitigate weaknesses of individual models and capture complex interactions.

**Business Value Delivered:**
- **Improved Forecast Accuracy:**  
  Directly supports inventory and supply chain management.
- **Actionable Insights:**  
  Outlet- and product-level predictions help target interventions (promotions, re-stocking).
- **Scalable Framework:**  
  Approach can be adapted for new outlets, product lines, or time periods.

---

### **How Feature Engineering Mapped to Business Understanding**

- **Outlet_Years:** Captures maturity; older outlets may have loyal customers.
- **Item_Category & Flags:** Food vs Drink vs Non-Consumable products have inherently different sales cycles.
- **Price_per_Weight & MRP_bin:** Models consumer price sensitivity and purchasing behavior.
- **Item_Visibility_MeanRatio:** Represents real-world shelf placement and its effect on sales.
- **Is_Supermarket:** Differentiates outlet business models—supermarkets often outperform groceries.

### **Final Thoughts**

A thoughtful, business-driven feature engineering approach, coupled with robust preprocessing and advanced modeling, provides a powerful framework for sales prediction. This methodology not only maximizes predictive accuracy but also generates interpretable insights for retail decision makers.
