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
| calories_category | Very Low | Low   | Medium | High  | Very High |
|--------------------|----------|-------|--------|-------|-----------|
| minutes_category   |          |       |        |       |           |
| Very Quick         | 4.74     | 4.75  | 4.72   | 4.68  | 4.66      |
| Quick              | 4.68     | 4.67  | 4.71   | 4.68  | 4.68      |
| Medium             | 4.61     | 4.65  | 4.68   | 4.68  | 4.67      |
| Slow               | 4.68     | 4.68  | 4.67   | 4.69  | 4.67      |
| Very Slow          | 4.68     | 4.66  | 4.64   | 4.61  | 4.63      |


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
