# Predicting the rate of the recipes

by Kai Sheng (shengkai@umich.edu)

***Note***: This is a homework for EECS 398 at U-M in the repository description


## Introduction
Our goal is to predict the ratings of recipes. Our training data comes from the website food.com, where recipe ratings provide a relatively fair measure for evaluating the quality of recipes. These ratings help customers easily choose suitable recipes to try when cooking. Therefore, we aim to predict recipe ratings based on some of their basic information.

In this prediction process, we combined the recipes data table with the user comments and ratings data table to create a unified dataset for predicting recipe ratings. The resulting dataset contains a total of 83782 rows. We decided to base our predictions on the columns "id," "minutes," "n_steps," "n_ingredients," "col" and "average rating."

***id***: Each recipes have a unique id.

***minutes***: "minutes" represents the time required to complete the recipe, measured in minutes.

***n_steps***: "n_steps" represents the number of steps required to complete the recipe.

***n_ingredients***: "n_ingredients" represents the number of ingredients involved in the recipe.

***col***: "col" represents the calorie level of the recipe. We classify the calorie levels as follows:

            "Less Energy": Calories less than or equal to 100.

            "Normal Energy": Calories between 101 and 300 (inclusive).

            "Upper Energy": Calories between 301 and 500 (inclusive).

            "Too Much Energy": Calories greater than 500.

***Ingredient Level***: "Ingredient Level" represents the Number of Ingredients level of the recipe. We classify the calorie levels as follows:

            "less": #ingredients is less than 9 (exclusive)

            "normal": #ingredients is between 9 and 26(exclusive)

            "more": #ingredients is more than 26

***average rating*** : "average rating" represents the average rate of each recipes based on the rate on the users'comment and rating.


## Data Cleaning and Exploratory Data Analysis

### Data Cleaning
The first step in our data cleaning process is converting ratings of 0 to NaN. This adjustment is necessary because users typically cannot give a rating of 0; they must rate recipes between 1 and 5. When a user submits a comment without providing a rating, the system automatically assigns a rating of 0 to the recipe for that user. To address this issue, we replace ratings of 0 with NaN in our dataset.

The next step is to convert the calories of each recipe into a calorie level. According to the definition provided in the Introduction, the calorie levels are categorized into four groups: "Less Energy," "Normal Energy," "Upper Energy," and "Too Much Energy."

Since our data table contains multiple entries for each recipe due to the merge process, the next step is to retain only one entry per recipe and drop the duplicate columns.

The following data cleaning step is to create a new column called "Ingredient Level," based on the "n_ingredients" column. The definition of "Ingredient Level" was provided in the Introduction.

Finally, we filter the data table to retain only the following columns: "minutes," "n_steps," "n_ingredients," "cal," "Ingredient Level," and "average_rating."

```py
print(combination.head(5))
```


|  | id   |  minutes| n_steps| n_ingredients|             cal|  average_rating| Ingredient Level|
|:-|:-----|:--------|:-------|:-------------|:---------------|:---------------|:----------------|
|0 |333281|       40|      10|             9|   Normal Energy|             4.0|             less|
|1 |453467|       45|      12|            11| Too Much Energy|             5.0|           normal|
|2 |306168|       40|       6|             9|   Normal Energy|             5.0|             less|
|3 |286009|      120|       7|             7| Too Much Energy|             5.0|             less|
|4 |475785|       90|      17|            13|   Normal Energy|             5.0|           normal|


### Univariate Analysis
The following graph shows the distribution of the average ratings. As the graph illustrates, the distribution is right-skewed, meaning that most recipes have an average rating between 4.5 and 5.0. Understanding the distribution of the average rating provides valuable insight into the general trend, helping us predict the average rating of unseen data.

<iframe src="plot/The_distribution_of_Average_Rating.html" width=800 height=600 frameBorder=0></iframe>

The following graph shows the distribution of the number of ingredients. It reveals that the majority of recipes have between 5 and 15 ingredients. This distribution also provides insight into the importance of the number of ingredients, suggesting its potential relevance in recipe ratings or other aspects of the data.

<iframe src="plot/Number_ingredients.html" width=800 height=600 frameBorder=0></iframe>


### Bivariate Analysis
The following graph shows the scatter plot of the number of steps versus the average rating. As the graph indicates, most points fall within the range of 1 to 40 steps.
However, there is a noticeable trend: as the number of steps increases, the occurrence of lower average ratings decreases. 
This trend provides us with valuable insight for predicting the average rating of each recipe.

<iframe src="plot/Number_of_Steps_VS_Average_Rate.html" width=800 height=600 frameBorder=0></iframe>

The following graph shows boxplots for the four different calorie level categories. The boxplots indicate that the maximum average rating for all categories is 5. 
However, the lower quantiles for "Normal Energy" and "Less Energy" appear slightly higher than those for "Too Much Energy" and "Upper Energy."
This suggests that calorie level may influence the average rating of a recipe.

<iframe src="plot/Boxplot_for_Colarie_Level_vs_Average_Rate.html" width=800 height=600 frameBorder=0></iframe>


### Interesting Aggregates
```py
print(combination.pivot_table(index = "Ingredient Level", columns = "cal",values='average_rating',aggfunc="mean"))
```


