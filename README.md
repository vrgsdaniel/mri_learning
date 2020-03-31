# machine_learning
# Schizophrenia
Repository for analysis of schizophrenia dataset

This projects needs python 3 and the dependencies specified in `environment.yml`.

To use the classical machine learning pipeline a configuration file needs to be created as a JSON file (details below).
To run the analysis execute: `python analysis/classical_ml/classical_ml.py cfg_file_path`.

The configuration file describes how the analysis will be run. It should be on a JSON format with the following structure:

| Attribute | Value | Remarks |
|-----------|-------|-----------|
| read_mode | String that specifies the type of data to be used   | Value should be one of the following: `BIDS`, `table`, `h5`      |
| paths     | JSON Object with specification of important paths   | The paths that can be described here are:<ul> <br> <li>`data`: path to BIDS folder </li> <li>`table`: path to table with data </li> <li>`h5:{training:path_tr, holdout:path_ho}`: paths to h5 files </li> <li>`atlas`: path to atlas for region based analysis </li>  </ul>|
| images    | JSON Object with the description for data loading and preprocessing | The possible keys (*default value*) are: <br> <ul> <li>`transform`: list with functions that will be applied to each image </li> <li>`target_transform_map (transform labels to a range of integers)` : name of an existing function </li> <li>`n_samples (null)`: Number of samples of the whole dataset to use, uses the entire dataset if null. </li> <li>`use_holdout (True)`: Flag that determines if the data should be split in training test sets. </li><li>`split_seed (0)` seed to do the train-test split</li><li>`test_size (null)`: Fraction of the dataset to use as holdout set. See `sklearn.model_selection.train_test_split`. </li> <li>`label ("label")`: Column associated to the data points label in a tabular </li> <li>`columns (null)`: Array with the list of columns to use when `read_mode` is `table` . If none, all columns but the label are used. </li> <li>`stratify_cols (["label"])` List of columns use to stratify the train test split.</li> <li> `mean_normalize (False)` Flag to standarize the data </li> <li> `minmax_normalize (False)` Flag for min-max normalization </li> <li>`mask_label (null)`: Name of a region to extract, ignored if null. </li> <li> `use_atlas (False)`: Flag to use an atlas as a dimensionality reduction. </li> <li> `atlas_strategy ('mean')`: Aggregation function if `use_atlas` is true. </li></ul>|
| training  | JSON Object with the description for data training | The possible keys (*default value*) are: <br> <ul> <li>`n_splits (3)`: Number of folds for cross validation </li> <li>`trials (4)`: Number of independent experiments to perform </li> <li>`scorers (['balanced_accuracy']) list of scoring metrics to evaluate the model` </li> <li>`retrain_metric (balanced_accuracy)` Metric from `scoring` to be used to retrain the model</li><li> List to specify the models to be used, where each element corresponds to one independent model to analyse. Each element should be a list of strings with the names of estimators to be chained. E.g. `[['PCA', 'SVC'], ['DummyClassifier']]` means that 2 models will be tested, first a pipeline that performs PCA followed by a support vector classifier, and the second model is just a dummy classifier</li><li> `parameters_lst`: Array of JSON objects with as many elements as models were specified. The key-values for ach element should correspond to the name of a hyperparameter mapped to a list of the values to be tried. E.g. `[{"PCA__n_components":[5, 20], {"SVC__C:[1, 10]"}, {"DummyClassifier__strategy":["most_frequent"]}]` </li>|
| holdout   | JSON Object with the description for inference in holdout set (optional) |The possible keys (*default value*) are: <br> <ul><li>`scorers (['balanced_accuracy']) list of scoring metrics to evaluate the model` </li> <li>`best_metric (balanced_accuracy)` Metric from `training.scorers` to be used to select a model</li><li>`model_selection (all_models)`: strategy to select a model. See model_selection</li><li>`plotting (True)`: Flag to determine if the results should be saved as a graph</li><li>`voting`: Groups multiple selected models into an ensemble through a voting system. Works when `model_selection` is `all_models`</li><li>`chosen_estimator`: See `model_selection`</li><li>`chosen_trial`: See `model_selection`</li></ul>|
|           |`model_selection`| It can have one of the following values: <ul><li>`all_models`: Reports mean and variance across trials for all estimators</li><li>`best_model`: Selects best overall model</li><li>`best_model_type`: Selects the estimator that performed best in average according to `best_metric` and retrieves the models for every trial</li><li>`best_model_type_random`: as `best_model_type` but selects a random model from all the trials</li><li>`random_model`: A random model is selected</li><li>Selects a model from a random trial for all estimators</li><li>`specific_model`: Selects the models from `chosen_estimator` for the trial `chosen_trial`</li></ul>|
