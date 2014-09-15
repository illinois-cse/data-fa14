We are going to do another demo project to demonstrate how to do some
advanced data analysis using R. Specifically, we will cover topics
including regression models, hypothesis tests and reproducible research.

We will use a built-in data set of R, **mtcars**, to do this project.
The data was extracted from the 1974 Motor Trend US magazine, and
comprises fuel consumption and 10 aspects of automobile design and
performance for 32 automobiles (1973–74 models). We are interested in
the following questions:

1.  Is there a difference in fuel efficiency between cars with automatic
    and manual transmissions?

2.  Can we develop a regression model for fuel efficiency using all
    other variables in the data set?

Read Data and Display Data
--------------------------

    # Read data and take a quick look at it
    data(mtcars);
    str(mtcars);

    ## 'data.frame':    32 obs. of  11 variables:
    ##  $ mpg : num  21 21 22.8 21.4 18.7 18.1 14.3 24.4 22.8 19.2 ...
    ##  $ cyl : num  6 6 4 6 8 6 8 4 4 6 ...
    ##  $ disp: num  160 160 108 258 360 ...
    ##  $ hp  : num  110 110 93 110 175 105 245 62 95 123 ...
    ##  $ drat: num  3.9 3.9 3.85 3.08 3.15 2.76 3.21 3.69 3.92 3.92 ...
    ##  $ wt  : num  2.62 2.88 2.32 3.21 3.44 ...
    ##  $ qsec: num  16.5 17 18.6 19.4 17 ...
    ##  $ vs  : num  0 0 1 1 0 1 0 1 1 1 ...
    ##  $ am  : num  1 1 1 0 0 0 0 0 0 0 ...
    ##  $ gear: num  4 4 4 3 3 3 3 4 4 4 ...
    ##  $ carb: num  4 4 1 1 2 1 4 2 2 4 ...

Difference between automatic and manual transmissions
-----------------------------------------------------

First, plot some exploratory graphs.

    # Extract the data we need
    automatic = mtcars[mtcars$am == 0, ]$mpg; 
    manual = mtcars[mtcars$am == 1, ]$mpg;

    # Plot the graphs
    par(mfrow = c(1,2));
    plot(automatic, col="red", pch=18, main = "Figure 1", xlab = "", ylab = "Miles per Gallon");
    points(manual, col="green", pch=18);
    legend("topright",c("Automatic", "Manual"), pch = c(18, 18), col = c("red", "green"));

    boxplot(mpg~am,data=mtcars,main="Figure 2",varwidth=TRUE, 
            col=c("green","red"), names=c("Automatic","Manual"));

![plot of chunk
explore](./img/explore.png)

