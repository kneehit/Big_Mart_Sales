Big Mart Sales
================
Nihit R. Save
20th January 2017

Dataset: <https://datahack.analyticsvidhya.com/contest/practice-problem-big-mart-sales-iii/>

Data Exploration
================

Loading Data from work directory.

``` r
train <- read.csv("Train.csv")
test <- read.csv("Test.csv")
```

Checking variables and their data types in training set.

``` r
str(train)
```

    ## 'data.frame':    8523 obs. of  12 variables:
    ##  $ Item_Identifier          : Factor w/ 1559 levels "DRA12","DRA24",..: 157 9 663 1122 1298 759 697 739 441 991 ...
    ##  $ Item_Weight              : num  9.3 5.92 17.5 19.2 8.93 ...
    ##  $ Item_Fat_Content         : Factor w/ 5 levels "LF","low fat",..: 3 5 3 5 3 5 5 3 5 5 ...
    ##  $ Item_Visibility          : num  0.016 0.0193 0.0168 0 0 ...
    ##  $ Item_Type                : Factor w/ 16 levels "Baking Goods",..: 5 15 11 7 10 1 14 14 6 6 ...
    ##  $ Item_MRP                 : num  249.8 48.3 141.6 182.1 53.9 ...
    ##  $ Outlet_Identifier        : Factor w/ 10 levels "OUT010","OUT013",..: 10 4 10 1 2 4 2 6 8 3 ...
    ##  $ Outlet_Establishment_Year: int  1999 2009 1999 1998 1987 2009 1987 1985 2002 2007 ...
    ##  $ Outlet_Size              : Factor w/ 4 levels "","High","Medium",..: 3 3 3 1 2 3 2 3 1 1 ...
    ##  $ Outlet_Location_Type     : Factor w/ 3 levels "Tier 1","Tier 2",..: 1 3 1 3 3 3 3 3 2 2 ...
    ##  $ Outlet_Type              : Factor w/ 4 levels "Grocery Store",..: 2 3 2 1 2 3 2 4 2 2 ...
    ##  $ Item_Outlet_Sales        : num  3735 443 2097 732 995 ...

Checking for missing values

``` r
table(is.na(train))
```

    ## 
    ##  FALSE   TRUE 
    ## 100813   1463

There are 1463 missing values in the train data set. Lets see which variables have missing values.

``` r
colSums(is.na(train))
```

    ##           Item_Identifier               Item_Weight 
    ##                         0                      1463 
    ##          Item_Fat_Content           Item_Visibility 
    ##                         0                         0 
    ##                 Item_Type                  Item_MRP 
    ##                         0                         0 
    ##         Outlet_Identifier Outlet_Establishment_Year 
    ##                         0                         0 
    ##               Outlet_Size      Outlet_Location_Type 
    ##                         0                         0 
    ##               Outlet_Type         Item_Outlet_Sales 
    ##                         0                         0

Observing the distribution of variables in dataset.

``` r
summary(train)
```

    ##  Item_Identifier  Item_Weight     Item_Fat_Content Item_Visibility  
    ##  FDG33  :  10    Min.   : 4.555   LF     : 316     Min.   :0.00000  
    ##  FDW13  :  10    1st Qu.: 8.774   low fat: 112     1st Qu.:0.02699  
    ##  DRE49  :   9    Median :12.600   Low Fat:5089     Median :0.05393  
    ##  DRN47  :   9    Mean   :12.858   reg    : 117     Mean   :0.06613  
    ##  FDD38  :   9    3rd Qu.:16.850   Regular:2889     3rd Qu.:0.09459  
    ##  FDF52  :   9    Max.   :21.350                    Max.   :0.32839  
    ##  (Other):8467    NA's   :1463                                       
    ##                  Item_Type       Item_MRP      Outlet_Identifier
    ##  Fruits and Vegetables:1232   Min.   : 31.29   OUT027 : 935     
    ##  Snack Foods          :1200   1st Qu.: 93.83   OUT013 : 932     
    ##  Household            : 910   Median :143.01   OUT035 : 930     
    ##  Frozen Foods         : 856   Mean   :140.99   OUT046 : 930     
    ##  Dairy                : 682   3rd Qu.:185.64   OUT049 : 930     
    ##  Canned               : 649   Max.   :266.89   OUT045 : 929     
    ##  (Other)              :2994                    (Other):2937     
    ##  Outlet_Establishment_Year Outlet_Size   Outlet_Location_Type
    ##  Min.   :1985                    :2410   Tier 1:2388         
    ##  1st Qu.:1987              High  : 932   Tier 2:2785         
    ##  Median :1999              Medium:2793   Tier 3:3350         
    ##  Mean   :1998              Small :2388                       
    ##  3rd Qu.:2004                                                
    ##  Max.   :2009                                                
    ##                                                              
    ##             Outlet_Type   Item_Outlet_Sales 
    ##  Grocery Store    :1083   Min.   :   33.29  
    ##  Supermarket Type1:5577   1st Qu.:  834.25  
    ##  Supermarket Type2: 928   Median : 1794.33  
    ##  Supermarket Type3: 935   Mean   : 2181.29  
    ##                           3rd Qu.: 3101.30  
    ##                           Max.   :13086.97  
    ## 

