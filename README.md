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
2. [Feature Summary](#feature-summary)
3. [Business Objectives](#-business-objectives)  
4. [Data Summary & Preprocessing](#-data-summary--preprocessing)  
5. [Exploratory Data Analysis](#-exploratory-data-analysis)  
6. [Statistical Testing](#statistical-testing)   
7. [Predictive Modeling](#-predictive-modeling)  
8. [Model Explainability](#model-explainability) 
9. [Clustering & Segmentation](#-clustering--segmentation)  
10. [Key Takeaways](#-key-takeaways)  

---

## 📌 Project Overview

This project analyzes the **pricing dynamics of Airbnb listings** in a major metropolitan area using statistical inference, explainable machine learning, and segmentation techniques. The goal is to support **consumer-tier segmentation** and **conditional pricing strategies** — not to build black-box models, but to derive actionable insights for stakeholders like hosts, travelers, and platform managers.

## Feature Summary 

| Feature               | Description                                                          |
| --------------------- | -------------------------------------------------------------------- |
| `property_type`       | Type of listing (e.g., Luxury Home, Standard Apartment, Tiny Studio) |
| `season`              | Booking season (Off, Shoulder, Peak)                                 |
| `number_of_bedrooms`  | Total number of bedrooms in the listing                              |
| `guest_capacity`      | Maximum number of guests the listing can accommodate                 |
| `amenities_count`     | Number of amenities offered in the listing                           |
| `minimum_stay_nights` | Minimum number of nights required to book                            |
| `host_response_time`  | Typical response speed of the host (e.g., within an hour, same day)  |
| `years_as_host`       | Duration (in years) the host has been active                         |
| `cancellation_policy` | Type of cancellation policy (e.g., Flexible, Moderate)               |
| `review_score`        | Average guest review rating (1–5 scale)                              |
| `location_score`      | Neighborhood desirability (scaled score)                             |
| `cleaning_fee`        | One-time cleaning fee charged to guests                              |
| `nightly_rate`        | Price per night (target variable — log-transformed for modeling)     |


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
  <img src="https://github.com/user-attachments/assets/8751fc42-977f-49ba-83e5-13975a862880" alt="Patient Selection Flowchart" width="600"/>
  <br>
  <em>Figure 1. Distribution of Nightly Rate and Cleaning Fee </em>
</p  

- Most listings:
  - Are 1–2 bedroom apartments
  - Charge <$300 per night
  - Have positive reviews and location scores

### Bivariate/Multivariate Insights

- **Guest capacity × Season**: Higher capacity drives up price, especially in peak season.
<p align="center">
  <img src="https://github.com/user-attachments/assets/b15b2780-ca74-40be-b8e6-150e21e3454d" width="500"/>
  <br>
  <em>Figure 2. Scatterplot of Guest Capacity x Season on Nightly Rate </em>
</p 

- **Property type** matters: Luxury homes command significantly higher prices.
  <p align="center">
  <img src="https://github.com/user-attachments/assets/cffe0fcc-d221-4f6e-b608-2039e38fe084" width="500"/>
  <br>
  <em>Figure 3. Grouped Bar Chart — Nightly Rate by Season & Property Type </em>
</p 
  
- **Review and location scores**: Have weaker individual impact but amplify with seasonal or spatial context.
- Detected **non-linear trends** — motivating the use of both linear and tree-based models.
-  Figure 2 shows multicollinearity between `bedrooms`, `guest_capacity`, and `cleaning_fee`.
  <p align="center">
  <img src="https://github.com/user-attachments/assets/38b1e553-357d-4004-bf56-e911c56cd2eb" alt="Patient Selection Flowchart" width="500"/>
  <br>
  <em>Figure 4. Correlation matrix of numerical features </em>
</p  

## Statistical Testing

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
  <p align="center">
  <img src="https://github.com/user-attachments/assets/f61a8215-f510-41d4-9fd0-1830ca138305" alt="Patient Selection Flowchart" width="1200"/>
  <br>
  <em>Figure 5. Decision Tree </em>
</p  

## 🔍 Model Explainability

### PDP (Partial Dependence Plots)

- Bedrooms → Strong upward trend in price
- 2D PDP:
  - **Bedrooms × Location**: Amplifies pricing in premium areas
  - **Season × Bedrooms**: Bigger impact in peak travel times
<p align="center">
<img src="https://github.com/user-attachments/assets/9c19f6a8-87fa-4830-a985-fd4a59d3982b" alt="Patient Selection Flowchart" width="700"/>
<br>
<em>Figure 6. 2D Partial Dependence Plots (PDPs) — Interaction Effects in Random Forest Model </em>
</p  

### SHAP Values
  - SHAP summary plot:
<p align="center">
<img src="https://github.com/user-attachments/assets/05d4d530-2d21-44a8-b9b3-3c307a23107a" alt="Patient Selection Flowchart" width="600"/>
<br>
<em>Figure 7. SHAP Summary Plot </em>
</p  

- `number_of_bedrooms`, `season_peak`, and `location_score` are the most influential
  - SHAP dependence plot:
<p align="center">
<img src="https://github.com/user-attachments/assets/2d586b6b-7437-4f5a-9b9e-3fcf2f8171c2" alt="Patient Selection Flowchart" width="600"/>
<br>
<em>Figure 8. SHAP Dependance Plot </em>
</p  

  - Sharp increase in price when `location_score > 90`

## 📊 Clustering & Segmentation

### 🔹 Methodology

- Features: `nightly_rate`, `bedrooms`, `guest_capacity`, `amenities`, `location_score`, `review_score`
- Applied **KMeans** (k = 3 from Elbow Method) after standardization
- Visualized clusters in 2D using PCA
<p align="center">
<img src="https://github.com/user-attachments/assets/488a340e-c65c-49c0-aa36-ef4fa3f09700" alt="Patient Selection Flowchart" width="600"/>
<br>
<em>Figure 9. PCA Plot </em>

### 🔹 Segments
<p align="center">
<img src="https://github.com/user-attachments/assets/dce1e944-13a5-440c-9c74-961e255356c5" alt="Patient Selection Flowchart" width="600"/>
<br>
<em>Figure 10. KDE Plots of Different Clusters </em>

- Analyzed the 3 clusters using KDE plots

| Cluster | Description | Characteristics                                 |
|---------|-------------|--------------------------------------------------|
| 2       | **Luxury**  | High price, spacious, high amenities            |
| 1       | **Budget**  | Low scores, smaller size, lowest prices         |
| 0       | **Mid-Tier**| Balanced price, great reviews, fewer amenities  |

## 📌 Key Takeaways

- **Property type**, **season**, and **bedroom count** are the most reliable predictors of price.
- **Linear model** helps infer monetary impact; **decision tree** helps segment and apply pricing rules.
- Clustering offers **interpretable consumer segmentation**.
