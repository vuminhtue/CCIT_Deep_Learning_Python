---
title: "Data Partition with Scikit-Learn"
teaching: 20
exercises: 0
questions:
- "What is Data Partition"
objectives:
- "Learn how to split data using sklearn"
keypoints:
- "sklearn, data partition"
---

# Data partition: training and testing
- In Machine Learning, it is mandatory to have training and testing set. Some time a verification set is also recommended.
Here are some functions for spliting training/testing set in `sklearn`:

- `createDataPartition`: create series of test/training partitions
- `createResample` creates one or more bootstrap samples.
- `createFolds` splits the data into k groups
- `createTimeSlices` creates cross-validation split for series data. 
- `groupKFold` splits the data based on a grouping factor.

Due to time constraint, we only focus on `createDataPartition` and `createFolds`
## Scikit-Learn data
The `sklearn.datasets` package embeds some small toy [datasets](https://scikit-learn.org/stable/datasets.html):
In this example we gonna use the renowned iris flower data
```python
from sklearn.datasets import load_iris
iris = load_iris()
iris_df = pd.DataFrame(data=iris.data,columns=iris.feature_names)
features = iris['feature_names']
iris_df['target'] = iris.target
iris_df["target_name"] = iris_df['target'].map({i:name for i,name in enumerate(iris.target_names)})
iris_df.sample(5)
```

## Data spliting using `train_test_split`: **Single fold**
Here we use `train_test_split` to randomly split 60% data for training and the rest for testing:
![image](https://user-images.githubusercontent.com/43855029/114209883-22b81700-992d-11eb-83a4-c4ab1538a1e5.png)

```python
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(iris_df.loc[:,features],
                                                    iris_df['target_name'],
                                                    train_size=0.6,random_state=21)
#random_state: int {0-42}, similar to R set_seed function
```

## Data spliting using `K-fold`
The procedure has a single parameter called k that refers to the number of groups that a given data sample is to be split into. As such, the procedure is often called k-fold cross-validation. When a specific value for k is chosen, it may be used in place of k in the reference to the model, such as k=10 becoming 10-fold cross-validation.
![image](https://user-images.githubusercontent.com/43855029/114211785-103edd00-992f-11eb-89d0-bbd7bd0c0178.png)
```python
kf10 = KFold(n_splits=10,shuffle=True,random_state=20)
for train_index, test_index in kf10.split(iris_df):
    X_train = iris_df.iloc[train_index].loc[:, features]
    X_test = iris_df.iloc[test_index][features]
    y_train = iris_df.iloc[train_index].loc[:,'target']
    y_test = iris_df.loc[test_index]['target']
    
    # Do not run this part as we have not defined a model. This will be introduced in the next part.
    model.fit(X_train, y_train) #Training the model
    y_pred = model.predict(X_test)
    print(f"Accuracy for the fold no. {i} on the test set: {accuracy_score(y_test, y_pred)}")
```
