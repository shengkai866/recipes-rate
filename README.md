# Predicting the rate of the recipes

by Kai Sheng (shengkai@umich.edu)

***Note***: This is a homework for EECS 398 at U-M in the repository description


## Introduction
Our goal is to predict the ratings of recipes. Our training data comes from the website food.com, where recipe ratings provide a relatively fair measure for evaluating the quality of recipes. These ratings help customers easily choose suitable recipes to try when cooking. Therefore, we aim to predict recipe ratings based on some of their basic information.

In this prediction process, we combined the recipes data table with the user comments and ratings data table to create a unified dataset for predicting recipe ratings. The resulting dataset contains a total of 234,429 rows. We decided to base our predictions on the columns "minutes," "n_steps," "n_ingredients," and "col."

***minutes***: "minutes" represents the time required to complete the recipe, measured in minutes.

***n_steps***: "n_steps" represents the number of steps required to complete the recipe.

***n_ingredients***: "n_ingredients" represents the number of ingredients involved in the recipe.

***col***: "col" represents the calorie level of the recipe. We classify the calorie levels as follows:

            "Less Energy": Calories less than or equal to 100.

            "Normal Energy": Calories between 101 and 300 (inclusive).

            "Upper Energy": Calories between 301 and 500 (inclusive).

            "Too Much Energy": Calories greater than 500.
