# 📈 Yahoo Stock Price Prediction Pipeline

An end-to-end financial time-series pipeline engineered in R. This repository features advanced data preprocessing, feature engineering, and a machine learning model tournament to forecast out-of-sample asset returns.

🚀 **[(https://github.io)](https://yohanan408.github.io/yahoo-stock-pipeline/)**

---

## 🛠️ Project Architecture & Pipeline Flow
The project workflow handles financial data through sequential processing stages:
1. **Data Ingestion & Quality Control**: Chronological sorting, filtering out non-trading days, and outlier handling via rolling 20-day Z-scores.
2. **Feature Engineering**: Creating stationary data transformations via log returns, lag structures, rolling volatility indicators, and technical indicators (14-Day RSI).
3. **Exploratory Data Analysis (EDA)**: Diagnostic profiling covering returns skewness distribution, market panic clustering waves, and structural breaks via the PELT changepoint algorithm.
4. **Machine Learning Model Tournament**: Training and evaluation of multiple algorithms including Random Forest, Ridge Regression, and Support Vector Regression (SVR).

---

## 📊 Core Models Implemented
* **Random Forest Regressor**: Captures non-linear, multi-dimensional feature interactions across sequential lag intervals.
* **Support Vector Machine (SVR)**: Leverages a Radial Basis Function (RBF) kernel to isolate trend shifts across continuous feature maps.
* **Ridge Regression (Linear Regularization)**: Serves as a regularized linear benchmark to mitigate overparameterization across correlated technical tracking features.

---

## 💻 Tech Stack & R Packages Used
* **Data Wrangling & Core Architecture**: `tidyverse` (`dplyr`, `readr`, `stringr`, `ggplot2`)
* **Time Series & Window Modeling**: `zoo`, `TTR`
* **Statistical Modeling & Machine Learning Engine**: `randomForest`, `e1071`, `glmnet`, `changepoint`
