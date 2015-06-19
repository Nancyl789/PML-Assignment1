# PML-Assignment1

Background
Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the website here: http://groupware.les.inf.puc-rio.br/har (see the section on the Weight Lifting Exercise Dataset). 

Data 
The training data for this project are available here: 
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv
The test data are available here: 
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv
The data for this project come from this source: http://groupware.les.inf.puc-rio.br/har.
Practical Machine Learning Prediction Motivation

> library(caret)  
> library(kernlab)  
> library(randomForest)  
> library(rpart)

### download/read the data
> pml_training=read.csv('pml-training.csv', na.strings=c("NA",""))  
> pml_testing=read.csv('pml-testing.csv', na.strings=c("NA",""))  

### clean the data
### removal of Near-Zero Values 
> nzv <- nearZeroVar(pml_training)   
> trainnzv <- pml_training[-nzv]  
> train1 <- trainnzv[, which(as.numeric(colSums(is.na(trainnzv)))==0)]   

### removal of NAs  
> train2 <- train1[,-(1:7)]

### removal of non-numeric Vb (x, user_name, raw_time_stamp 1, and 2, cvtd_timestamp, num_window)  

### class into factor
> train2$classe <- factor(train2$classe) 

> inTrain<-createDataPartition(y=train2$classe,
                             p=0.5, list=FALSE)  
> set.seed(3333)  
> trainSplit <- train2[inTrain,]  
> testSplit <- train2[-inTrain,]  

> table(trainSplit$classe) 

### check the classes of the columns in the dataframe.  
> lapply(trainSplit, class) 

> RF <- train(classe ~ ., method = 'rf', data=trainSplit, ntrees=10)  

### model Random Forest      
> LDA <- train(classe ~ ., method = 'lda', data = trainSplit)   
> RPART<- train(classe ~ ., method = 'rpart', data = trainSplit)  

### test Accuracy model:Randomn Forest
> RF_accuracy<- predict(RF, testSplit)  
> print(confusionMatrix(RF_accuracy, testSplit$classe))  

### test Accuracy model:Linear
> LDA_accuracy<- predict(LDA, testSplit)  
> print(confusionMatrix(LDA_accuracy, testSplit$classe))  

### test Accuracy model:
> RPART_accuracy<- predict(RPART, testSplit)  
> print(confusionMatrix(RPART_accuracy, testSplit$classe)) 

### to avoid over-fitting -> cross validation with 10 folds.
> set.seed(3333)   
> controlOF <- trainControl(method = "repeatedcv", number = 10, repeats = 10)  
> RF_CV <- train(classe ~ ., method="rf",  data=trainSplit, trControl = controlOF)

### test Accuracy model:  
> RF_CV_accuracy<- predict(RF_CV, testSplit) 
> print(confusionMatrix(RF_CV_accuracy, testSplit$classe))  

### test set prediction
> testing <- predict(RF, pml_testing)   
> testing  
