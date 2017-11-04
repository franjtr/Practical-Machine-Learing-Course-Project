Machine Learning Course Project {.title .toc-ignore}
===============================

#### *Francisco José Torres Ramírez* {.author}

#### *3 de noviembre de 2017* {.date}

The goal of your project is to predict the manner in which they did the
exercise. This is the “classe” variable in the training set. You may use
any of the other variables to predict with. You should create a report
describing how you built your model, how you used cross validation, what
you think the expected out of sample error is, and why you made the
choices you did. You will also use your prediction model to predict 20
different test cases.

Exploratory Data Analysis
-------------------------

``` {.r}
library(caret)
training <- read.csv("pml-training.csv")
dim(training)
```

    ## [1] 19622   160

First we are going to remove the columns with more than 15000 NA
(arround 75% of missing values), we also remove the first 7 columns
because they are not related to the problem.

``` {.r}
compcols <- colSums(is.na(training))<15000
rtrain <- training[compcols]
ftrain <- rtrain[,-(1:7)]
```

Model fitting
-------------

First we split the data into training and test set.

``` {.r}
set.seed(219) # For reproducibile purpose
inTrain <- createDataPartition(ftrain$classe, p=0.60, list=F)
trainData <- ftrain[inTrain, ]
testData <- ftrain[-inTrain, ]
```

We will fit a random forest algorithm and use 5 fold cross validation.

``` {.r}
control <- trainControl(method="cv", 5)
modelRf <- train(classe ~ ., data=trainData, method="rf", trControl=control, ntree=50)
```

Now we will calculate the accuracy of our model in the test data.

``` {.r}
pred <- predict(modelRf, testData)
acc <- sum(testData$classe==pred)/length(pred)
acc
```

    ## [1] 0.9835585

The accuracy of the model very high (more than 98%) so this is the final
model that we are going to use to predict the 20 test cases available in
the testing data.
