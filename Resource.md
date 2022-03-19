


# Data Cleaning

## Full Credit for the below https://miamioh.instructure.com/courses/38817/pages/data-cleaning 


In this section, we use the open data SFMTA Bikeway Network (Links to an external site.) at San Francisco Data (Links to an external site.). The data include the network of bike routes, lanes, and paths around the city of San Francisco. Maintained by the SFMTA. Since this data is subject to change, we downloaded it on June 28th, 2016. The data set we use can be downloaded here and the information about the data can be found here. 

First, we use the library pandas to open and read the data. If you didn't enable the object inspector to display the documentation of a function when the function is called and would like to have this feature, go to Basic Syntax to see how to do it. 

```python
import pandas as pd
import numpy as np
bikedata = pd.read_csv("C:\\Users\\your_username\\Desktop \\SFMTA_BIKEWAY_NETWORK.csv")
```

Note: If you check the type of bikedata, you should find it is a pandas dataframe. 

 

## This section covers

* Understand the data, 
* Check Missing Values, 
* Get information without missing values
* Fill in Missing Values, 
* Drop Data, 
* More Subsets (random samples are included)
 

## Understand the data

Before cleaning data, there are a couple of things we would like to know, for example, the dimension of a dataset, the data type of each variable, the first few rows and the last few rows of the data and name of each variable, etc. In this dataset, the identifier variable is CNN, San Francisco's street centerline network unique ID. 

```python
bikedata.shape  # check out the dimension of the dataset
bikedata.dtypes  # look at the data types for each column
bikedata.head()  # read the first five rows
bikedata.tail()  # read the last five rows
bikedata.columns.values  # return an array of column names
bikedata.columns.values.tolist()  # return a list of column names
```


## Check Missing Values

Next, we would like to check if there are any missing values. To check this, we can use the function dataframe.isnull() in pandas. It will return True for missing components and False for non-missing cells. However, when the dimension of a dataset is large, it could be difficult to figure out the existence of missing values. In general, we may just want to know if there are any missing values first. The function dataframe.isnull().values.any() returns True when there is at least one missing value occurring in the data. The function dataframe.isnull().sum().sum() returns the number of missing values in the data set. 

```python
bikedata.isnull()  # checking missing values
bikedata.notnull()  # checking non-missing values
bikedata.isnull().values.any()  # only want to know if there are any missing values
bikedata.notnull().sum()  # knowling number of non-missing values for each variable
bikedata.isnull().sum().sum()  # knowing how many missing values in the data
bikedata["CNN"].isnull().values.any()  # only want to know if there are any missing values in CNN
bikedata["TO_ST"].isnull().values.any()
bikedata["TO_ST"].isnull().sum()  # return the number of missing values in TO_ST
```

## Get information without missing values

A simple way to deal with data containing missing values is to skip rows with missing values in the dataset. 

```python
bikedata[bikedata["TO_ST"].notnull()]  # the data contain rows that no missing values in TO_ST
bikedata[bikedata["FROM_ST"].notnull() & bikedata["TO_ST"].notnull()]
# the data contain rows that no missing values in FROM_ST and TO_ST
no_missing = bikedata.dropna()  # drop missing values and assign the data to no_missing
clean_missing_rows = bikedata.dropna(how="all")  # drop rows where all cells in the row in NA and assign the data to clean_missing_rows
bikedata.dropna(axis=1, how="all")  # drop columns if they only contain missing values
bikedata.dropna(thresh=25)  # drop rows that contain less than 25 non-missing values
```


Note: If we use dataframe.dropna(thresh=25) to drop rows that contain less than 25 non-missing values, we don't change the original data. We can assign the output to a new variable or save the changes to the original data right away by using dataframe.dropna(thresh=25, inplace=True). In our example, it should be bikedata.dropna(thresh=25, inplace=True).

 

Sometimes, we may want to find the location (index) where missing values occur. Here is an example. We would like to know the index when either From_ST or TO_ST is missing. The where() function in the module numpy gives the LOCATIONS (all of them in a list) of where our statement is true.

```python
missinglist = list(bikedata["FROM_ST"].isnull() | bikedata["TO_ST"].isnull())
index_missing = np.where(missinglist)  # return an array of size 1
```

Note: If you print out index_missing, you should find that it is an array of size 1. To access elements in this array, use index_missing[0][k] for the (k+1)th component. For example, index_missing[0][2] returns 1217. 

 

## Fill in Missing Values

For quantitative variables, we may replace missing values with sample mean, mode, median or other numbers. For category variables, we can create a new category for missing values by replace missing values with a string. 

```python
Fill_no = bikedata.fillna(1000000)  # fill in missing with 1000000 and save the data to Fill_no
Fill_str=bikedata["TO_ST"].fillna("missing")  # fill in missing with a string: "missing" and save the data to Fill_str
bikedata["SHAPE_LEN"].fillna(bikedata["SHAPE_LEN"].mean(), inplace=True)  # fill missing values with the sample mean and save the changes to the original data
Drop Data
```

We may want to drop duplicate rows, if any. 

```python
bikedata.drop_duplicates(inplace=True)  # remove duplicates, if any, and save the changes to the original data
subset1 = bikedata.drop(bikedata.index[[1,7,9]])  # drop the 2nd, 8th, and 10th rows
subset2 = bikedata.drop(bikedata.index[range(1,11)])  # drop all rows between 2nd to 10th rows
subset3 = bikedata.drop(["LAST_EDITE", "LAST_EDI_1"], axis=1)  # drop LAST_EDITE and LAST_EDI_1
```

## More Subsets

```python
subset4 = bikedata.iloc[:100, :]  # a subset of the first 100 rows of the original data
subset5 = bikedata.iloc[:, :3]  # a subset of the first 3 columns of the original data
subset6 = bikedata.iloc[:100, :15]  # a subset of the first 100 rows and the first 15 columns
subset7 = bikedata[["CNN", "FROM_ST", "TO_ST", "SHAPE_LENG"]]  # a subset contains variables CNN, FROM_ST, TO_ST, and SHAPE_LENG
subset8 = bikedata.sample(n=1000)  # a random sample of size 1000 without replacement (replace = False (Default))
subset9 = bikedata.sample(frac=0.1, replace=True)  # a random sample of 10% of the original data with replacement
```