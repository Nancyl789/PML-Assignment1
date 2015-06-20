Practical Machine Learning Prediction Motivation

Background  
Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the website here: http://groupware.les.inf.puc-rio.br/har (see the section on the Weight Lifting Exercise Dataset). 

Data  
The training data for this project are available here: 
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv
The test data are available here: 
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv
The data for this project come from this source: http://groupware.les.inf.puc-rio.br/har.

download packages
> library(caret)  
> library(kernlab)  
> library(randomForest)  
> library (MASS)  
> library(rpart)

download/read the data
> pml_training=read.csv('pml-training.csv', na.strings=c("NA",""))  
> pml_testing=read.csv('pml-testing.csv', na.strings=c("NA",""))  

pml_training=19622 obs. of 160 variables  
[1] 19622   160

clean the data
removal of Near-Zero Values  
> nzv <- nearZeroVar(pml_training)     
> trainnzv <- pml_training[-nzv]  

removal of NAs  
> train1 <- trainnzv[, which(as.numeric(colSums(is.na(trainnzv)))==0)]   

removal of non-numeric Vb (x, user_name, raw_time_stamp 1, and 2, cvtd_timestamp, num_window)  
> train2 <- train1[,-(1:7)]

distribution of the Vb classe (A,B,C,D,E)  
> table(train2$classe)  

   A    B    C    D    E    
5580 3797 3422 3216 3607  

split training data into a training (70%) and a testing (30%) dataset  
> inTrain<-createDataPartition(y=train2$classe,
                             p=0.7, list=FALSE)  
> set.seed(3333)  
> trainSplit <- train2[inTrain,]  
> testSplit <- train2[-inTrain,]  

> table(trainSplit$classe) 

   A    B    C    D    E 
3906 2658 2396 2252 2525 

check the classes of the columns in the dataframe.  
> lapply(trainSplit, class) 

model1: Random Forest
> RF <- train(classe ~ ., method = 'rf', data=trainSplit, ntrees=10)  

model2: 
> LDA <- train(classe ~ ., method = 'lda', data = trainSplit)   

model3: Recursive Partitioning and Regression Trees
> RPART<- train(classe ~ ., method = 'rpart', data = trainSplit)  

test Accuracy model:Randomn Forest
> RF_accuracy<- predict(RF, testSplit)  
> print(confusionMatrix(RF_accuracy, testSplit$classe))  

test Accuracy model:Linear
> LDA_accuracy<- predict(LDA, testSplit)  
> print(confusionMatrix(LDA_accuracy, testSplit$classe))  

Confusion Matrix and Statistics  
 Prediction    A    B    C    D    E
         A 1349  166   90   49   50  
         B   40  740  103   55  164  
         C  139  140  664  120  116  
         D  137   43  152  678  115  
         E    9   50   17   62  637

Overall Statistics
               Accuracy : 0.6912         
                 95% CI : (0.6793, 0.703)  
    No Information Rate : 0.2845         
    P-Value [Acc > NIR] : < 2.2e-16      


                  Kappa : 0.6096         
 Mcnemar's Test P-Value : < 2.2e-16      

Statistics by Class:
                     Class: A Class: B Class: C Class: D Class: E  
Sensitivity            0.8059   0.6497   0.6472   0.7033   0.5887  
Specificity            0.9157   0.9237   0.8940   0.9092   0.9713  
Pos Pred Value         0.7917   0.6715   0.5632   0.6027   0.8219  
Neg Pred Value         0.9223   0.9166   0.9231   0.9399   0.9129  
Prevalence             0.2845   0.1935   0.1743   0.1638   0.1839  
Detection Rate         0.2292   0.1257   0.1128   0.1152   0.1082  
Detection Prevalence   0.2895   0.1873   0.2003   0.1912   0.1317  
Balanced Accuracy      0.8608   0.7867   0.7706   0.8062   0.7800  

test Accuracy model:
> RPART_accuracy<- predict(RPART, testSplit)  
> print(confusionMatrix(RPART_accuracy, testSplit$classe)) 

to avoid over-fitting -> cross validation with 10 folds.
> set.seed(3333)   
> controlOF <- trainControl(method = "repeatedcv", number = 10, repeats = 10)  
> RF_CV <- train(classe ~ ., method="rf",  data=trainSplit, trControl = controlOF)

test Accuracy model:  
> RF_CV_accuracy<- predict(RF_CV, testSplit) 
> print(confusionMatrix(RF_CV_accuracy, testSplit$classe))  

test set prediction
> testing <- predict(RF, pml_testing)   
> testing  
