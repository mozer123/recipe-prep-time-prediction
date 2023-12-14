# Project Summary

Predicting recipe preparation times with our final model, incorporating both basic and nutritional features, remains a challenging task, as evidenced by modest improvements in accuracy metrics like Mean Squared Error and R-squared. The complexity of cooking processes and the variability in recipe execution are not fully captured by the data, highlighting the implicit difficulties in this predictive problem.

by Mert Ozer

Our exploratory data analysis on this dataset can be found here. https://mozer123.github.io/recipes-ratings/

This project is inspired by the Practice and Application of Data Science course DSC80 (https://dsc80.com/), specifically Project 5 – Model Building.


# Framing The Problem

## The Data

The dataset used in this research contains recipes and ratings from food.com. It was originally scraped and used by the authors of this (https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf) recommender systems paper. A subset of the raw data containing only the recipes and reviews posted since 2008 will be used in this research, since the original data is quite large. Specifically, the CSV file named “RAW_recipes.csv” which contains 83782 recipes is the subject of this research.

Even though the original data contains many more columns, for the purpose of this research only certain columns will be used. For a detailed exploratory data analysis see here (https://mozer123.github.io/recipes-ratings/).

## Introduction to the Prediction Problem

In this study, we address the prediction problem centered around recipes, specifically aiming to predict the cooking time of various recipes as listed by users of food.com. This problem is a **regression** task, as the goal is to forecast a **continuous variable**—cooking time—based on a set of explanatory features. The motivation behind choosing this topic lies in examining the accuracy with which users determine and report the cooking time of their recipes. By analyzing this aspect, we can gain insights into user behavior and recipe characteristics, which can further enhance the user experience of the platform.

The **response variable** in our model is **'minutes'**, representing the cooking time of a recipe. This variable was chosen due to its fundamental importance in the domain, as cooking time is a crucial factor that influences a user's decision to try a recipe. It also reflects the complexity and effort required for a recipe, making it a valuable piece of information for users. At the time of prediction, which occurs when a user completes and uploads a recipe, we have access to various attributes of the recipe, such as the number of steps ('n_steps') and the number of ingredients ('n_ingredients'). However, since the recipe is new and yet to be viewed or tried by others, it lacks ratings and reviews. Therefore, our model does not include user generated feedback metrics like ratings, focusing instead on the characteristics of the recipe itself.

For evaluating our model, we will use two key metrics: **Mean Squared Error (MSE)** and **R-squared (R²)**. MSE is a standard metric for regression tasks that measures the average of the squares of the errors, the differences between observed and predicted values. It is particularly useful as it penalizes larger errors more severely, thus providing a clear picture of model performance. On the other hand, R² offers an understanding of the proportion of variance in the dependent variable that can be explained by the independent variables in the model. We chose these metrics over others due to their interpretability and relevance in regression analysis. MSE provides a direct measure of the model's accuracy in predicting cooking time, while R² offers insight into the model's explanatory power, helping us understand how well our model captures the underlying dynamics of recipe preparation times.

## Data Cleaning and Preprocessing

In the data preparation phase of our study, we undertook several steps to ensure the quality and relevance of our dataset. These steps primarily focused on feature selection, outlier removal, and feature extraction, as detailed below: 

1. **Feature Selection:** Initially, we filtered our dataset to include only the most relevant columns for our prediction task. The selected features are 'minutes', 'nutrition', 'n_steps', and 'n_ingredients'.

2. **Outlier Removal:** A crucial step in our data cleaning process was the identification and removal of outliers. Outliers can significantly skew results and lead to inaccurate models. To effectively identify these outliers, we first graphed the distributions of our key variables: 'minutes', 'n_ingredients', and 'n_steps'. This visual inspection allowed us to identify reasonable cutoff values for each feature, beyond which data points were considered outliers and subsequently removed. This approach ensured that our dataset reflects typical recipes, enhancing the model's ability to generalize and accurately predict cooking times for a wide range of recipes.

3. **Processing the Nutrition Column:** The 'nutrition' column, initially in a string format that appeared as a list, required a specialized approach for extraction. We processed this column to separate its nutritional information into distinct features.

Here is the resulted DataFrame:

|   minutes |   n_steps |   n_ingredients |   calories |   total_fat |   sugar |   sodium |   protein |   saturated_fat |   carbohydrates |
|----------:|----------:|----------------:|-----------:|------------:|--------:|---------:|----------:|----------------:|----------------:|
|        40 |        10 |               9 |      138.4 |          10 |      50 |        3 |         3 |              19 |               6 |
|        45 |        12 |              11 |      595.1 |          46 |     211 |       22 |        13 |              51 |              26 |
|        40 |         6 |               9 |      194.8 |          20 |       6 |       32 |        22 |              36 |               3 |
|       120 |         7 |               7 |      878.3 |          63 |     326 |       13 |        20 |             123 |              39 |
|        90 |        17 |              13 |      267   |          30 |      12 |       12 |        29 |              48 |               2 |


# Baseline Model

## Model Type

The baseline model is chosen to be Random Forest Regressor, a learning method that operates by constructing multiple decision trees during training and outputting the average prediction of the individual trees. This method is well-suited for regression tasks as it is robust against overfitting and can effectively capture complex patterns in the data.

## Features Used

The features included in this model are 'n_steps' and 'n_ingredients'.

1. **'n_steps' (Quantitative):** This feature represents the number of steps required to prepare the recipe. It is a quantitative variable, reflecting the count of steps.

2. **'n_ingredients' (Quantitative):** This feature represents the number of ingredients used in the recipe. Similar to 'n_steps', it is a quantitative variable, reflecting the count of ingredients.

## Model Performance

**Performance Metrics:** The model's performance was evaluated using Mean Squared Error (MSE) and R-squared (R²) metrics.

**MSE: 821.033**

**R²: 0.189**

**Interpretation of Performance:** The MSE value indicates that the model has an average squared error of this magnitude when predicting the cooking time. While not exceedingly high, this value suggests that there is room for improvement in terms of prediction accuracy.

An R² value of 0.189 suggests that approximately 18.9% of the variance in the cooking time is explained by the model. This value, while indicating some level of predictive ability, significant portion of the variance in cooking time is not accounted for by the model.

## Assessment of the Model

The baseline model's moderate performance can be attributed to several factors. Primarily, the limited feature set, comprising only 'n_steps' and 'n_ingredients', may not capture the full complexity of cooking time determination. While these features are undoubtedly relevant, cooking time is influenced by a myriad of other factors not currently accounted for. This includes aspects like the nature of the ingredients, the type of preparation involved, and the cooking method, which could significantly impact the time required. Furthermore, the quantitative nature of the selected features, while straightforward for model processing, overlooks qualitative aspects that might offer deeper insights. For example, knowing whether the recipe was a dessert, a main dish or a drink and categorizing them might have helped us achieve better results.


# Final Model

## Added Features

In improving our model, we integrated additional nutritional features: "calories", "total_fat", "sugar", "sodium", "protein", "saturated_fat", and "carbohydrates" that were engineered in the data preprocessing step. These attributes were chosen based on the hypothesis that the nutritional composition of a recipe is linked to its preparation complexity and thus, the cooking time. For instance, recipes with higher protein content might involve cooking methods like baking or grilling that generally take longer. The inclusion of "calories" and various fat components (total, saturated) offers insights into the recipe's richness and possible preparation steps, like frying, which are time-influencing. Additionally, "sugar" and "sodium" levels can be indicative of certain types of food, such as a dessert or a main dish, that have specific time requirements. By incorporating these nutritional factors, we aimed to capture a more holistic view of each recipe, believing that these variables would improve the model's predictive power. 

## Improved Model

In this research, both for the baseline and the enhanced model, we employed the Random Forest Regressor as our primary modeling algorithm. This choice was reiterated from the baseline model section, where its selection was based on its capability to effectively capture complex, nonlinear relationships in the data.

## Hyperparameter Selection

When selecting values for the hyperparameter n_estimators, which represents the number of trees in the Random Forest, it's important to balance between computational efficiency which where we struggled the most and model accuracy. Here are three different values we've chosen for n_estimators and the reason behind each:

1. **50 Trees:** This is a relatively low number of trees. Choosing 50 as a starting point was beneficial for initial model training and quick iterations to pick the best performing features, especially when working with large datasets or when computational resources are limited. Fewer trees mean faster training, although it might not capture all the complexities of the data. This value is useful for establishing a baseline performance.

2. **100 Trees:** This is a mid-range value that should provide a good balance between training time and model performance. With 100 trees, the model is likely to capture more nuances in the data than with 50 trees, leading to improved accuracy and generalization without excessively increasing the computational cost. This value can be practical choice for more refined modeling after initial tests.

3. **150 Trees:** This is a relatively high number of trees, which can potentially lead to better model performance due to the increased decision trees. More trees can capture more patterns and reduce the risk of overfitting by averaging more decision paths. However, the trade-off is longer training times and higher computational demand. This value is suitable for final model tuning, especially when the initial results show promise and there's a need to squeeze out additional performance gains.

Overall, in the hyperparameter tuning, testing with 50, 100, and 150 trees revealed that 150 trees (n_estimators=150) yielded the best performance. This test was performed manually by fitting the model for all 3 cases seperately.

## Performance Improvement

The Final Model's performance slightly improved over the Baseline Model, as evidenced by the comparative analysis of their respective performances below:

**Baseline Model**

MSE: 821.033

R²: 0.189

**Final Model**

MSE: 798.356

R²: 0.212

The Baseline Model, which utilized a more limited set of features ('n_steps' and 'n_ingredients'), demonstrated a certain level of predictive ability but was constrained by the lack of data representation. In contrast, the Final Model incorporated a broader range of features, including nutritional values like 'calories', 'total_fat', 'sugar', 'sodium', 'protein', 'saturated_fat', and 'carbohydrates'. This inclusion of diverse and nutritionally relevant features allowed the Final Model to capture a more detailed picture of the recipes, leading to a more accurate prediction of cooking times.

# Fairness Analysis

The two groups selected for the fairness analysis are **“Reasonable Recipes”** and **“Complex Recipes”**

1. Choice of group “Reasonable Recipes” involves the following specifications.
	- Recipes with less than or equal to 180 minutes of cooking time.
	- Recipes with less than or equal to 25 ingredients.
	- Recipes with less than or equal to 40 steps.

2. Choice of group “Complex Recipes” involves the following specifications
	- Recipes with more than 180 minutes of cooking time.
	- Recipes with more than 25 ingredients.
	- Recipes with more than 40 steps.

Other details of the permutation test is as follows.

**The Evaluation Metric:** Mean Squared Error

**Null Hypothesis:** Our model is fair. Its Mean Squared Error for “Reasonable Recipes” and “Complex Recipes” as defined above are roughly the same, and any differences are due to random chance.

**Alternative Hypothesis:** Our model is unfair. Its Mean Squared Error for “Reasonable Recipes” does not equal to its Mean Squared Error for “Complex Recipes”.

**Test Statistic:** Absolute difference of Mean Squared Errors of the two groups

**Significance Level:** 5%

**P-value** = 0.00

In conclusion, p-value of 0 in the context of our permutation test strongly suggests that the difference in model performance between the two groups is statistically significant, thus we are likely to reject the null hypothesis. In practical terms, this result is not surprising as the model was specifically trained to perform well on reasonable recipes that do not have outlier feature values.
