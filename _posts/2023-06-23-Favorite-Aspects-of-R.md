Favorite Aspect of R Learned in This Course
================
Grace Holliday
2023-06-05

# The Coolest Thing I’ve Learned About Programming in R

Given that I have a fair amount of experience with R and more
specifically, ggplot and the tidyverse, I wouldn’t consider these to be
the most interesting this I have learned in this course. I think I would
say what we have learned starting in the end of last unit and the
beginning of this one is what I find most interesting, being parallel
computing and (broadly) machine learning. I have enjoyed learning the
application of statistical methods in R more than anything thus far.

## Parallel Computing Example Code

Below is a brief example (from HW) of how you may implement parallel
computing using R. This example uses different cores to apply the Lapply
function many different times, simultaneously.

``` r
# establishing clusters
library('parallel')
cores <- detectCores()
cores

cluster <- makeCluster(cores-1)
clusterEvalQ(cluster,library(tidyverse))

clusterExport(cluster,list("fun","reject2","teststat"))
xyz <- parLapply(cl=cluster,1:length(X), fun,data=X)

parResults = data.frame(expand.grid(list(ss,shapes)))
parResults$results <- unlist(xyz)
colnames(parResults)[1] <- "n"
colnames(parResults)[2] <- "Shape"
parResults
```

## (Basic) Machine Learning Example Code

The below code is a brief example of some basic machine learning. It
takes data from the **iris** dataset and splits it into a training and
test set. It then fits two different models using 5-fold cross
validation and compares the two using their RMSE and R<sup>2</sup>
values to determine which is the better model. After this, it uses the
better model on the test data to explore how good of a fit for the data
this model is.

``` r
# splitting data
train <- sample(1:nrow(iris), size = nrow(iris)*.75)
test <- setdiff(1:nrow(iris), train)

# training and testing subsets
irisTrain <- iris[train, ]
irisTest <- iris[test, ]

# fitting a model using 5-fold cross validation and RMSE as metrics
mod1 <- train(Petal.Length ~ Sepal.Length + Sepal.Width ,data=iris, method='lm',
                preProcess=c("center","scale"),
                trControl=trainControl(method='cv',number=5))

mod1
```

    ## Linear Regression 
    ## 
    ## 150 samples
    ##   2 predictor
    ## 
    ## Pre-processing: centered (2), scaled (2) 
    ## Resampling: Cross-Validated (5 fold) 
    ## Summary of sample sizes: 119, 120, 122, 119, 120 
    ## Resampling results:
    ## 
    ##   RMSE       Rsquared   MAE      
    ##   0.6579849  0.8717543  0.5320439
    ## 
    ## Tuning parameter 'intercept' was held constant at a value of TRUE

``` r
## fitting another model
mod2 <- train(Petal.Length ~ I(Sepal.Length^2)+Species+Sepal.Width,
                data=iris, method='lm',
                preProcess=c("center","scale"),
                trControl=trainControl(method='cv',number=5))

mod2
```

    ## Linear Regression 
    ## 
    ## 150 samples
    ##   3 predictor
    ## 
    ## Pre-processing: centered (4), scaled (4) 
    ## Resampling: Cross-Validated (5 fold) 
    ## Summary of sample sizes: 121, 118, 120, 121, 120 
    ## Resampling results:
    ## 
    ##   RMSE       Rsquared   MAE     
    ##   0.2812264  0.9765743  0.214486
    ## 
    ## Tuning parameter 'intercept' was held constant at a value of TRUE

``` r
## Comparing results
# compile results for all models for training set
data.frame(t(mod1$results), t(mod2$results))
```

    ##                    X1        X1.1
    ## intercept  1.00000000 1.000000000
    ## RMSE       0.65798495 0.281226401
    ## Rsquared   0.87175433 0.976574280
    ## MAE        0.53204389 0.214486017
    ## RMSESD     0.05205845 0.026023574
    ## RsquaredSD 0.01893746 0.002819945
    ## MAESD      0.03120643 0.022579912

``` r
#taking best model (Mod2) and using it on test data
pred <- predict(mod2, newdata=irisTest)
postResample(pred,obs=irisTest$Petal.Length)
```

    ##      RMSE  Rsquared       MAE 
    ## 0.2509211 0.9838036 0.1778353

\`\`\`
