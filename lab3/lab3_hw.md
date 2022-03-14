---
title: "Lab 3 Homework"
author: "rajesvi"
date: "2022-03-14"
output:
  html_document: 
    theme: spacelab
    keep_md: yes
---

## Instructions
Answer the following questions and complete the exercises in RMarkdown. Please embed all of your code and push your final work to your repository. Your final lab report should be organized, clean, and run free from errors. Remember, you must remove the `#` for the included code chunks to run. Be sure to add your name to the author header above.  

Make sure to use the formatting conventions of RMarkdown to make your report neat and clean!  

## Load the tidyverse

```r
library(tidyverse)
```

## Mammals Sleep
1. For this assignment, we are going to use built-in data on mammal sleep patterns. From which publication are these data taken from? Since the data are built-in you can use the help function in R.

```r
?import
```

2. Store these data into a new data frame `sleep`.

3. What are the dimensions of this data frame (variables and observations)? How do you know? Please show the *code* that you used to determine this below.  

```r
dim(sleep)
```

```
## [1] 20  3
```

4. Are there any NAs in the data? How did you determine this? Please show your code.  

```r
summary(sleep)
```

```
##      extra        group        ID   
##  Min.   :-1.600   1:10   1      :2  
##  1st Qu.:-0.025   2:10   2      :2  
##  Median : 0.950          3      :2  
##  Mean   : 1.540          4      :2  
##  3rd Qu.: 3.400          5      :2  
##  Max.   : 5.500          6      :2  
##                          (Other):8
```

5. Show a list of the column names is this data frame.

```r
names(sleep)
```

```
## [1] "extra" "group" "ID"
```

6. How many herbivores are represented in the data?  

```r
glimpse(sleep)
```

```
## Rows: 20
## Columns: 3
## $ extra <dbl> 0.7, -1.6, -0.2, -1.2, -0.1, 3.4, 3.7, 0.8, 0.0, 2.0, 1.9, 0.8, …
## $ group <fct> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2
## $ ID    <fct> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
```

7. We are interested in two groups; small and large mammals. Let's define small as less than or equal to 1kg body weight and large as greater than or equal to 200kg body weight. Make two new dataframes (large and small) based on these parameters.

```r
small <- subset(sleep, 'body weight in kg'<=1)
small
```

```
## [1] extra group ID   
## <0 rows> (or 0-length row.names)
```

```r
large <- subset(sleep, 'body weight in kg'>=200)
large
```

```
##    extra group ID
## 1    0.7     1  1
## 2   -1.6     1  2
## 3   -0.2     1  3
## 4   -1.2     1  4
## 5   -0.1     1  5
## 6    3.4     1  6
## 7    3.7     1  7
## 8    0.8     1  8
## 9    0.0     1  9
## 10   2.0     1 10
## 11   1.9     2  1
## 12   0.8     2  2
## 13   1.1     2  3
## 14   0.1     2  4
## 15  -0.1     2  5
## 16   4.4     2  6
## 17   5.5     2  7
## 18   1.6     2  8
## 19   4.6     2  9
## 20   3.4     2 10
```

8. What is the mean weight for both the small and large mammals?


```r
mean(subset)
```

```
## Warning in mean.default(subset): argument is not numeric or logical: returning
## NA
```

```
## [1] NA
```

9. Using a similar approach as above, do large or small animals sleep longer on average?  




10. Which animal is the sleepiest among the entire dataframe?




## Push your final code to GitHub!
Please be sure that you check the `keep md` file in the knit preferences.   
