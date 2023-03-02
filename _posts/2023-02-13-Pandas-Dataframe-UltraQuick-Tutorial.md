---
title: Pandas DataFrame UltraQuick Tutorial
date: 2023-02-13 01:40:00 -0500
categories: [Resources, machine learning]
tags: [machine learning, data, numpy, python, tensorflow, dataframe, pandas]
published: false
---

[Dataframes](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) are the central data structure in the pandas API. This is a very quick introduction into the parts of DataFrames required to the other colab exercises in the google machine learning crash course.

A dataframe is similar to in-memory spreadsheet. Like a spreadsheet:

- A dataframe stores data in cells.
- A dataframe has named colums (usually) and numbered rows

## Importing NumPy and pandas modules and usage

```python
import numpy as np
import pandas as pd
```

### Creating a DataFrame

The following code cell creates a simple DataFrame containing 10 cells organized as follows:

- 5 rows
- 2 columns, one named temperature and the other named activity

The following code cell instantiates a pd.DataFrame class to generate a DataFrame. The class takes two arguments:

- The first argument provides the data to populate the 10 cells. The code cell calls np.array to generate the 5x2 NumPy array.
- The second argument identifies the names of the two columns.

```python
# Create and populate a 5x2 NumPy array.
my_data = np.array([[0, 3], [10, 7], [20, 9], [30, 14], [40, 15]])

# Create a Python list that holds the names of the two columns.
my_column_names = ['temperature', 'activity']

# Create a DataFrame.
my_dataframe = pd.DataFrame(data=my_data, columns=my_column_names)

# Print the entire DataFrame
print(my_dataframe)
```

### Specifying a subset of a DataFrame

Pandas provides multiple ways to isolate specific rows, columns, slices, or cells in a DataFrame

```python
print("Rows #0, #1, and #2:")
print(my_dataframe.head(3), '\n')

print("Row #2:")
print(my_dataframe.iloc[[2]], '\n')

print("Rows #1, #2, and #3:")
print(my_dataframe[1:4], '\n')

print("Column 'temperature':")
print(my_dataframe['temperature'])
```

## Task 1: Create a DataFrame

Do the following:

1. Create an 3x4 (3 rows x 4 columns) pandas DataFrame in which the columns are named Eleanor, Chidi, Tahani, and Jason. Populate each of the 12 cells in the DataFrame with a random integer between 0 and 100, inclusive.
2. Output the following:
    - the entire DataFrame
    - the value in the cell of row #1 of the Eleanor column
3. Create a fifth column named Janet, which is populated with the row-by-row sums of Tahani and Jason.

```python
task1_data = np.random.randint(low=0, high=101, size=(4,4))
task1_column_names = ['Eleanor', 'Chidi', 'Tahani', 'Jason']
task1_dataframe = pd.DataFrame(data=task1_data, columns=task1_column_names)
print('Print entire dataframe:')
print(task1_dataframe)
print('Print value of row 1 of eleanor column')
print(task1_dataframe['Eleanor'].iloc[[1]], '\n')
print('Crteate column of Janet, which is populated with jason and Tahani')
task1_dataframe['Janet'] = task1_dataframe['Tahani'] + task1_dataframe['Jason']
print(task1_dataframe)
```

## Copying a DataFrame (optional)

Pandas provides two different ways to duplicate a DataFrame:

* **Referencing.** If you assign a DataFrame to a new variable, any change to the DataFrame or to the new variable will be reflected in the other. 
* **Copying.** If you call the `pd.DataFrame.copy` method, you create a true independent copy.  Changes to the original DataFrame or to the copy will not be reflected in the other. 

The difference is subtle, but important.

```python
# Create a reference by assigning my_dataframe to a new variable.
print("Experiment with a reference:")
reference_to_task1_dataframe = task1_dataframe

# Print the starting value of a particular cell.
print("  Starting value of task1_dataframe: %d" % task1_dataframe['Jason'][1])
print("  Starting value of reference_to_task1_dataframe: %d\n" % reference_to_task1_dataframe['Jason'][1])

# Modify a cell in task1_dataframe.
task1_dataframe.at[1, 'Jason'] = task1_dataframe['Jason'][1] + 5
print("  Updated task1_dataframe: %d" % task1_dataframe['Jason'][1])
print("  Updated reference_to_task1_dataframe: %d\n\n" % reference_to_task1_dataframe['Jason'][1])

# Create a true copy of my_dataframe
print("Experiment with a true copy:")
copy_of_my_dataframe = my_dataframe.copy()

# Print the starting value of a particular cell.
print("  Starting value of my_dataframe: %d" % my_dataframe['activity'][1])
print("  Starting value of copy_of_my_dataframe: %d\n" % copy_of_my_dataframe['activity'][1])

# Modify a cell in task1_dataframe.
my_dataframe.at[1, 'activity'] = my_dataframe['activity'][1] + 3
print("  Updated my_dataframe: %d" % my_dataframe['activity'][1])
print("  copy_of_my_dataframe does not get updated: %d" % copy_of_my_dataframe['activity'][1])
```