Important Observations: The Item fat content variables seems to have mismatched levels. Also Outlet Size has a level with no value.

``` r
dim(train)
```

    ## [1] 8523   12

``` r
dim(test)
```

    ## [1] 5681   11

Test dataset has 1 less column which is that of value to predicted.

Adding Item Outlet Sales column in test dataset.

``` r
test$Item_Outlet_Sales <- NA
```

Data Exploration using Graphs
=============================

``` r
library(ggplot2)

 ggplot(data = train,aes(x = Outlet_Identifier,y = Item_Outlet_Sales)) + geom_boxplot() +scale_y_continuous(breaks = seq(0,15000,2000)) + theme(axis.text.x = element_text(angle = 70,vjust = 0.5)) +xlab("Outlet Identifier") + ylab("Sale of Item in Outlet") +ggtitle("Item sales according to Outlets")
```

![](Big_Mart_Sales_files/figure-markdown_github/unnamed-chunk-8-1.png)

Conclusion: As can be seen from above box plot, Outlet 27 contributes to most sales while Outlet 10 and Outlet 19 contribute the least.

``` r
ggplot(data = train,aes(x = Item_Type,y = Item_Outlet_Sales)) + geom_bar(stat = "identity") + theme(axis.text.x = element_text(angle = 70,vjust = 0.5)) +xlab("Item Type") +ylab("Sales")
```

![](Big_Mart_Sales_files/figure-markdown_github/unnamed-chunk-9-1.png)

Conclusion: Fruits,Vegetables and Snacks are sold most in these outlets while sale of seafood and breakfast items is very less

``` r
ggplot(data = train,aes(x = Outlet_Establishment_Year,y = Item_Outlet_Sales)) + geom_histogram(stat = "identity")  + xlab("Year Of Establishment") + ylab("Sales") 
```

![](Big_Mart_Sales_files/figure-markdown_github/unnamed-chunk-10-1.png)

On the first glance it seems that the sales of outlet established in 1985 has most sales. But on closer observation we can notice that there are only 9 bars and we have 10 distinct outlets. This must mean two outlets launched in same year. Let us see which are those outlets.

``` r
library(plyr)
library(dplyr)
train %>% group_by(Outlet_Identifier,Outlet_Establishment_Year) %>% summarise(TotalSales = sum(Item_Outlet_Sales)) %>% arrange(Outlet_Establishment_Year)
```

    ## Source: local data frame [10 x 3]
    ## Groups: Outlet_Identifier [10]
    ## 
    ##    Outlet_Identifier Outlet_Establishment_Year TotalSales
    ##               <fctr>                     <int>      <dbl>
    ## 1             OUT019                      1985   179694.1
    ## 2             OUT027                      1985  3453926.1
    ## 3             OUT013                      1987  2142663.6
    ## 4             OUT046                      1997  2118395.2
    ## 5             OUT010                      1998   188340.2
    ## 6             OUT049                      1999  2183969.8
    ## 7             OUT045                      2002  2036725.5
    ## 8             OUT035                      2004  2268122.9
    ## 9             OUT017                      2007  2167465.3
    ## 10            OUT018                      2009  1851822.8

As it can be seen from above table, Outlet 19 and Outlet 27 were both established in 1985. Despite this,the sales of Outlet 27 is the most. On the other hand outlet 19 which also launched in 1985 has least sales.

Conclusion: Year of establishment does not have significant impact on sales

``` r
 ggplot(train,aes(x = Outlet_Type,y = Item_Outlet_Sales)) + geom_boxplot() + xlab("Outlet Type") + ylab("Item Sales")
```

