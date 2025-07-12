# Airbnb Data Mining Project Pricing Segmentation Analysis
A data mining-driven analysis of Airbnb listings to uncover pricing drivers and segment listings into Budget, Mid-Tier, and Luxury tiers

## ⚡ TL;DR (Project Summary)

- Explored 3,982 Airbnb listings to uncover pricing patterns using EDA, statistical analysis, and explainable ML.
- Identified **property type**, **season**, and **number of bedrooms** as top predictors of price.
- Built and interpreted models:
  - **Linear Regression** (R² = 0.379): Ideal for inference, pricing effect in $$.
  - **Decision Tree** (R² = 0.359): Captured nonlinear thresholds and rules.
- Used SHAP and PDP to visualize feature importance and interactions.
- Segmented listings into **Budget, Mid-Tier, and Luxury** tiers using **KMeans + PCA**.
- Delivered insights to support dynamic pricing, host strategy, and customer targeting.

---

## 📚 Table of Contents

1. [Project Overview](#-project-overview)  
2. [Business Objectives](#-business-objectives)  
3. [Data Summary & Preprocessing](#-data-summary--preprocessing)  
4. [Exploratory Data Analysis](#-exploratory-data-analysis)  
5. [Statistical Testing](#-statistical-testing)  
6. [Predictive Modeling](#-predictive-modeling)  
7. [Model Explainability](#-model-explainability)  
8. [Clustering & Segmentation](#-clustering--segmentation)  
9. [Key Takeaways](#-key-takeaways)  
10. [Next Steps](#-next-steps)  
11. [Author](#-author)

---

## 📌 Project Overview

This project analyzes the **pricing dynamics of Airbnb listings** in a major metropolitan area using statistical inference, explainable machine learning, and segmentation techniques. The goal is to support **consumer-tier segmentation** and **conditional pricing strategies** — not to build black-box models, but to derive actionable insights for stakeholders like hosts, travelers, and platform managers.

## 🎯 Business Objectives

- Identify **key factors** that drive nightly pricing for Airbnb listings.
- Analyze **interactions** (e.g., season × property type) using statistical tests and explainable ML.
- Segment listings into **Budget**, **Mid-Tier**, and **Luxury** tiers using clustering.
- Translate statistical significance into **practical recommendations** for pricing optimization.

## 📊 Data Summary & Preprocessing

- **Dataset**: `Airbnb_df.csv`, 3,982 listings, 15 features including:
  - Property attributes (type, bedrooms, amenities, etc.)
  - Host data (response time, experience)
  - Pricing info (nightly rate, cleaning fee)
- **Use Case**: Price optimization, customer segmentation, and actionable listing insights.

### 🔍 Cleaning & Feature Engineering

- Dropped `listing_id` (identifier) and log-transformed `nightly_rate` (target).
- **Skewed variables** (`years_as_host`, `cleaning_fee`) transformed or capped.
- Addressed **multicollinearity** (removed `guest_capacity` due to high VIF).
- Standardized numerical variables before modeling and clustering.

## 📈 Exploratory Data Analysis

### Univariate Insights

- Nightly rate and cleaning fee are **right-skewed**.
- Right-skewed distributions justify log transformation for modeling
  <p align="center">
  <img src="https://github.com/user-attachments/assets/8751fc42-977f-49ba-83e5-13975a862880" alt="Patient Selection Flowchart" width="500"/>
  <br>
  <em>Figure 1. Distribution of Nightly Rate and Cleaning Fee </em>
</p
- Most listings:
  - Are 1–2 bedroom apartments
  - Charge <$300 per night
  - Have positive reviews and location scores

### Bivariate/Multivariate Insights

- **Guest capacity × Season**: Higher capacity drives up price, especially in peak season.
- **Property type** matters: Luxury homes command significantly higher prices.
- **Review and location scores**: Have weaker individual impact but amplify with seasonal or spatial context.
- Detected **non-linear trends** — motivating the use of both linear and tree-based models.

## 🧪 Statistical Testing

Key questions and findings (ANOVA, interaction effects):

1. **Property type affects nightly rate** ✅  
2. **Guest capacity × Season interaction** ✅  
3. **Bedrooms × Property type interaction** ✅  
4. **Seasonality matters** ✅  
5. **Cleaning fee × Property type** ❌ Not statistically significant  

## 🔍 Predictive Modeling

### 🔹 Linear Regression (Inference-Focused)

- **Final model features**: `property_type`, `season`, `number_of_bedrooms`, `location_score`
- Achieved:
  - R² = 0.379 (test)
  - MAE ≈ $63
- Passed assumptions: homoscedasticity, normality, low multicollinearity

#### Key Coefficients (Interpreted in Dollars):

| Feature              | Impact    |
|----------------------|-----------|
| Standard Apartment   | -$61      |
| Tiny Studio          | -$80      |
| Peak Season          | +$89      |
| Shoulder Season      | +$34      |
| +1 SD in bedrooms    | +$24      |
| +1 SD in location score | +$8   |

### 🔹 Decision Tree (Interpretability-Focused)

- R² = 0.359, MAE ≈ $70
- Easy-to-follow pricing rules:
  - If `season = peak` and `bedrooms > 2.5`, price ≈ $278–$350
  - If `bedrooms ≤ 2.5` and off-season, price ≈ $117

## 🔍 Model Explainability

### PDP (Partial Dependence Plots)

- Bedrooms → Strong upward trend in price
- 2D PDP:
  - **Bedrooms × Location**: Amplifies pricing in premium areas
  - **Season × Bedrooms**: Bigger impact in peak travel times

### SHAP Values

- Confirmed: `number_of_bedrooms`, `season_peak`, and `location_score` are the most influential
- SHAP dependence plot:
  - Sharp increase in price when `location_score > 90`

## 📊 Clustering & Segmentation

### 🔹 Methodology

- Features: `nightly_rate`, `bedrooms`, `guest_capacity`, `amenities`, `location_score`, `review_score`
- Applied **KMeans** (k = 3 from Elbow Method) after standardization
- Visualized clusters in 2D using PCA

### 🔹 Segments

| Cluster | Description | Characteristics                                 |
|---------|-------------|--------------------------------------------------|
| 2       | **Luxury**  | High price, spacious, high amenities            |
| 1       | **Budget**  | Low scores, smaller size, lowest prices         |
| 0       | **Mid-Tier**| Balanced price, great reviews, fewer amenities  |

## 📌 Key Takeaways

- **Property type**, **season**, and **bedroom count** are the most reliable predictors of price.
- **Linear model** helps infer monetary impact; **decision tree** helps segment and apply pricing rules.
- Clustering offers **interpretable consumer segmentation**.
