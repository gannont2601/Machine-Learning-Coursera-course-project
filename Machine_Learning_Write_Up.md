Machine Learning Course project
-------------------------------

Background
==========

Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now
possible to collect a large amount of data about personal activity
relatively inexpensively. These type of devices are part of the
quantified self movement - a group of enthusiasts who take measurements
about themselves regularly to improve their health, to find patterns in
their behavior, or because they are tech geeks. One thing that people
regularly do is quantify how much of a particular activity they do, but
they rarely quantify how well they do it. In this project, your goal
will be to use data from accelerometers on the belt, forearm, arm, and
dumbell of 6 participants. They were asked to perform barbell lifts
correctly and incorrectly in 5 different ways. More information is
available from the website here:
<http://groupware.les.inf.puc-rio.br/har> (see the section on the Weight
Lifting Exercise Dataset).

Data
====

The training data for this project are available here:

<https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv>

The test data are available here:

<https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv>

The data for this project come from this source:
<http://groupware.les.inf.puc-rio.br/har>. If you use the document you
create for this class for any purpose please cite them as they have been
very generous in allowing their data to be used for this kind of
assignment.

The goal of below analysis is to predict the manner in which they did
the exercise. This is the "classe" variable in the training set.

Load libraries
==============

    library('caret')

    ## Loading required package: lattice

    ## Loading required package: ggplot2

Load data
=========

-original data locations stated above -turn blanks into NA for cleaning
stage

    training <- read.csv("pml-training.csv", header = TRUE, na.strings = c("","NA"))
    testing <- read.csv("pml-testing.csv", header = TRUE)

clean data
==========

-remove summary observations from training data -remove columns with NA
in more than 75% of observations in training -remove same columns in
testing

    clean_training <- training[training$new_window == 'no', ]
    clean_training <- training[lapply(training, function(training) sum(is.na(training)) / length(training) ) < 0.75 ]
    clean_testing <- testing[lapply( training, function(training) sum(is.na(training)) / length(training) ) < 0.75 ]

Run Model
=========

-Chose random forest model and it returned 99% accuracy so didn't pursue
other models; -correctly predicted 20 out of 20 test dataset
observations -Processing times of random forest model were very long so
researched model tuning methods to reduce complexity - train control

    rf <- train(classe ~ ., clean_training, method = 'rf', 
                trainControl = trainControl(method = 'cv', number = 3),
                rf.tunegrid = data.frame(mtry = c(4)))

    ## Loading required package: randomForest

    ## randomForest 4.6-12

    ## Type rfNews() to see new features/changes/bug fixes.

    ## 
    ## Attaching package: 'randomForest'

    ## The following object is masked from 'package:ggplot2':
    ## 
    ##     margin

    rf

    ## Random Forest 
    ## 
    ## 19622 samples
    ##    59 predictor
    ##     5 classes: 'A', 'B', 'C', 'D', 'E' 
    ## 
    ## No pre-processing
    ## Resampling: Bootstrapped (25 reps) 
    ## Summary of sample sizes: 19622, 19622, 19622, 19622, 19622, 19622, ... 
    ## Resampling results across tuning parameters:
    ## 
    ##   mtry  Accuracy   Kappa    
    ##    2    0.9961159  0.9950855
    ##   41    0.9998728  0.9998391
    ##   81    0.9997622  0.9996992
    ## 
    ## Accuracy was used to select the optimal model using  the largest value.
    ## The final value used for the model was mtry = 41.

Predict using test set
======================

-correctly predicted 20 out of 20 test dataset observations

    rf.predict <- predict(rf, clean_testing)