![](Big_Mart_Sales_files/figure-markdown_github/unnamed-chunk-12-1.png)

We can conclude that customers tend to buy at supermarkets rather than at grocery store. This could be due to the fact that they can buy other items besides foods at supermarket.

Checking distribution of predictor variables

``` r
ggplot(train,aes(x = Item_Outlet_Sales)) + geom_histogram(colour = "Black",fill = "Green")
```

![](Big_Mart_Sales_files/figure-markdown_github/unnamed-chunk-13-1.png)

Our predictor variable is heavily skewed and would require transformation in later stages.

Data Manipulation
=================

Combining the datasets for manipulating data.

``` r
combined <- rbind(train,test)
str(combined)
```

    ## 'data.frame':    14204 obs. of  12 variables:
    ##  $ Item_Identifier          : Factor w/ 1559 levels "DRA12","DRA24",..: 157 9 663 1122 1298 759 697 739 441 991 ...
    ##  $ Item_Weight              : num  9.3 5.92 17.5 19.2 8.93 ...
    ##  $ Item_Fat_Content         : Factor w/ 5 levels "LF","low fat",..: 3 5 3 5 3 5 5 3 5 5 ...
    ##  $ Item_Visibility          : num  0.016 0.0193 0.0168 0 0 ...
    ##  $ Item_Type                : Factor w/ 16 levels "Baking Goods",..: 5 15 11 7 10 1 14 14 6 6 ...
    ##  $ Item_MRP                 : num  249.8 48.3 141.6 182.1 53.9 ...
    ##  $ Outlet_Identifier        : Factor w/ 10 levels "OUT010","OUT013",..: 10 4 10 1 2 4 2 6 8 3 ...
    ##  $ Outlet_Establishment_Year: int  1999 2009 1999 1998 1987 2009 1987 1985 2002 2007 ...
    ##  $ Outlet_Size              : Factor w/ 4 levels "","High","Medium",..: 3 3 3 1 2 3 2 3 1 1 ...
    ##  $ Outlet_Location_Type     : Factor w/ 3 levels "Tier 1","Tier 2",..: 1 3 1 3 3 3 3 3 2 2 ...
    ##  $ Outlet_Type              : Factor w/ 4 levels "Grocery Store",..: 2 3 2 1 2 3 2 4 2 2 ...
    ##  $ Item_Outlet_Sales        : num  3735 443 2097 732 995 ...

### Imputation of missing values in Item Weight

As we noted earlier there were 1463 missing values in Item Weight. On inspecting the dataset we can clearly see that items with same item identifier have equal weights. That makes sense as same items even though sold in different outlet would have equal weights. Lets impute the missing values in Item Weight.

``` r
combined <- ddply(combined,~Item_Identifier,transform,Item_Weight = ifelse(is.na(Item_Weight), median(Item_Weight,na.rm = TRUE),Item_Weight))

table(is.na(combined$Item_Weight))
```

    ## 
    ## FALSE 
    ## 14204

### Revalue empty level in Outlet Size

Outlet size has a level which is empty. We categorize it as Other.

``` r
levels(combined$Outlet_Size)[1] <- "Other"
```

### Correction of mismatched levels in Fat Content

Fat content has similar levels for same fat content. We need to categorize them into Low Fat and Regular.

``` r
combined$Item_Fat_Content <- mapvalues(combined$Item_Fat_Content,from = c("LF","reg"),to = c("Low Fat","Regular"))
combined$Item_Fat_Content <- mapvalues(combined$Item_Fat_Content,from = c("low fat"),to = c("Low Fat"))
```

### Imputation of NAs Item Visibility

Item visibility is 0 for some of the items. If the item is present in an outlet it has to be visible. Therefore we will consider the items with 0 visbility to have missing values and replace them with median of item visibility of same item.

``` r
combined$Item_Visibility[combined$Item_Visibility == 0] <- NA

combined <- ddply(combined,~Item_Identifier,transform,Item_Visibility = 
                    ifelse(is.na(Item_Visibility),median(Item_Visibility,TRUE),Item_Visibility))
```

### Subdiving item into Item Category

On close observation of Item Identifier it seems all items begin with DR,FD or NC. Checking these items under Item Type the must mean Drinks,Food and Non Consumables respectively Lets make a new variable: Item Category

``` r
q <- substr(combined$Item_Identifier,1,2)
q <- gsub("DR","Drinks",q) 
q <- gsub("FD","Food",q)
q <- gsub("NC","Non Consumables",q)

combined$Item_Category <- q
combined$Item_Category <- factor(combined$Item_Category,levels = c("Drinks","Food","Non Consumables"))
```

