------------------------------------------------------------------------

\#\#\#\#A1

In this project we want to determine the effects of gene knockout and
sex on olfactory response in Drosophila.

    d <- read.table(file="olfactory_response.tsv", sep="\t", header=TRUE)
    head(d)

    ##    sex knockout response
    ## 1 male wildtype 4.937247
    ## 2 male knockout 3.856852
    ## 3 male wildtype 5.528290
    ## 4 male knockout 2.562061
    ## 5 male wildtype 3.687418
    ## 6 male knockout 2.522212

    table(d$sex, d$knockout)

    ##         
    ##          knockout wildtype
    ##   female       13       13
    ##   male         13       13

**Here we want to compare olfactory response with sex and gene
knockout.**

Let’s first perform anova on the full model.

    summary(aov(response ~ knockout * sex, data=d))

    ##              Df Sum Sq Mean Sq F value   Pr(>F)    
    ## knockout      1  18.81   18.81   28.53 2.50e-06 ***
    ## sex           1  46.21   46.21   70.11 6.04e-11 ***
    ## knockout:sex  1  36.65   36.65   55.61 1.47e-09 ***
    ## Residuals    48  31.64    0.66                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Here the both the marginal effects and interaction effect are all
significant. The same was true for regression with categorical
variables.

    summary(lm(response ~ knockout * sex, data=d))

    ## 
    ## Call:
    ## lm(formula = response ~ knockout * sex, data = d)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -1.41739 -0.68489 -0.07257  0.55090  1.98906 
    ## 
    ## Coefficients:
    ##                          Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)                7.0244     0.2252  31.196  < 2e-16 ***
    ## knockoutwildtype          -2.8819     0.3184  -9.050 5.99e-12 ***
    ## sexmale                   -3.5645     0.3184 -11.194 5.56e-15 ***
    ## knockoutwildtype:sexmale   3.3582     0.4503   7.457 1.47e-09 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.8119 on 48 degrees of freedom
    ## Multiple R-squared:  0.7627, Adjusted R-squared:  0.7478 
    ## F-statistic: 51.42 on 3 and 48 DF,  p-value: 5.03e-15

Box plot to model differences.

    boxplot(response ~ knockout * sex, data=d)

<img src="drosophila_files/figure-markdown_strict/unnamed-chunk-5-1.png" style="display: block; margin: auto;" />

With this visualization we can see more clearly that the gene might only
have an effect in females.

Next we can test this by doing anova on each sex. females:

    summary(aov(response ~ knockout, data=d[d$sex == "female",]))

    ##             Df Sum Sq Mean Sq F value   Pr(>F)    
    ## knockout     1  53.98   53.98   90.81 1.25e-09 ***
    ## Residuals   24  14.27    0.59                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

males:

    summary(aov(response ~ knockout, data=d[d$sex == "male",]))

    ##             Df Sum Sq Mean Sq F value Pr(>F)
    ## knockout     1  1.475  1.4747   2.037  0.166
    ## Residuals   24 17.372  0.7238

The F tests performed show clearly that the effect of the knockout is
only present in female flies.

------------------------------------------------------------------------
