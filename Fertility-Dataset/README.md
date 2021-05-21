------------------------------------------------------------------------

# Fertility Dataset

In this project, we want to obtain the confidence interval for fertility
when fertility is modeled along side income and urbanization.

Data: Wikipedia detailing fertility, income, and urbanization from the
50 US states and the District of Columbia.

    f <- read.table(file="predicting_fertility.tsv", sep="\t", header=TRUE, stringsAsFactors=FALSE)
    head(f)

    ##            state income fertility urbanization
    ## 1        Arizona  56581      1.79        0.898
    ## 2        Georgia  56183      1.79        0.751
    ## 3      Wisconsin  59305      1.79        0.702
    ## 4         Nevada  58003      1.77        0.942
    ## 5 North Carolina  52752      1.77        0.661
    ## 6       Michigan  54909      1.76        0.746

    cor(f$fertility, f$income)

    ## [1] -0.3119353

Regression of income vs fertility:

    res <- summary(lm(fertility ~ income, data=f))
    print(res)

    ## 
    ## Call:
    ## lm(formula = fertility ~ income, data = f)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.28177 -0.10527 -0.02235  0.06891  0.42323 
    ## 
    ## Coefficients:
    ##               Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  2.092e+00  1.340e-01  15.616   <2e-16 ***
    ## income      -5.047e-06  2.196e-06  -2.298   0.0259 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.1588 on 49 degrees of freedom
    ## Multiple R-squared:  0.0973, Adjusted R-squared:  0.07888 
    ## F-statistic: 5.282 on 1 and 49 DF,  p-value: 0.02586

Extracting values for *s*<sub>*b̂*<sub>1</sub></sub> :

    sb1 <- res$coefficients["income", "Std. Error"]

Confidence interval when just modeling fertility with income:

    b1 <- res$coefficients["income", "Estimate"]
    b1 + qt(c(0.025, 0.975), df=nrow(f)-2) * sb1

    ## [1] -9.460793e-06 -6.339229e-07

Both ends of this interval are negative, therefore the confidence
interval does not include 0.

Now income vs fertility and regression line:

    intercept <- res$coefficients["(Intercept)", "Estimate"]
    plot(fertility ~ income, data=f)
    abline(intercept, b1, col="red")

<img src="Figure1" style="display: block; margin: auto;" />
Lowest fertility:

    f[which.min(f$fertility),]

    ##                   state income fertility urbanization
    ## 28 District of Columbia  82372      1.42            1

Highest Fertility:

    f[which.max(f$fertility),]

    ##           state income fertility urbanization
    ## 29 South Dakota  56521      2.23        0.567

The District of Columbia has the lowest fertility, while South Dakota
has the highest.

    sum(f$urbanization == 1)

    ## [1] 1

DC is the only place with 100% urbanization, because its the only place
that is not a state.

Given that the lowest fertility has highest urbanization, perhaps
urbanization should be included in the model.

Here’s a new linear model with `urbanization` as a predictor:

    res2 <- summary(lm(fertility ~ income + urbanization, data=f))
    print(res2)

    ## 
    ## Call:
    ## lm(formula = fertility ~ income + urbanization, data = f)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.30432 -0.10021 -0.01547  0.06449  0.41344 
    ## 
    ## Coefficients:
    ##                Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   2.108e+00  1.417e-01  14.872   <2e-16 ***
    ## income       -4.461e-06  2.739e-06  -1.629    0.110    
    ## urbanization -6.847e-02  1.881e-01  -0.364    0.717    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.1602 on 48 degrees of freedom
    ## Multiple R-squared:  0.09979,    Adjusted R-squared:  0.06228 
    ## F-statistic:  2.66 on 2 and 48 DF,  p-value: 0.08022

New confidence interval:

    res$coefficients["income", "Estimate"] + qt(0.025, df=49)*sb1

    ## [1] -9.460793e-06

    res$coefficients["income", "Estimate"] + qt(0.975, df=49)*sb1

    ## [1] -6.339229e-07

Including urbanization in the model, removes the effect between income
and fertility. Unlike the previous model, the confidence interval now
includes 0.

------------------------------------------------------------------------
