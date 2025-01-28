### EqualityInsight-Project

# Research Hypothesis
Countries with higher female graduation rates in science, technology, engineering, and mathematics (STEM) fields will have lower wage inequality.

## Final Goal
The increase in the number of female STEM graduates can contribute to higher employment rates, women's economic independence, and ultimately foster economic growth while reducing inequality.  
(Aligned with **SDG Goal 5 (Gender Equality)** and **SDG Goal 8 (Decent Work and Economic Growth)**.)

---

## 1. Machine Learning Tasks

- **Regression**: Quantitatively analyze the impact of STEM graduation rates on wage gaps.
- **Classification**: Predict countries with low wage inequality and address data imbalance issues.
  - Apply oversampling techniques such as SMOTE to mitigate data imbalance and improve classification model performance.
  - Compare the performance of models such as `XGBRegressor`, `Random Forest`, and `Logistic Regression` using:
    - **Regression Metrics**: R², RMSE
    - **Classification Metrics**: Accuracy, Precision, Recall, F1-score

---

## 2. Data Definition

### (1) **Gender Wage Gap by Occupation (%)** - ILOstat (2000-2023)
- Provides gender wage gap by occupation, country, and year.
- Variables:
  - Country Name
  - Gender Wage Gap by Occupation (%)
  - Year

### (2) **Distribution of Tertiary Graduates by Field of Study** - UIS.Stat (2017-2023)
- Contains data on the distribution of tertiary graduates by field of study and gender.
- Includes the proportion of STEM graduates by gender.
- Variables:
  - Country Name
  - Graduation Rate by Field of Study and Gender
  - Year

### (3) 
- Merge the two datasets by **country** and **year**.
- Handle missing values and select key variables:
  - Include STEM graduation rates and wage inequality metrics as critical features for regression models.
- Feature engineering:
  - Add economic and social factors to improve model performance.
 
---

## 3. Data Processing Workflow

### (1) **Gender Wage Gap by Occupation (%) - ILOstat (2000-2023)**

- **Handling Rows with Flags**:
  - Rows with the `"Magnitude nil or negligible"` flag: Set values to `0`  
    > This flag indicates negligible or nearly non-existent values, so explicitly setting them to `0` ensures they do not affect the analysis.
  - Rows with the `"Category not applicable"` flag: Retain as-is (NaN)  
    > This flag indicates the data is not applicable to certain categories, so keeping them as NaN allows filtering during later analysis.
  - Rows with the `"UIS Estimation"` flag: Use **time-based interpolation** for missing values  
    > Since this flag indicates estimated data, time-based interpolation helps maintain data consistency and reliability over time.
  - Rows without any flags: Apply **forward-fill and backward-fill** for missing values  
    > Missing data without flags is likely incomplete, so standard fill methods help minimize missing data impact.

- **Filtering Specific Indicators**:
  - Retain only indicators relevant to the analysis to prevent unnecessary data processing and interference.

- **Removing Unnecessary Columns**:
  - Drop columns not directly relevant to the analysis to simplify the dataset.

---

### (2) **Distribution of Tertiary Graduates by Field of Study - UIS.Stat (2017-2023)**

- **Filtering Data**:
  - Retain only rows where `classif1.label` is `"Occupation (Skill level): Total"`.
  - Use data from **2010 onward** to focus on recent trends, as older data may have lower relevance.

- **Converting obs_value to Numeric**:
  - Convert `obs_value` to numeric for calculations.
  - Non-numeric values are converted to `NaN` and handled as missing data.

- **Calculating Group Statistics**:
  - Compute the **mean** and **standard deviation** for each `source.label` group to enable normalization later.

- **Merging Statistics**:
  - Combine the computed statistics (mean, standard deviation) with the original dataset for group-level normalization.

- **Standardizing Values**:
  - Normalize values to improve comparability between groups and adjust for distribution differences.
  - This ensures fairness in model training and interpretation.

---

### (3) **Data Merging and Preprocessing**

- **Merging Datasets**:
  - Combine data by matching on **country** and **year** to create a unified dataset for analysis.

- **Column Standardization and Outlier Removal**:
  - Ensure uniform column naming conventions for merging.
  - Remove outliers from `wage_gap` and `grad_rate` based on the 1st and 99th percentiles to improve model stability.

- **Categorical Variable Encoding**:
  - Encode `source.label` as a categorical variable using one-hot encoding for use in machine learning models.

- **Defining Features and Target**:
  - Independent variables (`X`): Include `grad_rate` and one-hot encoded `source.label` variables.
  - Dependent variable (`y`): Use `wage_gap` as the target.

- **Splitting Dataset**:
  - Split data into training and testing sets to evaluate the model’s generalization performance.

