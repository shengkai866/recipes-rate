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


   | id   |  minutes| n_steps| n_ingredients|             cal|  average_rating| Ingredient Level|
|0 |333281|       40|      10|             9|   Normal Energy|             4.0|             less|
|1 |453467|       45|      12|            11| Too Much Energy|             5.0|           normal|
|2 |306168|       40|       6|             9|   Normal Energy|             5.0|             less|
|3 |286009|      120|       7|             7| Too Much Energy|             5.0|             less|
|4 |475785|       90|      17|            13|   Normal Energy|             5.0|           normal|