|             cal| Less Energy| Normal Energy| too much Energy| upper Energy|
|:---------------|:-----------|:-------------|:---------------|:------------|
|Ingredient Level|            |              |                |             |
|            less|    4.647942|      4.633041|        4.617930|     4.609297|
|            more|    5.000000|      4.858974|        4.764676|     4.864881|
|          normal|    4.633259|      4.610637|        4.626318|     4.629658|



As shown in the table above, it presents the combined values for "Ingredient Level" and "Calorie Level." 
This table is crucial in providing insights for predicting the average rating of each recipe. According to the table, 
recipes with more ingredients tend to have higher average ratings compared to those with fewer ingredients.
From the calorie level perspective, the table suggests that "Less Energy"
recipes generally have slightly higher average ratings than those in other calorie categories.
Those conclusion from the table above provides us with direction to predict average rate.


### Imputation
Firstly, the features we explore, such as "minutes," "n_steps," "n_ingredients," and "cal," do not contain any missing values. 
The "Ingredient Level" column is created based on the information from "n_ingredients." Although the "average_rate" column does contain missing values due to the conversion of
 0 ratings to NaN, these missing values occur because no user rated those recipes. Since these recipes do not provide meaningful information, 
we will drop all missing values for future predictions. As a result, the distribution remains unchanged before and after imputation.

<iframe src="plot/The_distribution_of_Average_Rating.html" width=800 height=600 frameBorder=0></iframe>


## Problem Identification
Our goal is to predict the average rating for each recipe, which is a regression problem.
We chose to predict the average rating because it is generally a fair value for evaluating recipes from the public's perspective.
If we can accurately predict the average rating for each recipe, it will make it easier for users to select recipes to try.
The features we used to predict the average rating are "cal," "minutes," "n_steps," and "n_ingredients."
"Minutes" and "n_steps" represent the time required to complete the recipe, making them reasonable variables for predicting the average rating. 
"n_ingredients" and "cal" represent, to some extent, the healthiness and taste of the recipe, which also makes them relevant variables for prediction. 
Since our model is a regression model, we will use Mean Squared Error (MSE) to evaluate its performance.



## Baseline model
For our baseline model, we first split the entire dataset into four subsets: X_train, X_test, y_train, and y_test. The X_train 
and X_test datasets include the columns "minutes," "n_steps," "n_ingredients," and "cal," while the y_train and y_test datasets contain the corresponding 
"average_rating" column.

In the baseline model, we chose to use "cal" and "n_ingredients" as features to predict the average rating. "n_ingredients" is a quantitative variable, 
while "cal" is an ordinal categorical variable.

To prepare the "cal" column for regression, we applied OneHotEncoding to transform the categorical values into a format suitable for use in regression prediction. 
Next, we used GridSearchCV to identify the best hyperparameters for the model. Specifically, we defined a PolynomialFeatures transformer for "n_ingredients" and set the 
degree of the polynomial feature transformation to range from 1 to 10. GridSearchCV was then used to determine the optimal degree for "n_ingredients." Finally, we use Linear regression
to combine "n_ingredients" and "cal" to predict the average rate together.

The Mean Squared Error (MSE) for our baseline model is ***0.4239693***, indicating that the model's performance is relatively suboptimal. This is likely because the baseline model only considers features related to taste and health to predict the average rating. 
In our final model, we will address this limitation and work to improve the model's performance.




 ## Final Model
In our final model, we decided to include the features ***"minutes"*** and ***"n_steps"*** in the prediction process. 
The "minutes" feature represents the time users need to complete the recipe, while "n_steps" reflects the number of steps, 
which correlates to both the time required and the complexity of the recipe. We believe that the time spent on a recipe and its level of sophistication can influence 
how users rate the recipe. 
Therefore, incorporating these two features into our final model aims to improve its predictive accuracy.

"minutes" and "n_steps" are both quantitative variable.

The transformation process for "n_ingredients" and "cal" remains the same as described in the Baseline Model section. For the "minutes" and "n_steps" variables, 
we also decided to use PolynomialFeatures as a transformer to process these two columns. 
Consequently, we will use GridSearchCV to identify the best hyperparameters that fit "n_ingredients," "n_steps," and "minutes" for our final model.

Instead of using linear regression to combine those columns together, we choose to use Ridge to Ridge Regression. Since Ridge Regression is good at dealing the m+ulticollinearity. We believe
"n_steps" and "minutes" may have some special relationship, "n_steps" may represents both how long need to take to finish this recieps and the complexity, and "minutes" represents the time.
Therefore, we believe to choose Ridge Regression is a good decision to prevent potential multicollinearity.

Instead of using ***linear regression*** to combine these features, we opted for ***Ridge Regression***. Ridge Regression is effective in addressing ***multicollinearity***, 
which can arise when predictor variables are highly correlated. We believe that "n_steps" and "minutes" may share a special relationship, 
as "n_steps" reflects both the time required to complete a recipe and its complexity, while "minutes" specifically represents the total time. 
Given this potential ***multicollinearity***, we consider Ridge Regression a suitable choice to mitigate the effects of multicollinearity and enhance the model's performance.

Finally, our final model use the 5 as polynomial degree for "n_steps," 2 as polynomial degree for "n_ingredients," and 1 for "minutes." The ridge alpha is 512. The final model's MSE 
(mean squared error) is ***0.4239125***. Therefore is still 0.0000514 improvement compare to the Baseline model, although it is just a little improvement. It still shows that the time and 
complexity of a recipe is still influence the rate of recipes.