### Addition of a level to Fat Content

As we categorized items into different categories,we noticed that some items were not consumables. Hence they should not have fat content either low or regular. Replacing fat content of non consumable items by Non Edible

``` r
combined$Item_Fat_Content <- as.character(combined$Item_Fat_Content)

combined$Item_Fat_Content <- ifelse(combined$Item_Category == "Non Consumables","Non Edible",combined$Item_Fat_Content)

combined$Item_Fat_Content <- as.factor(combined$Item_Fat_Content)
```

Lets see how fat content affects sales.

``` r
ggplot(data = combined,aes(x = Item_Fat_Content,y = Item_Outlet_Sales)) + geom_histogram(stat = "identity") + 
  
  xlab("Fat Content") + ylab("Item Outlet Sale")
```

![](Big_Mart_Sales_files/figure-markdown_github/unnamed-chunk-21-1.png)

Compared to regular fat content, Items with low fat content had higher sales. It seems that customers prefer low fat items.

### New Variable: Outlet Age

This data was collected in 2013 and therefore we can find how long the outlet has been running if we subtract establishment year from 2013

``` r
combined$Outlet_Age <- 2013 - combined$Outlet_Establishment_Year

combined <- select(combined,-c(Outlet_Establishment_Year))
```

### Splitting the dataset

``` r
new_train <- combined[!is.na(combined$Item_Outlet_Sales),]
new_test <- combined[is.na(combined$Item_Outlet_Sales),]
```

Using Linear Regression Model
=============================

``` r
lm1 <- lm(data = new_train, Item_Outlet_Sales ~ Item_Weight + Item_Fat_Content + Item_Visibility + Item_Type + Item_MRP  + Outlet_Size + Outlet_Location_Type + Outlet_Type + Item_Category + Outlet_Age)
summary(lm1)
```

    ## 
    ## Call:
    ## lm(formula = Item_Outlet_Sales ~ Item_Weight + Item_Fat_Content + 
    ##     Item_Visibility + Item_Type + Item_MRP + Outlet_Size + Outlet_Location_Type + 
    ##     Outlet_Type + Item_Category + Outlet_Age, data = new_train)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -4343.7  -677.5   -90.2   569.1  7948.5 
    ## 
    ## Coefficients: (2 not defined because of singularities)
    ##                                 Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)                    -947.5035   321.7147  -2.945 0.003237 ** 
    ## Item_Weight                       0.0740     2.6566   0.028 0.977780    
    ## Item_Fat_ContentNon Edible      -67.9542   145.0887  -0.468 0.639537    
    ## Item_Fat_ContentRegular          41.9874    28.4664   1.475 0.140254    
    ## Item_Visibility                -184.6695   257.4129  -0.717 0.473144    
    ## Item_TypeBreads                   5.7292    84.0684   0.068 0.945669    
    ## Item_TypeBreakfast                4.4792   116.5930   0.038 0.969356    
    ## Item_TypeCanned                  25.0314    62.7768   0.399 0.690098    
    ## Item_TypeDairy                  -51.5578    66.0445  -0.781 0.435029    
    ## Item_TypeFrozen Foods           -28.3203    58.8778  -0.481 0.630528    
    ## Item_TypeFruits and Vegetables   29.1600    54.9747   0.530 0.595831    
    ## Item_TypeHard Drinks            -45.6760   139.6650  -0.327 0.743646    
    ## Item_TypeHealth and Hygiene      12.0315   100.0480   0.120 0.904282    
    ## Item_TypeHousehold              -17.8081    94.6821  -0.188 0.850816    
    ## Item_TypeMeat                    -0.5163    70.6833  -0.007 0.994172    
    ## Item_TypeOthers                       NA         NA      NA       NA    
    ## Item_TypeSeafood                184.0418   148.0179   1.243 0.213764    
    ## Item_TypeSnack Foods            -11.8818    55.2671  -0.215 0.829782    
    ## Item_TypeSoft Drinks            -73.6586   128.0447  -0.575 0.565133    
    ## Item_TypeStarchy Foods           20.6225   103.0691   0.200 0.841419    
    ## Item_MRP                         15.5698     0.1981  78.576  < 2e-16 ***
    ## Outlet_SizeHigh                 848.4056   256.3252   3.310 0.000937 ***
    ## Outlet_SizeMedium               171.0220    71.0863   2.406 0.016157 *  
    ## Outlet_SizeSmall                143.9395    45.6745   3.151 0.001630 ** 
    ## Outlet_Location_TypeTier 2     -100.1933    90.2418  -1.110 0.266912    
    ## Outlet_Location_TypeTier 3     -325.8327   154.9061  -2.103 0.035458 *  
    ## Outlet_TypeSupermarket Type1   1476.7054   140.6846  10.497  < 2e-16 ***
    ## Outlet_TypeSupermarket Type2   1079.9637   136.0498   7.938 2.32e-15 ***
    ## Outlet_TypeSupermarket Type3   3642.4722   178.1471  20.446  < 2e-16 ***
    ## Item_CategoryFood               -46.8513   108.2743  -0.433 0.665237    
    ## Item_CategoryNon Consumables          NA         NA      NA       NA    
    ## Outlet_Age                      -34.8001    10.4934  -3.316 0.000916 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1129 on 8493 degrees of freedom
    ## Multiple R-squared:  0.5637, Adjusted R-squared:  0.5622 
    ## F-statistic: 378.4 on 29 and 8493 DF,  p-value: < 2.2e-16

