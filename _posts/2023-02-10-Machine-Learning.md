---
title: Machine Learning
date: 2023-02-10 01:40:00 -0500
categories: [Resources, machine learning]
tags: [machine learning, data]
published: false
---

These are my notes from [Google's Introduyctino to Machine Learning Problem Framing](https://developers.google.com/machine-learning/problem-framing).

## Overview

Problem framning is the process of understanding if a machine learning approch is a good solutions for the problem before going to work and training a model.

## Understand the Problem

To understand the problem, we need to perform the following tasks:

1. State the goal for the product you are developing or refactoring.
2. Determine whether the goal is best solved using ML.
3. Verify you have the data required to train a model.

For example you could have a Weather app that needs to calculate precipitation in six-hour increments for a geographic region. 

### Clear use Case for ML

So to find a clear case use if we should use ML instead of a heuristic approach we can do this. Solve the problem manually using the heuristic approch. If you are using this type of approach and already are achieving high success 99%+ then there is no need to use a ML approach. We use heuristic appraches as benchmarks to determine if there is a usecase for ML. 

If you think the ML model will have a much higher quality solutions then it would be best to implement, but if it is marginal or not as high quality it would be wasteful to implement.

The cost and maintenance also come into question. 

Can the ML solution justify the increase in cost? Note that small improvements in large systems can easily justify the cost and maintenance of implementing an ML solution.

How much maintenance will the solution require? In many cases, ML implementations need dedicated long-term maintenance.

Does your product have the resources to support training or hiring people with ML expertise?

#### Checking Understanding

Is it important to have a non-ML solution or huristic in place before analyzing the ML solution because the non-ML solutions is the benchnmark that we can use to measure the ML solution against.

### Data

We need good data in order to make a good model. Data should have these characteristics, or If you can't get the data we need in the required format, our model will make poor predictions.

- **Abundant** - The more relevant and useful examples in your dataset, the better your model will be.
- **Consistent and reliable** - Having data that's consistently and reliably collected will produce a better model. For example, an ML-based weather model will benefit from data gathered over many years from the same reliable instruments.
- **Trusted** - Understand where your data will come from. Will the data be from trusted sources you control, like logs from your product, or will it be from sources you don't have much insight into, like the output from another ML system?
- **Available** - Make sure all inputs are available at prediction time in the correct format. If it will be difficult to obtain certain feature values at prediction time, omit those features from your datasets.
- **Correct** - In large datasets, it's inevitable that some labels will have incorrect values, but if more than a small percentage of labels are incorrect, the model will produce poor predictions.
- **Representative** - The datasets should be as representative of the real world as possible. In other words, the datasets should accurately reflect the events, user behaviors, and/or the phenomena of the real world being modeled. Training on unrepresentative datasets can cause poor performance when the model is asked to make real-world predictions.

#### Predictice Power

For a model to make good predictions, the features in your dataset should have predictive power. The more correlated a feature is with a label, the more likely it is to predict it.

Some features will have more predictive power than others. For example, in a weather dataset, features such as `cloud_coverage`, `temperature`, and `dew_point` would be better predictors of rain than `moon_phase` or `day_of_week`. 

#### Checking Understanding 

When analyzing datasets, the three key attrivutes we should look for are:

- Contains Correct Values.
- Small enough to load onto a local machine.
- Representative of the real world.

### Predictions vs. Actions

There no use in predicting something if we cant turn it into an action that helps users. 

A model that predicts whether a user will find a video useful should feed into an app that recommends useful videos. A model that predicts whether it will rain should feed into a weather app.

#### Checking Understanding

An engineering team at a large organization is responsible for managing incoming phone calls.

The goal: To inform callers how long they'll wait on hold given the current call volume.

They don't have any solution in place, but they think a heuristic would be to divide the number of employees answering phones by the current number of customers on hold, and then multiply by 10 minutes. However, they know that some customers have their issues resolved in two minutes, while others can take up to 45 minutes or longer.

Their heuristic probably won't get them a precise enough number. They can create a dataset with the following columns: number_of_callcenter_phones, user_issue, time_to_resolve, call_time, time_on_hold.

**Use ML.** The engineering team has a clearly defined goal. Their heuristic won't be good enough for their use case. The dataset appears to have predictive features for the label, time_on_hold. 

## Framing an ML Problem

After verifying that your problem is best solved using ML and that you have access to the data you'll need, you're ready to frame your problem in ML terms. You frame a problem in ML terms by completing the following tasks:

- Define the ideal outcome and the model's goal.
- Identify the model's output.
- Define success metrics.

### Define the ideal outcome and the model's goal

