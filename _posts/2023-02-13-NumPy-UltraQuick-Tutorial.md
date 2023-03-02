---
title: NumPy UltraQuick Tutorial
date: 2023-02-13 01:40:00 -0500
categories: [Resources, machine learning]
tags: [machine learning, data, numpy, python, tensorflow]
published: false
---

Using the `tf.keras` module of tensorflow for requires at least a little understanding of `NumPy`. So just going over their tutorial for quick reference.

## Using Numpy

We start with importing NumPy

```python
import numpy as np
```

### Populating arrays with specific numbers

We populate arrays with specific numbers the following is an 8 element array

```python
one_dimensional_array = np.array([1.2, 2.4, 3.5, 4.7, 6.1, 7.2, 8.3, 9.5])
print(one_dimensional_array)
```

### Two-Dimensional Arrays

np.array to create two-dimensional array. We use and extra later of square brackets. example 3x2 array

```python
two_dimensional_array = np.array([[6, 5], [11, 7], [4, 8]])
print(two_dimensional_array)
```

We can also populate an array with all zeroes, call `np.zeros`. To populate an array with all ones, call `np.ones`.

### Number Sequences 

Populate arrays with sequences of numbers with np.arange

```python
sequence_of_integers = np.arange(5, 12)
print(sequence_of_integers)
```

### Random arrays

Populate arrays with random numbers with np.random.randint The following call populates a 6-element array with random integers between 50 and 100. 

```python
random_integers_between_50_and_100 = np.random.randint(low=50, high=101, size=(6))
print(random_integers_between_50_and_100)
```

To create random floating-point values between 0.0 and 1.0, call np.random.random. For example:

```python
random_floats_between_0_and_1 = np.random.random([6])
print(random_floats_between_0_and_1) 
```

### Mathematical Operations on numpy Operands

If we want to add or subtract two arrays linear algebra needs two operands to have the same dimensions. use broadcasting to add 2.0 to the value of every item in the array of the previous cell.

```python
random_floats_between_2_and_3 = random_floats_between_0_and_1 + 2.0
print(random_floats_between_2_and_3)
```

The follow operation uses broadcasting to multiply each cell by 3

```python
random_integers_between_150_and_300 = random_integers_between_50_and_100 * 3
print(random_integers_between_150_and_300)
```

## Task 1: Create Linear Dataset

Your goal is to create a simple dataset consisting of a single feature and a label as follows:

1. Assign a sequence of integers from 6 to 20 (inclusive) to a NumPy array named feature.
2. Assign 15 values to a NumPy array named label such that:

```python
label = (3)(feature) + 4
```

### Code

```python
#task 1 problemset
print('task 1 problemset')
feature = np.arange(6, 21)
print(feature)
label = (3 * feature) + 4
print(label)
```

## Task 2: Add Some Noise to the Dataset

To make your dataset a little more realistic, insert a little random noise into each element of the label array you already created. To be more precise, modify each value assigned to label by adding a different random floating-point value between -2 and +2.

Don't rely on broadcasting. Instead, create a noise array having the same dimension as label.

### Code

```python
#task 2 problemset
noise = np.random.random([15] * 4) - 2
print(noise)
label = label + noise
print(label)
```