We get adjusted R-squared of 0.5628. This mean only 56% of variations can be explained by the data.

### Linear Model of log transformed data

One assumption of Linear Regression is that predictor variable is normalized. But our predictor variable Item Outlet Sales is heavily skewed to one side. Therefore we shall use log transform of sales in formula for linear regression.

``` r
lm2 <- lm(log(Item_Outlet_Sales) ~  Item_Weight + Item_Fat_Content + Item_Visibility + Item_Type + Item_MRP  + Outlet_Size + Outlet_Location_Type + Outlet_Type + Item_Category + Outlet_Age ,data = new_train)
summary(lm2)
```

    ## 
    ## Call:
    ## lm(formula = log(Item_Outlet_Sales) ~ Item_Weight + Item_Fat_Content + 
    ##     Item_Visibility + Item_Type + Item_MRP + Outlet_Size + Outlet_Location_Type + 
    ##     Outlet_Type + Item_Category + Outlet_Age, data = new_train)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -2.30385 -0.29408  0.06772  0.37852  1.36418 
    ## 
    ## Coefficients: (2 not defined because of singularities)
    ##                                  Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)                     4.783e+00  1.532e-01  31.210  < 2e-16 ***
    ## Item_Weight                    -1.404e-04  1.265e-03  -0.111  0.91163    
    ## Item_Fat_ContentNon Edible     -2.567e-02  6.911e-02  -0.371  0.71032    
    ## Item_Fat_ContentRegular         1.447e-02  1.356e-02   1.067  0.28579    
    ## Item_Visibility                 1.039e-02  1.226e-01   0.085  0.93249    
    ## Item_TypeBreads                 2.849e-02  4.004e-02   0.712  0.47677    
    ## Item_TypeBreakfast             -7.021e-02  5.553e-02  -1.264  0.20617    
    ## Item_TypeCanned                 2.536e-02  2.990e-02   0.848  0.39632    
    ## Item_TypeDairy                 -7.523e-02  3.146e-02  -2.391  0.01681 *  
    ## Item_TypeFrozen Foods          -5.422e-02  2.804e-02  -1.933  0.05323 .  
    ## Item_TypeFruits and Vegetables -4.837e-03  2.618e-02  -0.185  0.85346    
    ## Item_TypeHard Drinks           -4.991e-02  6.652e-02  -0.750  0.45310    
    ## Item_TypeHealth and Hygiene     9.134e-03  4.765e-02   0.192  0.84800    
    ## Item_TypeHousehold             -2.875e-02  4.510e-02  -0.637  0.52382    
    ## Item_TypeMeat                   2.254e-02  3.367e-02   0.669  0.50329    
    ## Item_TypeOthers                        NA         NA      NA       NA    
    ## Item_TypeSeafood                5.343e-03  7.050e-02   0.076  0.93959    
    ## Item_TypeSnack Foods           -1.452e-03  2.632e-02  -0.055  0.95601    
    ## Item_TypeSoft Drinks           -5.046e-02  6.099e-02  -0.827  0.40801    
    ## Item_TypeStarchy Foods         -4.783e-02  4.909e-02  -0.974  0.32990    
    ## Item_MRP                        8.319e-03  9.438e-05  88.146  < 2e-16 ***
    ## Outlet_SizeHigh                 3.546e-01  1.221e-01   2.904  0.00369 ** 
    ## Outlet_SizeMedium               8.226e-02  3.386e-02   2.429  0.01514 *  
    ## Outlet_SizeSmall                6.743e-02  2.176e-02   3.100  0.00194 ** 
    ## Outlet_Location_TypeTier 2     -4.200e-02  4.298e-02  -0.977  0.32855    
    ## Outlet_Location_TypeTier 3     -1.564e-01  7.378e-02  -2.120  0.03402 *  
    ## Outlet_TypeSupermarket Type1    1.746e+00  6.701e-02  26.051  < 2e-16 ***
    ## Outlet_TypeSupermarket Type2    1.541e+00  6.480e-02  23.786  < 2e-16 ***
    ## Outlet_TypeSupermarket Type3    2.609e+00  8.485e-02  30.751  < 2e-16 ***
    ## Item_CategoryFood              -2.863e-02  5.157e-02  -0.555  0.57887    
    ## Item_CategoryNon Consumables           NA         NA      NA       NA    
    ## Outlet_Age                     -1.495e-02  4.998e-03  -2.991  0.00279 ** 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.5378 on 8493 degrees of freedom
    ## Multiple R-squared:  0.7214, Adjusted R-squared:  0.7204 
    ## F-statistic: 758.3 on 29 and 8493 DF,  p-value: < 2.2e-16

