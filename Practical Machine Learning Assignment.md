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
> library (caret)  
> library (kernlab)  
> library (randomForest)  
> library (MASS)  
> library (rpart)

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
seed for pseudo-random generator
> set.seed(3333)  
> trainSplit <- train2[inTrain,]  
> testSplit <- train2[-inTrain,]  

> table(trainSplit$classe) 

A    B    C    D    E  
3906 2658 2396 2252 2525 

model1: Random Forest (RF)
> RF <- train(classe ~ ., method = 'rf', data=trainSplit, ntrees=10)  

model2: Linear Discriminant Analysis (LDA)
> LDA <- train(classe ~ ., method = 'lda', data = trainSplit)   

model3: Recursive Partitioning and Regression Trees (RPART)
> RPART<- train(classe ~ ., method = 'rpart', data = trainSplit)  


test Accuracy model: Random Forest
> RF_accuracy<- predict(RF, testSplit)  
> print(confusionMatrix(RF_accuracy, testSplit$classe))  

#####Confusion Matrix and Statistics      
          A     B    C    D    E  
      A 1674    4    0    0    0  
      B    0 1133   11    0    0  
      C    0    2 1015   10    0  
      D    0    0    0  953    0  
      E    0    0    0    1 1082  
         
######Overall Statistics  
               Accuracy : 0.9952          
                 95% CI : (0.9931, 0.9968)   
    No Information Rate : 0.2845          
    P-Value [Acc > NIR] : < 2.2e-16       
                   Kappa : 0.994           
 Mcnemar's Test P-Value : NA        
 
######Statistics by Class:  
                     Class: A Class: B Class: C Class: D Class: E  
Sensitivity            1.0000   0.9947   0.9893   0.9886   1.0000  
Specificity            0.9991   0.9977   0.9975   1.0000   0.9998  
Pos Pred Value         0.9976   0.9904   0.9883   1.0000   0.9991  
Neg Pred Value         1.0000   0.9987   0.9977   0.9978   1.0000  
Prevalence             0.2845   0.1935   0.1743   0.1638   0.1839  
Detection Rate         0.2845   0.1925   0.1725   0.1619   0.1839  
Detection Prevalence   0.2851   0.1944   0.1745   0.1619   0.1840  
Balanced Accuracy      0.9995   0.9962   0.9934   0.9943   0.9999  



test Accuracy model: Linear Discriminant Analysis
> LDA_accuracy<- predict(LDA, testSplit)  
> print(confusionMatrix(LDA_accuracy, testSplit$classe))  

#####Confusion Matrix and Statistics  
             A    B    C    D    E  
         A 1349  166   90   49   50  
         B   40  740  103   55  164  
         C  139  140  664  120  116  
         D  137   43  152  678  115  
         E    9   50   17   62  637

#####Overall Statistics
               Accuracy : 0.6912         
                 95% CI : (0.6793, 0.703)  
    No Information Rate : 0.2845         
    P-Value [Acc > NIR] : < 2.2e-16      
                  Kappa : 0.6096         
 Mcnemar's Test P-Value : < 2.2e-16      

#####Statistics by Class:
                     Class: A Class: B Class: C Class: D Class: E  
Sensitivity            0.8059   0.6497   0.6472   0.7033   0.5887  
Specificity            0.9157   0.9237   0.8940   0.9092   0.9713  
Pos Pred Value         0.7917   0.6715   0.5632   0.6027   0.8219  
Neg Pred Value         0.9223   0.9166   0.9231   0.9399   0.9129  
Prevalence             0.2845   0.1935   0.1743   0.1638   0.1839  
Detection Rate         0.2292   0.1257   0.1128   0.1152   0.1082  
Detection Prevalence   0.2895   0.1873   0.2003   0.1912   0.1317  
Balanced Accuracy      0.8608   0.7867   0.7706   0.8062   0.7800  



test Accuracy model: Recursive Partitioning and Regression Trees (RPART)
> RPART_accuracy<- predict(RPART, testSplit)  
> print(confusionMatrix(RPART_accuracy, testSplit$classe)) 

#####Confusion Matrix and Statistics  
              A    B    C    D    E  
         A 1508  440  471  420  236  
         B   37  373   30   24  174  
         C  128  284  525  354  300  
         D    0   42    0  166   57  
         E    1    0    0    0  315  
#####Overall Statistics  
               Accuracy : 0.4906          
                 95% CI : (0.4777, 0.5034)  
    No Information Rate : 0.2845          
    P-Value [Acc > NIR] : < 2.2e-16      
    Kappa : 0.335           
 Mcnemar's Test P-Value : < 2.2e-16       

#####Statistics by Class:
                     Class: A Class: B Class: C Class: D Class: E  
Sensitivity            0.9008  0.32748  0.51170  0.17220  0.29113  
Specificity            0.6279  0.94416  0.78061  0.97988  0.99979  
Pos Pred Value         0.4904  0.58464  0.32998  0.62642  0.99684  
Neg Pred Value         0.9409  0.85401  0.88333  0.85801  0.86227  
Prevalence             0.2845  0.19354  0.17434  0.16381  0.18386  
Detection Rate         0.2562  0.06338  0.08921  0.02821  0.05353  
Detection Prevalence   0.5225  0.10841  0.27035  0.04503  0.05370  
Balanced Accuracy      0.7644  0.63582  0.64615  0.57604  0.64546  

model 	Accuracy 	Kappa
RF        0.9952      0.994
LDA       0.6912      0.6096 
RPART     0.4906      0.335

to avoid over-fitting -> cross validation with 10 folds.
> set.seed(3333)   
> controlOF <- trainControl(method = "repeatedcv", number = 10, repeats = 10)  
> RF_CV <- train(classe ~ ., method="rf",  data=trainSplit, trControl = controlOF)

test Accuracy model:  
> RF_CV_accuracy<- predict(RF_CV, testSplit) 
> print(confusionMatrix(RF_CV_accuracy, testSplit$classe))  

#####Confusion Matrix and Statistics  
              A    B    C    D    E  
         A 1673    5    0    0    0  
         B    1 1131   12    0    0  
         C    0    3 1014   10    0  
         D    0    0    0  953    0  
         E    0    0    0    1 1082  

######Overall Statistics  
                Accuracy : 0.9946          
                 95% CI : (0.9923, 0.9963)  
    No Information Rate : 0.2845          
    P-Value [Acc > NIR] : < 2.2e-16       
                  Kappa : 0.9931          
 Mcnemar's Test P-Value : NA              

######Statistics by Class:  
                     Class: A Class: B Class: C Class: D Class: E  
Sensitivity            0.9994   0.9930   0.9883   0.9886   1.0000  
Specificity            0.9988   0.9973   0.9973   1.0000   0.9998  
Pos Pred Value         0.9970   0.9886   0.9873   1.0000   0.9991  
Neg Pred Value         0.9998   0.9983   0.9975   0.9978   1.0000  
Prevalence             0.2845   0.1935   0.1743   0.1638   0.1839  
Detection Rate         0.2843   0.1922   0.1723   0.1619   0.1839  
Detection Prevalence   0.2851   0.1944   0.1745   0.1619   0.1840  
Balanced Accuracy      0.9991   0.9951   0.9928   0.9943   0.9999  

test set prediction
> testing <- predict(RF, pml_testing)   
> testing  

#####Random forest provide the best results and the right predictions for the submission.
 1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20   
 B  A  B  A  A  E  D  B  A  A  B  C  B  A  E  E  A  B  B  B   
Levels: A B C D E  