From the graph, we see that most points of manual transmission are above
those of automatic transmission. In order to see the difference
statistically, we perform a [student's
t-test](http://en.wikipedia.org/wiki/Student's_t-test) about the null
hypothesis that automatic and manual transmission have the same mean
value for miles per gallon(mpg).

    # Do the t-test for difference between means
    t.test(automatic, manual, alternative="two.sided", conf.level=0.99);

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  automatic and manual
    ## t = -3.767, df = 18.33, p-value = 0.001374
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 99 percent confidence interval:
    ##  -12.769  -1.721
    ## sample estimates:
    ## mean of x mean of y 
    ##     17.15     24.39

So, with a confidence level of 99%, we can claim that in 1973-74 models,
automatic transmissions have smaller mpg values than manual
transmissions.

Regression Model
----------------

In the previous section, we see that the variable **mpg** varies much
for different values of the variable **am**. In this way, we will
definitely have the variable **am** inside the regression model for
**mpg**. Further more, we would also like to include interaction terms
of **am** with other variables. So, what we will do is: include all
variables inside the model (both variables and interaction terms with
am). Then, we will remove variables from the model gradually to get our
final model.

    # Include all variables and interaction terms with am
    fit <- lm(mpg~as.factor(am) *., data = mtcars);
    summary(fit);

    ## 
    ## Call:
    ## lm(formula = mpg ~ as.factor(am) * ., data = mtcars)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -2.035 -0.760  0.109  0.548  2.696 
    ## 
    ## Coefficients: (2 not defined because of singularities)
    ##                      Estimate Std. Error t value Pr(>|t|)  
    ## (Intercept)            8.6435    22.3728    0.39    0.706  
    ## as.factor(am)1      -146.5509    66.3235   -2.21    0.047 *
    ## cyl                   -0.5339     1.1726   -0.46    0.657  
    ## disp                  -0.0203     0.0181   -1.12    0.286  
    ## hp                     0.0622     0.0479    1.30    0.218  
    ## drat                   0.5916     3.1326    0.19    0.853  
    ## wt                     1.9541     2.3207    0.84    0.416  
    ## qsec                  -0.8843     0.7888   -1.12    0.284  
    ## vs                     0.7389     2.6125    0.28    0.782  
    ## am                         NA         NA      NA       NA  
    ## gear                   8.6542     4.0517    2.14    0.054 .
    ## carb                  -4.8105     1.9765   -2.43    0.032 *
    ## as.factor(am)1:cyl    -0.7474     4.2614   -0.18    0.864  
    ## as.factor(am)1:disp    0.2002     0.1596    1.25    0.234  
    ## as.factor(am)1:hp     -0.2227     0.1381   -1.61    0.133  
    ## as.factor(am)1:drat   -5.5414     5.8474   -0.95    0.362  
    ## as.factor(am)1:wt    -12.4960     5.0728   -2.46    0.030 *
    ## as.factor(am)1:qsec    8.9793     3.2147    2.79    0.016 *
    ## as.factor(am)1:vs      0.2042     5.2854    0.04    0.970  
    ## as.factor(am)1:am          NA         NA      NA       NA  
    ## as.factor(am)1:gear    3.6743     7.2513    0.51    0.622  
    ## as.factor(am)1:carb    9.4991     4.1683    2.28    0.042 *
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1.88 on 12 degrees of freedom
    ## Multiple R-squared:  0.962,  Adjusted R-squared:  0.903 
    ## F-statistic: 16.2 on 19 and 12 DF,  p-value: 8.25e-06

Then we choose variables that are significant in the model, which are
**as.factor(am)**, **as.factor(am):wt**, **as.factor(am):qsec** and
**as.factor(am):carb** and do a nested model test.

    fit1 <- lm(mpg ~ as.factor(am), data = mtcars);
    fit2 <- update(fit, mpg~ as.factor(am):wt);
    fit3 <- update(fit, mpg~ as.factor(am):(wt + qsec));
    fit4 <- update(fit, mpg~ as.factor(am):(wt + qsec + carb));
    anova(fit1, fit2, fit3, fit4);

    ## Analysis of Variance Table
    ## 
    ## Model 1: mpg ~ as.factor(am)
    ## Model 2: mpg ~ as.factor(am):wt
    ## Model 3: mpg ~ as.factor(am):wt + as.factor(am):qsec
    ## Model 4: mpg ~ as.factor(am):wt + as.factor(am):qsec + as.factor(am):carb
    ##   Res.Df RSS Df Sum of Sq      F  Pr(>F)    
    ## 1     30 721                                
    ## 2     29 270  1       451 102.09 2.6e-10 ***
    ## 3     27 119  2       151  17.09 2.1e-05 ***
    ## 4     25 110  2         8   0.93    0.41    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

From the result, fit3 with formula: **mpg ~ as.factor(am):(wt + qsec)**
is the best model among these four models. We do the regression and see
the results for coefficients and residuals.

    summary(fit3);

    ## 
    ## Call:
    ## lm(formula = mpg ~ as.factor(am):wt + as.factor(am):qsec, data = mtcars)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -3.936 -1.402 -0.155  1.269  3.886 
    ## 
    ## Coefficients:
    ##                     Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)           13.969      5.776    2.42   0.0226 *  
    ## as.factor(am)0:wt     -3.176      0.636   -4.99  3.1e-05 ***
    ## as.factor(am)1:wt     -6.099      0.969   -6.30  9.7e-07 ***
    ## as.factor(am)0:qsec    0.834      0.260    3.20   0.0035 ** 
    ## as.factor(am)1:qsec    1.446      0.269    5.37  1.1e-05 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 2.1 on 27 degrees of freedom
    ## Multiple R-squared:  0.895,  Adjusted R-squared:  0.879 
    ## F-statistic: 57.3 on 4 and 27 DF,  p-value: 8.42e-13

Compared the adjusted R squared between fit3 and fit (0.879 and 0.903),
this could be the model we use to do analysis between **mpg** and
**am**. A plot for residuals is shown in Figure 3. From the plot we can
see that the model fits well with few outliers.

    # Plot residuals of the final model.
    par(mfrow = c(2,2), oma = c(0,2,4,2))
    plot(fit3);
    mtext("Figure 3 Residual Diagnostics", cex = 1.5, outer = TRUE, line = 1)

![plot of chunk
residualplot](./img/residualplot.png)

**Analysis of the model**: If we hold the **qsec** constant at its mean,
for each unit increase in the weight starting at mean, miles per gallon
will decrease -3.176 for manual cars and -6.1 for automatic cars. These
coefficients are quite significant in the model with low standard
errors. On the other hand, if we hold the weight constant at its mean,
for each unit increase in the qsec starting at mean value, manual cars
will make 0.834 more miles per gallon while 1.446 for automatic cars.

Reproducible research
---------------------

The R package, *knitr*, provides a great tool to make your research
reproducible. It allows you to include R code, graphs and many things
else inside a markdown file. Please go to
[KnitR](http://yihui.name/knitr/): <http://yihui.name/knitr/> to find
more help files.