Thus we can see that our model has greatly improved and about 72% of variations in data can be explained.
Also we get NAs in ItemType Others and Item_Category Non Consumables row. This is because there is strong correlation between them due to the fact that we have categorised Non Consumables as Others in Item Type. 



### Linear Model of Important Variables

To make our model more robust, we shall only select the most important variables. For this we shall omit all variables who have p value more than 0.05.


``` r
lm3 <- lm(data = new_train, log(Item_Outlet_Sales) ~  Item_MRP  + Outlet_Size + Outlet_Location_Type + Outlet_Type  + Outlet_Age)

summary(lm3)
```

    ## 
    ## Call:
    ## lm(formula = log(Item_Outlet_Sales) ~ Item_MRP + Outlet_Size + 
    ##     Outlet_Location_Type + Outlet_Type + Outlet_Age, data = new_train)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -2.29735 -0.29340  0.06762  0.38066  1.35449 
    ## 
    ## Coefficients:
    ##                                Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)                   4.7361757  0.1415879  33.450  < 2e-16 ***
    ## Item_MRP                      0.0083006  0.0000936  88.681  < 2e-16 ***
    ## Outlet_SizeHigh               0.3451588  0.1220655   2.828  0.00470 ** 
    ## Outlet_SizeMedium             0.0834449  0.0338571   2.465  0.01374 *  
    ## Outlet_SizeSmall              0.0675278  0.0217602   3.103  0.00192 ** 
    ## Outlet_Location_TypeTier 2   -0.0380719  0.0429650  -0.886  0.37558    
    ## Outlet_Location_TypeTier 3   -0.1513457  0.0737650  -2.052  0.04023 *  
    ## Outlet_TypeSupermarket Type1  1.7491969  0.0667464  26.207  < 2e-16 ***
    ## Outlet_TypeSupermarket Type2  1.5449997  0.0646121  23.912  < 2e-16 ***
    ## Outlet_TypeSupermarket Type3  2.6038719  0.0846973  30.743  < 2e-16 ***
    ## Outlet_Age                   -0.0145374  0.0049968  -2.909  0.00363 ** 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.538 on 8512 degrees of freedom
    ## Multiple R-squared:  0.7205, Adjusted R-squared:  0.7202 
    ## F-statistic:  2194 on 10 and 8512 DF,  p-value: < 2.2e-16

We see that out Adjusted R-squared value is almost same as our second model when only important varibales were included.

### Predicting the sales for test dataset

Now we can use our model to predict values of sales for test dataset.

``` r
prediction <- predict(lm3, newdata = new_test)

sub_file <- data.frame(new_test$Item_Identifier, new_test$Outlet_Identifier, exp(prediction))

names(sub_file) <- c("Item_Identifier","Outlet_Identifier","Item_Outlet_Sales")

write.csv(sub_file, file="Predicted_Test.csv", row.names=FALSE, quote = FALSE)
```

Model performance was evaluated on the basis of prediction of the sales for the test data and the calculated root mean square error(RMSE) was found out to be 1225.

