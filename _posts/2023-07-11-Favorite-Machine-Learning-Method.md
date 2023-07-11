Favorite Machine Learning Method
================
Grace Holliday
2023-07-11

# The Most Interesting Machine Learning Method

I think the most interesting method that I have learned in this class is
boosting. Boosting is a general approach that can be applied to trees
and allows for the slow training of trees. The trees are grown
sequentially with each subsequent tree being grown on a modified version
of the original data. The predictions are then updated as the trees are
grown, and new trees are grown by considering the errors in the trees
previously created. Lambda represents a shrinkage parameter than slows
the fitting process.

# Fitting a Boosted Model

``` r
# First setting seed for reproducibility.
set.seed(1234)

# Divide data into training and test set.
train <- sample(1:nrow(iris), size = nrow(iris)*.70)
test <- setdiff(1:nrow(iris), train)

# training and testing subsets
datTrain <- iris[train, ]
datTest <- iris[test, ]
```

``` r
## fitting amodel
boostFit <- train(Petal.Length ~ I(Sepal.Length^2)+Species+Sepal.Width,
                data=iris, method='gbm',
                preProcess=c("center","scale"),
                trControl=trainControl(method='cv',number=5),
                tuneGrid=expand.grid(n.trees=seq(25,200,50),
                                     interaction.depth=seq(1,4,1),
                                     shrinkage=0.1, n.minobsinnode=10))
```

``` r
plot(boostFit)
```

![](C:/Users/Sarah/Documents/ghollid.github.io/_posts/2023-07-11-Favorite-Machine-Learning-Method_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
boostFit$finalModel
```

    ## A gradient boosted model with gaussian loss function.
    ## 175 iterations were performed.
    ## There were 4 predictors of which 4 had non-zero influence.

``` r
#Run on test data
boostFit.predict <- predict(boostFit, newdata = datTest)

#Obtain RMSE from test set
boostFit.RMSE <- postResample(boostFit.predict, obs = datTest$Petal.Length)
boostFit.RMSE
```

    ##      RMSE  Rsquared       MAE 
    ## 0.3340400 0.9625241 0.2474259

This model has a relatively high R<sup>2</sup> value, but the RMSE
probably isn’t as low as we would like.
