# My-project
---
title: "My Project"
author: "FN"
date: "March 26, 2025"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Introduction

The goal of your project is to predict the manner in which they did the exercise. This is the "classe" variable in the training set. You may use any of the other variables to predict with. You should create a report describing how you built your model, how you used cross validation, what you think the expected out of sample error is, and why you made the choices you did. You will also use your prediction model to predict 20 different test cases.

## Model built
The expacted outcome variable is classe, a 5 level of factor variable. 
In this dataset, participants were asked to perform one set of 10 repetitions of the Unilateral Dumbbell Biceps Curl in 5 different fashion: Class A - excatly according to the specification, Class B - throwing the elbows to the front, Class c- lifting the dumbbell only halfway, Class D - lowering the dumbbell only halfway, Class E - throwing the hips to the front. 

Class A correspons to the specified execution of the exercise, while the other 4 classes correpond to common mistakes. Decision tree will be used to create the model. After the model have been developed. Cross-validation will be performed. Two set of data will be created, original training data set (75%) and subtesting data set (25%). 


## Load library
```{r library, echo=FALSE}

library(caret)
library(knitr)
library(randomForest)
library(rpart)
library(rpart.plot)

```

## Download and loading the Dataset
```{r load data}

# Download the dataset 
trainUrl <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
testUrl <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"


# Load the dataset into memory
trainingData <- read.csv(url(trainUrl), na.strings = c("NA", "#DIV/0!", ""))
testingData <- read.csv(url(testUrl), na.strings = c("NA", "#DIV/0!", ""))

#
trainingData <- trainingData[, colSums(is.na(trainingData)) == 0]
testingData <- testingData[, colSums(is.na(testingData)) == 0]

# Delete variables that are not related 
trainingData <- trainingData[, -c(1:7)]
testingData <- testingData[, -c(1:7)]

# partioning the training set into two different dataset

traningPartitionData <- createDataPartition(trainingData$classe,  p = 0.7, list = F)
trainingDataSet <- trainingData[traningPartitionData, ]
testingDataSet <- trainingData[-traningPartitionData, ]
dim(trainingData); dim(testingDataSet)

```

## Prediction model 1 - decision tree

```{r feature decision tree}

decisionTreeModel <- rpart(classe ~ ., data = trainingDataSet, method = "class")
decisionTreePrediction <- predict(decisionTreeModel, testingDataSet, type = "class")

# Plot Decision Tree
rpart.plot(decisionTreeModel, main = "Decision Tree", under = T, faclen = 0)

# Using confusion matrix to test results
confusionMatrix(decisionTreePrediction, testingDataSet$classe)

```


## Prediction model 2 - random forest

```{r random forest}

randomForestModel <- randomForest(classe ~. , data = trainingDataSet, method = "class")
randomForestPrediction <- predict(randomForestModel, testingDataSet, type = "class")

confusionMatrix(randomForestPrediction, testingDataSet$classe)

```


## Prediction model 2 - random forest
From the result, it show Random Forest accuracy is higher than Decision tree which is 0.9915  > 0.6644. Therefore, we will use random forest to answer the assignment. 

```{r final prediction}
predictionFinal <- predict(randomForestModel, testingDataSet, type = "class")
#predictionFinal

```
