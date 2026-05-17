# Graduate Admissions Prediction: Jamboree's Success Enabler

## Table of Contents
1.  [Project Overview](#project-overview)
2.  [Problem Statement](#problem-statement)
3.  [Data Description](#data-description)
4.  [Exploratory Data Analysis (EDA)](#exploratory-data-analysis-eda)
5.  [Data Preprocessing](#data-preprocessing)
6.  [Model Building & Evaluation](#model-building--evaluation)
    *   [Linear Regression](#linear-regression)
    *   [Ridge Regression](#ridge-regression)
    *   [Lasso Regression](#lasso-regression)
7.  [Linear Regression Assumptions Check](#linear-regression-assumptions-check)
8.  [Key Findings & Recommendations](#key-findings--recommendations)
9.  [Technologies Used](#technologies-used)

## Project Overview
This project aims to analyze and predict the 'Chance of Admit' for graduate school applicants, focusing on factors influencing admission from an Indian perspective. Leveraging a dataset containing various academic and extracurricular metrics, we build and evaluate several regression models to provide actionable insights for Jamboree, an educational consultancy.

## Problem Statement
Jamboree aims to understand what factors are important in graduate admissions and how these factors are interrelated. The project will also help predict an individual's chances of admission given their academic and profile variables, enabling Jamboree to guide students more effectively.

## Data Description
The dataset (`Jamboree_Admission.csv`) contains 500 entries across 9 features, including:
*   `Serial No.` (Dropped during preprocessing)
*   `GRE Score`
*   `TOEFL Score`
*   `University Rating`
*   `SOP` (Statement of Purpose strength)
*   `LOR` (Letter of Recommendation strength)
*   `CGPA` (Cumulative Grade Point Average)
*   `Research` (Binary: 0 for no, 1 for yes)
*   `Chance of Admit` (Target variable, ranging from 0 to 1)

All columns were found to be non-null, and no duplicate entries were present.

## Exploratory Data Analysis (EDA)

### Numerical Columns (GRE Score, TOEFL Score, CGPA, Chance of Admit)
*   **GRE Score**: Ranges from 290-340, with a concentration around 312-324.
*   **TOEFL Score**: Ranges from 90-120, peaking between 105-115.
*   **CGPA**: Ranges from 6-10, mostly between 8.0-9.0.
*   **Chance of Admit**: Ranges from 0-1.0, with a higher frequency between 0.70-0.75.

### Categorical Columns (University Rating, SOP, LOR, Research)
*   **University Rating**: Predominantly 2, 3, and 4.
*   **SOP & LOR**: Scores typically range from 2.5-4.0.
*   **Research**: A slight majority of students have research experience.

### Bivariate Analysis (Categorical vs. Chance of Admit)
*   Box plots show an **upward trend** for all categorical variables: higher University Ratings, SOP/LOR scores, and having Research experience correlate with a higher 'Chance of Admit'.

### Correlation Matrix (Numerical Features)
*   Strong positive correlations were observed between `CGPA`, `GRE Score`, `TOEFL Score`, and `Chance of Admit`. `CGPA` showed the strongest correlation with `Chance of Admit` (0.88).

## Data Preprocessing
*   **Missing Values**: No missing values were found.
*   **Duplicates**: No duplicate rows were identified.
*   **Column Renaming**: Trailing spaces were removed from 'LOR ' and 'Chance of Admit ' for consistency.
*   **'Serial No.'**: Dropped as it's an identifier and not a predictive feature.
*   **Outlier Treatment**: Box plots were used to visualize outliers, but no explicit capping or removal was performed, keeping the original data distribution intact for initial modeling.
*   **Feature Scaling**: `StandardScaler` was used on training data for OLS regression to ensure features contribute equally.

## Model Building & Evaluation
The data was split into an 80% training set and a 20% test set. Three regression models were trained and evaluated:

### Linear Regression
*   **Training Adjusted R²**: 0.8179
*   **Test Adjusted R²**: 0.8051
*   **Test MAE**: 0.0427
*   **Test RMSE**: 0.0609

### Ridge Regression
*   **Training Adjusted R²**: 0.8178
*   **Test Adjusted R²**: 0.8041
*   **Test MAE**: 0.0429
*   **Test RMSE**: 0.0610

Ridge regression showed very similar performance to Linear Regression, suggesting that multicollinearity was not a severe issue in the dataset, or the default alpha value was not strong enough to significantly alter coefficients compared to OLS.

### Lasso Regression
*   **Training Adjusted R²**: 0.7673
*   **Test Adjusted R²**: 0.7064
*   **Test MAE**: 0.0540
*   **Test RMSE**: 0.0747

Lasso regression resulted in slightly lower performance and led to some coefficients being shrunk towards zero (e.g., 'Research' coefficient became 0 at `alpha=0.01`), indicating its feature selection capability. For this dataset, with relatively few features and low multicollinearity, the standard Linear Regression or Ridge Regression performed better in terms of predictive accuracy.

## Linear Regression Assumptions Check

### Multicollinearity
*   **VIF Scores**: All VIF (Variance Inflation Factor) scores were below 5, indicating that multicollinearity is not a significant problem among the independent variables.

### Normality of Residuals
*   **Histogram**: The histogram of test residuals appeared somewhat normal and centered around zero, although with a slight left-skew.
*   **Q-Q Plot**: The Q-Q plot showed that the residuals largely fell along the standardized line, suggesting approximate normality. Some deviations at the tails were observed but the core distribution was normal.

### Homoscedasticity
*   **Residuals vs. Predicted Plot**: Visually, the scatter plot of residuals against predicted values suggested a **cone-like shape**, hinting at heteroscedasticity.
*   **Breusch-Pagan Test**: The test yielded a p-value of `0.0007`, which is less than 0.05, leading to the **rejection of the null hypothesis of homoscedasticity**. This formally indicated the presence of heteroscedasticity.
*   **Goldfeld-Quandt Test**: This test resulted in a p-value of `0.637`, which is greater than 0.05, leading to a **failure to reject the null hypothesis of homoscedasticity**. This test suggested no evidence of heteroscedasticity.

**Resolution**: Given the conflicting results and visual evidence, it was decided to use **robust standard errors** for the OLS model. This approach ensures valid statistical inference (p-values, confidence intervals) even in the presence of heteroscedasticity, without altering the model's coefficients.

## Key Findings & Recommendations

### Significance of Predictor Variables
*   **CGPA, GRE Score, TOEFL Score, and LOR** are highly significant positive predictors of a student's 'Chance of Admit'. Higher values in these metrics strongly increase admission chances.
*   **Research experience** also has a statistically significant positive impact.
*   **University Rating and SOP** did not show a statistically significant independent effect in the linear model after accounting for other factors. Their influence might be captured by other correlated variables or have non-linear relationships.

### Model Performance and Robustness
*   The Linear Regression model (including Ridge) achieved strong predictive performance with Adjusted R-squared values around **0.80-0.82** on both training and test data.
*   The use of **Robust Standard Errors** ensures the reliability of statistical inferences for the OLS model, addressing potential heteroscedasticity concerns.

### Recommendations for Model Improvement and Implementation
1.  **Explore Non-Linear Relationships**: Investigate non-linear patterns or interaction terms between variables, as the current linear model might not capture all complexities.
2.  **Feature Engineering**: Create new features, such as a combined academic score from GRE, TOEFL, and CGPA, or categorical encoding for other relevant factors.
3.  **Additional Data Sources**: Incorporate university-specific admission data, detailed applicant demographics (internships, publications, extracurriculars), or historical admission trends to enhance model accuracy and insight.
4.  **Model Implementation in Real World (for Jamboree)**:
    *   **Interactive Tool**: Develop an interactive web tool on Jamboree's website where students can input their details and receive an instant 'Chance of Admit' prediction, along with personalized feedback on areas for improvement.
    *   **Personalized Study Plans**: Utilize the model's insights to generate tailored recommendations, e.g., if GRE score is a strong factor, suggest focused GRE preparation.
    *   **Counselor Aid**: Empower Jamboree counselors with this data-driven model to provide more effective and precise guidance to students.

### Potential Business Benefits for Jamboree
*   **Increased Student Engagement**: A sophisticated prediction tool will attract more students to the platform.
*   **Enhanced Credibility**: Data-backed predictions will strengthen Jamboree's reputation as a reliable and effective admission consultancy.
*   **Improved Admission Rates**: More precise guidance will lead to higher success rates for students, translating to more success stories and increased business.
*   **Optimized Resource Allocation**: Understanding key predictive factors allows Jamboree to focus its resources on areas that yield the highest returns for students.
*   **Competitive Advantage**: A robust predictive model will differentiate Jamboree from competitors, reinforcing its leadership in educational consulting.

## Technologies Used
*   Python
*   Pandas (for data manipulation)
*   NumPy (for numerical operations)
*   Matplotlib (for plotting)
*   Seaborn (for statistical data visualization)
*   Scikit-learn (for Linear, Ridge, Lasso Regression, train-test split, and metrics)
*   Statsmodels (for OLS, VIF, Breusch-Pagan, Goldfeld-Quandt tests, and Q-Q plots)
