# Practical Machine Learning -Final Project 


```R
#Importing libaries 
library(caret)
library(randomForest)
library(e1071)
```


```R
#Read data 
rawData <- read.csv("pml-training.csv", na.strings = c("NA", ""))
```


```R
#Pre-processing
propNAs <- colMeans(is.na(rawData))
table(propNAs)
idx <- !propNAs
rawDataReduced <- rawData[idx]

idx <- grep("^X$|user_name|timestamp|window", names(rawDataReduced))
rawDataReduced2 <- rawDataReduced[-idx]
```


    propNAs
                    0 0.979308938946081 
                   60               100 



```R
#Train set 
inTrain <- createDataPartition(y = rawDataReduced2$classe, p = 0.7, list = FALSE)
training <- rawDataReduced2[inTrain, ]
crossval <- rawDataReduced2[-inTrain, ]
```


```R
#Trainning model 
trControl <- trainControl(method = "cv", number = 2)
modFit <- train(classe ~ ., data = training, method = "rf", prox = TRUE, trControl = trControl)
```


```R
#Prediction model 
pred <- predict(modFit, newdata = crossval)
coMa <- confusionMatrix(pred, reference = crossval$classe)
acc <- coMa$overall["Accuracy"]
acc
```


<strong>Accuracy:</strong> 0.99303313508921



```R
#Variable importance 
vi <- varImp(modFit)$importance
vi[head(order(unlist(vi), decreasing = TRUE), 5L), , drop = FALSE]
```


<table>
<thead><tr><th></th><th scope=col>Overall</th></tr></thead>
<tbody>
	<tr><th scope=row>roll_belt</th><td>100.00000</td></tr>
	<tr><th scope=row>yaw_belt</th><td> 78.16359</td></tr>
	<tr><th scope=row>magnet_dumbbell_z</th><td> 67.30660</td></tr>
	<tr><th scope=row>magnet_dumbbell_y</th><td> 63.19929</td></tr>
	<tr><th scope=row>pitch_forearm</th><td> 62.34782</td></tr>
</tbody>
</table>


