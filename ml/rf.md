## Difference Between Classification and Regression

Classification:

- Predicting a label.
- For example, an email of text can be classified as belonging to one of two classes: “spam“ and “not spam“.
- Predictions can be evaluated using accuracy.

Regression:

- Predicting a quantity.
- For example, a house may be predicted to sell for a specific dollar value, perhaps in the range of 100000 to 200000.
- Predictions can be evaluated using root mean squared error.

## RandomForestClassifier

The default values for the parameters controlling the size of the trees (e.g. `max_depth`, `min_samples_leaf`, etc.) lead to fully grown and unpruned trees which can potentially be very large on some data sets.

#### n_estimators

default=100 - The number of trees in the forest.

Higher number of trees give you better performance but makes your code slower.

#### max_features

These are the maximum number of features Random Forest is allowed to try in individual tree:

1. *Auto/None* : This will simply take all the features which make sense in every tree.
2. *sqrt* : This option will take square root of the total number of features in individual run.
3. *0.2* : This option allows the random forest to take 20% of variables in individual run.

Increasing max_features generally improves the performance of the model as at each node now we have a higher number of options to be considered. However, this is not necessarily true as this decreases the diversity of individual tree which is the USP of random forest.

#### min_sample_leaf

If you have built a decision tree before, you can appreciate the importance of minimum sample leaf size. Leaf is the end node of a decision tree. A smaller leaf makes the model more prone to capturing noise in train data. Generally I prefer a minimum leaf size of more than 50.

#### random_state

This parameter makes a solution easy to replicate. A definite value of random_state will always produce same results if given with same parameters and training data.

#### oob_score

This is a random forest cross validation method. It is very similar to leave one out validation technique, however, this is so much faster. This method simply tags every observation used in different tress. And then it finds out a maximum vote score for every observation based on only trees which did not use this particular observation to train itself.

## RandomForestRegressor

