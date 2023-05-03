# amazon_recommender_project
Project for recommending systems class at UAM

Author: Adam Szpilkowski
Index: s464868

*This readme contains only general information about how project was made and what methods were used, for more in-detail information, look inside source code. Everything important is commented.*

# Data preparation

Data preparation went as instructed in project_1_data_preparation.ipynb and data_processing/data_preprocessing_toolkit.py

# Recommender and evaluation

## User features - methods used
### 1. Probability distribution

## Item features
### One-hot encoding
For item features, I used pandas get_dummies method for creating one-hot encoding for each item existing in interactions_df dataframe.

## Recommender functions:

### fit():
To make fit method as fast as possible I tried both numpy and pandas library. With numpy I could not make it work, but with pandas I managed to make it work fast by using one trick.
This trick consisted of creating two variables for how many entries should be created inside of pandas dataframe containing all negative interactions:
- n_to_generate_cross - this variable was equal to (n_neg_per_pos value + 1) * lenght of interactions_df. Why n+1? Because in worst case we would generate n * len_of_int_df negative interactions and 1 * len_of_int_df positive interactions already existing inside interactions_df. By using this trick, we make sure that later by removing possibly all of interactions_df entries from generated ones we will get at least n +/- some negative interactions.
- n_to_generate - this variable containst actual number of entries to pick from earlier generation of cartesian product.

### recommend():
This function is more straight-forward, no special tricks were used like in fit() function.
For each user I generated cartesian product of user and items and made sure that if user_id is not existing inside self.users_df, I filled his values by mean values of all users.
Items were prepared by prepare_items_df() function.
Scores were calculated by using model.predict() function, and then used to create column inside dataframe containing all the scores assigned to items.
Then dataframe for each user was sorted descending, so I could used .head() function to get n_recommendations items.

## Tuning:

### 1. LinearRegressionCBUIRecommender
Best parametes after tuning:
- n_neg_per_pos = 4
which gave these results:
| Recommender                     	| HR@1     	| HR@3    	| HR@5     	| HR@10    	| NDCG@1   	| NDCG@3   	| NDCG@5   	| NDCG@10  	|
|---------------------------------	|----------	|---------	|----------	|----------	|----------	|----------	|----------	|----------	|
| LinearRegressionCBUIRecommender 	| 0.041073 	| 0.09131 	| 0.145282 	| 0.230143 	| 0.041073 	| 0.070236 	| 0.091918 	| 0.119625 	|

### 2. SVRCBUIRecommender
Did not tune this recommender

### 3. RandomForestCBUIRecommender
Did not tune this recommender

### 4. XGBoostCBUIRecommender
Sadly, because of lack of time I did not tuned or tested this recommender.

## Final evaluation:
| Recommender                     	| Parameters       	| HR@1     	| HR@3     	| HR@5     	| HR@10    	| NDCG@1   	| NDCG@3   	| NDCG@5   	| NDCG@10  	|
|---------------------------------	|------------------	|----------	|----------	|----------	|----------	|----------	|----------	|----------	|----------	|
| LinearRegressionCBUIRecommender 	| n_neg_per_pos: 4 	| 0.041073 	| 0.09131  	| 0.145282 	| 0.230143 	| 0.041073 	| 0.070236 	| 0.091918 	| 0.119625 	|
| SVRCBUIRecommender              	| base             	| 0.000679 	| 0.004073 	| 0.006789 	| 0.012559 	| 0.000679 	| 0.002732 	| 0.003886 	| 0.005748 	|
| RandomForestCBUIRecommender     	| base             	| 0.04243  	| 0.07943  	| 0.12797  	| 0.211134 	| 0.04243  	| 0.063997 	| 0.084203 	| 0.111491 	|
| XGBoostCBUIRecommender          	|                  	|          	|          	|          	|          	|          	|          	|          	|          	|
| AmazonRecommender               	| base             	| 0.03666  	| 0.09776  	| 0.138493 	| 0.208079 	| 0.03666  	| 0.071565 	| 0.088349 	| 0.110865 	|