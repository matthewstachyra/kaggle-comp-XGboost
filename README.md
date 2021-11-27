# kaggle-comp-XGboost
- Kaggle competition to maximize AUC for binary classification 
- Dataset was data from wearable in clinical trial
- Final AUC was 0.74
- Team achieved 4th place
- More detail below.

We tested 3 types of models in arriving at our final model with a score of 0.74338 on the kaggle leaderboard. The first model was an XGBoost model with simple pre-processing (more details below). The second model was testing different stacked models where we combined various classifiers, using the same pre-processing as in the XGBoost model. The third and final model was again just an XGBoost model (no ensemble/stacking). However, for this third model we pre-processed the input set differently using PCA and k-means clustering to identify the most important features and to add additional features to a transformed input set (more details below).

Below we organize our final summary around the 3 major approaches/models we were working with. Additional notes and reasoning to the below is included at key inflection points in the pdf of our submitted notebook (i.e., the below is a high level summary).

Model 1: XGBoost 

Data was preprocessed as follows. Numeric features were scaled using a minmax scaler. Categorical variables were unpacked using get_dummies(). Features with only one unique value were removed. Categorical variables were then one hot encoded. We did see that the data was unbalanced with a roughly 18/82 split. Therefore in all folds we stratify by the y.

We created a default XGBoost model to have a baseline. This had an accuracy of 86% and returned a kaggle leaderboard score of 0.65321.

We then tuned this model using grid search. We tested various parameters including the major ones: n_estimators, max_depth, and few others. We ran 3 rounds of gridsearch, tuning the parameters based on what we observed. For example, the higher range of n_estimaors and max_depth were chosen in the first run, so we increased the range of possible values in subsequent runs. The best hyperparameters at this stage were: {'colsample_bytree': 0.5, 'eval_metric': 'logloss', 'learning_rate': 0.1, 'max_depth': 25, 'min_child_weight': 0.5, 'n_estimators': 150, 'objective': 'reg:logistic', 'scale_pos_weight': 0.33, 'sub_sample': 0.5}.

The submitted model returned a kaggle leaderboard score of 0.66521. A minor couple percentage point increase so we continued to model 2.

Model 2: Stacked model

The input data was the same as for model 1. Here for the stacked model we started with an ensemble of 5 models -- the same 5 as presented in section(random forest, extra trees, adaboost, gradient boost, support vector machine). The performance of this model compared to the tuned XGBoost model was subpar. It had lower accuracy and a lower AUC. (Note: as stated above, additional details are within comments and discussions sections at corresponding points in the pdf of the notebook). The performance of the stacked model was lower despite optimizing the threshold for a positive classification. To obtain the optimized threshold we used Youden’s J statistic. One thing to note is that the recall for the 0 classification was very low (as it had been for the XGBoost model).

We continued by adding 5 more classifiers from sklearn to build a new stacked model of 10. These additional 5 were knn, logistic regression, naive bayes, multilayer perceptron, and linear discriminant analysis. Performance across key parameters increased. We observed a higher accuracy and higher AUC relative to the tuned XGBoost model. The difference was…
The Kaggle leaderboard score for this model was lower than the tuned XGBoost model with 0.64981.


(Final) Model 3: XGBoost with feature tuning

The input data was different for this third and final model. Some of the pre-processing was the same: one hot encoding the categorical features, removing features with just one unique value. The key difference was we didn’t work with all the features and added we some new features. To get there we first ran an extra tree classifier to identify feature importances. We took the top 100 most important numeric features. The new feature set included this subset of 100. 

Next, to create new features, we applied k-means clustering with 5 clusters to get the cluster features, applied k means clustering on x, y, and z features and got the x, y, and z clusters as new features, applied PCA on all features to get 3 new features for the PC1, PC2, PC3, and also applied PCA to the x, y, and z features. Finally we combined the categorical features.

XGBoost responded very well to the new data as described above. With just a little bit of hyperparameter tuning using grid search we were able to achieve higher accuracy, specificity, sensitivity, and AUC compared to the other 2 models. Submitted to kaggle we achieved 4th place (at the time of this writing) with a score of 0.74338.

