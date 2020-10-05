# Catalog_CrossSelling
Rules of Association Model 
21.8 for background on the CatalogCrossSell.csv  file 



### explore data
catalog.df <- read.csv("CatalogCrossSell.csv") # Load Data
summary(catalog.df)
str(catalog.df)
dim(catalog.df)
head(catalog.df)
tail(catalog.df)
is.na(catalog.df)
catalog.df <- (catalog.df[-c(1,11:20)])
catalog.df <- na.omit(catalog.df)
summary(catalog.df)
head(catalog.df)
tail(catalog.df)
is.na(catalog.df)
str(catalog.df)
#To prepare the dat

Analyze your data by creating and interpreting association rules.

If item from the automotive division, personal electronics division, novelty gift division, and jewelry division then with 82% confidence an item from the garden division will also be purchased. This rule has a lift ratio of 3 and a support of 24%. 
If an item from the health products division, the automotive division, personal electronics division, novelty gift division, and jewelry division, then with 81% confidence an item from the garden division will be bought. This rule has a a lift of 3 and a support of 24%. 
If an item is purchased from the housewares division, the automotive division, personal electronics division, the garden division, and the jewelry division, then with 66% confidence an item from the novelty gift division will also be purchased. This rule has a lift of 2.90 and a support of 17%. 


 inspect(head(sort(rules, by = "lift"), n = 6))
    lhs                                rhs                  support confidence     lift count
[1] {Automotive.Division,                                                                    
     Personal.Electronics.Division,                                                          
     Novelty.Gift.Division,                                                                  
     Jewelry.Division}              => {Garden.Division} 0.02400960  0.8163265 3.000000   120
[2] {Health.Products.Division,                                                               
     Automotive.Division,                                                                    
     Personal.Electronics.Division,                                                          
     Novelty.Gift.Division,                                                                  
     Jewelry.Division}              => {Garden.Division} 0.02400960  0.8163265 3.000000   120
[3] {Automotive.Division,                                                                    
     Novelty.Gift.Division,                                                                  
     Jewelry.Division}              => {Garden.Division} 0.02781112  0.8128655 2.987281   139
[4] {Health.Products.Division,                                                               
     Automotive.Division,                                                                    
     Novelty.Gift.Division,                                                                  
     Jewelry.Division}              => {Garden.Division} 0.02781112  0.8128655 2.987281   139
[5] {Automotive.Division,                                                                    
     Personal.Electronics.Division,                                                          
     Novelty.Gift.Division}         => {Garden.Division} 0.03541417  0.7972973 2.930068   177
[6] {Health.Products.Division,                                                               
     Automotive.Division,                                                                    
     Personal.Electronics.Division,                                                          
     Novelty.Gift.Division}         => {Garden.Division} 0.03541417  0.7972973 2.930068   177

Include appropriate visualizations in your analysis.


This R markdown file demonstrates how to catalog cross sell by building an association rules model using the arules and arulesviz package. The dataset used for this analysis is the CatalogCrossSell.csv file obtained from course materials.  


I will follow the first 5 steps of the **CRISP-DM** model steps of

1. Understand business
2. Understand data (explore data)
3. Prep data for modeling
4. Build model
5. Evaluate model
6. Deploy model

## 1. Understand the business

Exter sells products from different catalogs. Exter owns various catalogs but has nine main categories. To maximize profits Exter would like to know what catalogs are most likely to be purchased together. Knowing this would allow Exter to make informed decisions on how carry out their cross promotion strategies. 



## 2 Understand the data

The data is available from course materials or under module 8 Portfolio Project.

The CatalogCrossSell.csv file includes a list of transactions from the various catalogs owned by Exter Inc. The dataset is in binary incidence matrix format. The format displayed shows columns as the catalogs owned by Exter. There are over 20 columns but only 9 main catalogs are displayed. The nine main catalogs are clothing, housewares, health products, automotive, personal electronics, computers, garden, novelty gift, and jewelry. The rows in the dataset represent transactions and there are ones and zero’s which represent the purchase or non purchase of an item from a specific division.  

```{r}
catalog.df <- read.csv("CatalogCrossSell.csv") # Load Data
summary(catalog.df)
str(catalog.df)
dim(catalog.df)
head(catalog.df)
tail(catalog.df)
```
### 3 Prep Data for modeling

The goal of this model is to develop and interpret rules of association, as well as to include proper visualizations of the rules developed. To accomplish this I will need the arules package and the aruluesViz package.Next, I will convert the data frame structure into a matrix structure and convert the matrix structure into transaction database format. When creating the matrix structure I will exclude columns 1, 11 to 20, and all values which are not 1’s or 0’s. 

```{r}
library(arules)
library(arulesViz)
catalog.mat <- as.matrix(na.omit(catalog.df[-c(1,11:20)]))
head(catalog.mat)
tail(catalog.mat)
catalog.trans <- as(catalog.mat, "transactions")
inspect(head(catalog.trans))



### 4 Build a Model
Next, I will build the association rules model. I will use the apriori function from the arules package. I will use a minimum support of 0.01, a minimum condidence of 0.1 and 0.5 in the apriori() function. Once the model runs I will inspect the first six generated rules and sort them by their lift. To support my findings, I will also create 
```{r}
rules <- apriori(catalog.trans, parameter = list(supp = 0.01, conf = 0.5, target = "rules"))

rules1 <- apriori(catalog.trans, parameter = list(supp = 0.01, conf = 0.1, target = "rules"))

inspect(head(sort(rules, by = "lift"), n = 6))
inspect(head(sort(rules1, by = "lift"), n = 6))
```
### Evaluate the Model

To evaluate the model I will use the arulesVis package for plotting. I will plot a graph for the first 10 rules. A parallel coordinate graph for the top 10 rules, as well as two different forms of matrix plots. 
```{r}
plot(rules[1:10],
     method = "graph",
     control = list(type = “items"))
plot(rules[1:11],
     method = "paracoord",
     control = list(reorder = TRUE))
plot(rules[1:20],
     method = “matrix")
plot(rules[1:20],
           method = “grouped”)
