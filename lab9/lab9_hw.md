---
title: "Lab 9 Homework"
author: "Please Add Your Name Here"
date: "2022-03-14"
output:
  html_document: 
    theme: spacelab
    keep_md: yes
---



## Instructions
Answer the following questions and complete the exercises in RMarkdown. Please embed all of your code and push your final work to your repository. Your final lab report should be organized, clean, and run free from errors. Remember, you must remove the `#` for the included code chunks to run. Be sure to add your name to the author header above.  

Make sure to use the formatting conventions of RMarkdown to make your report neat and clean!  

## Load the libraries

```r
library(tidyverse)
library(janitor)
library(here)
library(naniar)
```

For this homework, we will take a departure from biological data and use data about California colleges. These data are a subset of the national college scorecard (https://collegescorecard.ed.gov/data/). Load the `ca_college_data.csv` as a new object called `colleges`.

```r
colleges <- read_csv("data/ca_college_data.csv")
```

```
## Rows: 341 Columns: 10
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (4): INSTNM, CITY, STABBR, ZIP
## dbl (6): ADM_RATE, SAT_AVG, PCIP26, COSTT4_A, C150_4_POOLED, PFTFTUG1_EF
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

The variables are a bit hard to decipher, here is a key:  

INSTNM: Institution name  
CITY: California city  
STABBR: Location state  
ZIP: Zip code  
ADM_RATE: Admission rate  
SAT_AVG: SAT average score  
PCIP26: Percentage of degrees awarded in Biological And Biomedical Sciences  
COSTT4_A: Annual cost of attendance  
C150_4_POOLED: 4-year completion rate  
PFTFTUG1_EF: Percentage of undergraduate students who are first-time, full-time degree/certificate-seeking undergraduate students  

1. Use your preferred function(s) to have a look at the data and get an idea of its structure. Make sure you summarize NA's and determine whether or not the data are tidy. You may also consider dealing with any naming issues.

```r
colleges %>% 
  summarize(number_nas = sum(is.na(colleges)))
```

```
## # A tibble: 1 × 1
##   number_nas
##        <int>
## 1        949
```


```r
colleges %>% 
  summarise_all(~(sum(is.na(.))))
```

```
## # A tibble: 1 × 10
##   INSTNM  CITY STABBR   ZIP ADM_RATE SAT_AVG PCIP26 COSTT4_A C150_4_POOLED
##    <int> <int>  <int> <int>    <int>   <int>  <int>    <int>         <int>
## 1      0     0      0     0      240     276     35      124           221
## # … with 1 more variable: PFTFTUG1_EF <int>
```


```r
colleges %>% 
  na_if("-999")
```

```
## # A tibble: 341 × 10
##    INSTNM      CITY  STABBR ZIP   ADM_RATE SAT_AVG PCIP26 COSTT4_A C150_4_POOLED
##    <chr>       <chr> <chr>  <chr>    <dbl>   <dbl>  <dbl>    <dbl>         <dbl>
##  1 Grossmont … El C… CA     9202…       NA      NA 0.0016     7956        NA    
##  2 College of… Visa… CA     9327…       NA      NA 0.0066     8109        NA    
##  3 College of… San … CA     9440…       NA      NA 0.0038     8278        NA    
##  4 Ventura Co… Vent… CA     9300…       NA      NA 0.0035     8407        NA    
##  5 Oxnard Col… Oxna… CA     9303…       NA      NA 0.0085     8516        NA    
##  6 Moorpark C… Moor… CA     9302…       NA      NA 0.0151     8577        NA    
##  7 Skyline Co… San … CA     9406…       NA      NA 0          8580         0.233
##  8 Glendale C… Glen… CA     9120…       NA      NA 0.002      9181        NA    
##  9 Citrus Col… Glen… CA     9174…       NA      NA 0.0021     9281        NA    
## 10 Fresno Cit… Fres… CA     93741       NA      NA 0.0324     9370        NA    
## # … with 331 more rows, and 1 more variable: PFTFTUG1_EF <dbl>
```

```r
names(colleges)
```

```
##  [1] "INSTNM"        "CITY"          "STABBR"        "ZIP"          
##  [5] "ADM_RATE"      "SAT_AVG"       "PCIP26"        "COSTT4_A"     
##  [9] "C150_4_POOLED" "PFTFTUG1_EF"
```

2. Which cities in California have the highest number of colleges?

```r
colleges %>%
  count(CITY) 
```

```
## # A tibble: 161 × 2
##    CITY            n
##    <chr>       <int>
##  1 Alameda         3
##  2 Anaheim         4
##  3 Angwin          1
##  4 Aptos           1
##  5 Arcata          1
##  6 Atherton        1
##  7 Azusa           1
##  8 Bakersfield     3
##  9 Barstow         1
## 10 Belmont         1
## # … with 151 more rows
```

3. Based on your answer to #2, make a plot that shows the number of colleges in the top 10 cities.

```r
colleges %>% 
  ggplot(aes(x=CITY)) + 
  geom_bar()
```

![](lab9_hw_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

4. The column `COSTT4_A` is the annual cost of each institution. Which city has the highest average cost? Where is it located?

```r
colleges %>% 
  count(COSTT4_A) %>%
  arrange(COSTT4_A)
```

```
## # A tibble: 218 × 2
##    COSTT4_A     n
##       <dbl> <int>
##  1     7956     1
##  2     8109     1
##  3     8278     1
##  4     8407     1
##  5     8516     1
##  6     8577     1
##  7     8580     1
##  8     9181     1
##  9     9281     1
## 10     9370     1
## # … with 208 more rows
```

```r
colleges %>% 
  ggplot(aes(x=COSTT4_A)) + 
  geom_bar()
```

```
## Warning: Removed 124 rows containing non-finite values (stat_count).
```

![](lab9_hw_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

5. Based on your answer to #4, make a plot that compares the cost of the individual colleges in the most expensive city. Bonus! Add UC Davis here to see how it compares :>).

```r
colleges %>% 
  select(CITY, COSTT4_A) %>% 
  ggplot(aes(x=CITY, y=COSTT4_A))+
  geom_col()
```

```
## Warning: Removed 124 rows containing missing values (position_stack).
```

![](lab9_hw_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

6. The column `ADM_RATE` is the admissions rate by college and `C150_4_POOLED` is the four-year completion rate. Use a scatterplot to show the relationship between these two variables. What do you think this means?

```r
ggplot(data = colleges, mapping = aes(x = ADM_RATE, y = C150_4_POOLED)) +
  geom_point()+
  geom_smooth(method=lm, se=F)
```

```
## `geom_smooth()` using formula 'y ~ x'
```

```
## Warning: Removed 251 rows containing non-finite values (stat_smooth).
```

```
## Warning: Removed 251 rows containing missing values (geom_point).
```

![](lab9_hw_files/figure-html/unnamed-chunk-12-1.png)<!-- -->
This shows that the lower thw admission rate, the more likely that the students are to complete the 4 year course. 

7. Is there a relationship between cost and four-year completion rate? (You don't need to do the stats, just produce a plot). What do you think this means?

```r
ggplot(data = colleges, mapping = aes(x = COSTT4_A, y = C150_4_POOLED)) +
  geom_point()+
  geom_smooth(method=lm, se=F)
```

```
## `geom_smooth()` using formula 'y ~ x'
```

```
## Warning: Removed 225 rows containing non-finite values (stat_smooth).
```

```
## Warning: Removed 225 rows containing missing values (geom_point).
```

![](lab9_hw_files/figure-html/unnamed-chunk-13-1.png)<!-- -->
Ths plot is all over the place, but the best fit line has an increasing slope, which means that the higher the cost, the more porbability of completing the course. 


```r
names(colleges)
```

```
##  [1] "INSTNM"        "CITY"          "STABBR"        "ZIP"          
##  [5] "ADM_RATE"      "SAT_AVG"       "PCIP26"        "COSTT4_A"     
##  [9] "C150_4_POOLED" "PFTFTUG1_EF"
```

8. The column titled `INSTNM` is the institution name. We are only interested in the University of California colleges. Make a new data frame that is restricted to UC institutions. You can remove `Hastings College of Law` and `UC San Francisco` as we are only interested in undergraduate institutions.

```r
colleges2 <- colleges %>% 
  group_by(STABBR, INSTNM) %>%
  filter(STABBR=="CA")
colleges2
```

```
## # A tibble: 339 × 10
## # Groups:   STABBR, INSTNM [339]
##    INSTNM      CITY  STABBR ZIP   ADM_RATE SAT_AVG PCIP26 COSTT4_A C150_4_POOLED
##    <chr>       <chr> <chr>  <chr>    <dbl>   <dbl>  <dbl>    <dbl>         <dbl>
##  1 Grossmont … El C… CA     9202…       NA      NA 0.0016     7956        NA    
##  2 College of… Visa… CA     9327…       NA      NA 0.0066     8109        NA    
##  3 College of… San … CA     9440…       NA      NA 0.0038     8278        NA    
##  4 Ventura Co… Vent… CA     9300…       NA      NA 0.0035     8407        NA    
##  5 Oxnard Col… Oxna… CA     9303…       NA      NA 0.0085     8516        NA    
##  6 Moorpark C… Moor… CA     9302…       NA      NA 0.0151     8577        NA    
##  7 Skyline Co… San … CA     9406…       NA      NA 0          8580         0.233
##  8 Glendale C… Glen… CA     9120…       NA      NA 0.002      9181        NA    
##  9 Citrus Col… Glen… CA     9174…       NA      NA 0.0021     9281        NA    
## 10 Fresno Cit… Fres… CA     93741       NA      NA 0.0324     9370        NA    
## # … with 329 more rows, and 1 more variable: PFTFTUG1_EF <dbl>
```

Remove `Hastings College of Law` and `UC San Francisco` and store the final data frame as a new object `univ_calif_final`.

```r
univ_calif_final <- colleges2 %>% 
  filter(INSTNM !="Hastings College of Law" |INSTNM !="University of California - San Francisco" )
```

Use `separate()` to separate institution name into two new columns "UNIV" and "CAMPUS".

```r
colleges2 %>%
  separate(INSTNM, c("UNIV", "CAMPUS"), sep = "-")
```

```
## Warning: Expected 2 pieces. Additional pieces discarded in 9 rows [139, 144,
## 164, 172, 176, 291, 297, 298, 299].
```

```
## Warning: Expected 2 pieces. Missing pieces filled with `NA` in 262 rows [1, 2,
## 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, ...].
```

```
## # A tibble: 339 × 11
## # Groups:   STABBR [1]
##    UNIV               CAMPUS CITY  STABBR ZIP   ADM_RATE SAT_AVG PCIP26 COSTT4_A
##    <chr>              <chr>  <chr> <chr>  <chr>    <dbl>   <dbl>  <dbl>    <dbl>
##  1 Grossmont College  <NA>   El C… CA     9202…       NA      NA 0.0016     7956
##  2 College of the Se… <NA>   Visa… CA     9327…       NA      NA 0.0066     8109
##  3 College of San Ma… <NA>   San … CA     9440…       NA      NA 0.0038     8278
##  4 Ventura College    <NA>   Vent… CA     9300…       NA      NA 0.0035     8407
##  5 Oxnard College     <NA>   Oxna… CA     9303…       NA      NA 0.0085     8516
##  6 Moorpark College   <NA>   Moor… CA     9302…       NA      NA 0.0151     8577
##  7 Skyline College    <NA>   San … CA     9406…       NA      NA 0          8580
##  8 Glendale Communit… <NA>   Glen… CA     9120…       NA      NA 0.002      9181
##  9 Citrus College     <NA>   Glen… CA     9174…       NA      NA 0.0021     9281
## 10 Fresno City Colle… <NA>   Fres… CA     93741       NA      NA 0.0324     9370
## # … with 329 more rows, and 2 more variables: C150_4_POOLED <dbl>,
## #   PFTFTUG1_EF <dbl>
```

9. The column `ADM_RATE` is the admissions rate by campus. Which UC has the lowest and highest admissions rates? Produce a numerical summary and an appropriate plot.

```r
univ_calif_final %>%
  arrange(desc(ADM_RATE))
```

```
## # A tibble: 339 × 10
## # Groups:   STABBR, INSTNM [339]
##    INSTNM      CITY  STABBR ZIP   ADM_RATE SAT_AVG PCIP26 COSTT4_A C150_4_POOLED
##    <chr>       <chr> <chr>  <chr>    <dbl>   <dbl>  <dbl>    <dbl>         <dbl>
##  1 California… Fres… CA     93727    1          NA  0        13869         0.176
##  2 Shasta Bib… Redd… CA     96002    1          NA  0        22005         0.426
##  3 North-West… West… CA     91790    1          NA  0           NA        NA    
##  4 Concorde C… San … CA     9211…    1          NA  0           NA        NA    
##  5 Newschool … San … CA     92101    0.991      NA  0        44971         0.326
##  6 Life Pacif… San … CA     9177…    0.973     889  0        24838         0.427
##  7 Notre Dame… Belm… CA     9400…    0.967     913  0.135    50415         0.467
##  8 Otis Colle… Los … CA     9004…    0.930      NA  0        56519         0.657
##  9 San Franci… San … CA     9413…    0.926      NA  0        65453         0.388
## 10 Concorde C… Gard… CA     9284…    0.905      NA  0           NA        NA    
## # … with 329 more rows, and 1 more variable: PFTFTUG1_EF <dbl>
```


```r
univ_calif_final %>%
  arrange((ADM_RATE))
```

```
## # A tibble: 339 × 10
## # Groups:   STABBR, INSTNM [339]
##    INSTNM      CITY  STABBR ZIP   ADM_RATE SAT_AVG PCIP26 COSTT4_A C150_4_POOLED
##    <chr>       <chr> <chr>  <chr>    <dbl>   <dbl>  <dbl>    <dbl>         <dbl>
##  1 California… Pasa… CA     91125   0.0807    1555 0.0723    63471         0.922
##  2 Pomona Col… Clar… CA     9171…   0.0944    1442 0.171     64870         0.957
##  3 Claremont … Clar… CA     9171…   0.0944    1413 0.0681    66325         0.924
##  4 Harvey Mud… Clar… CA     91711   0.129     1496 0.0674    69355         0.925
##  5 Pitzer Col… Clar… CA     9171…   0.137       NA 0.0888    65880         0.888
##  6 University… Los … CA     90089   0.166     1395 0.0461    67064         0.922
##  7 University… Berk… CA     94720   0.169     1422 0.105     34924         0.916
##  8 University… Los … CA     9009…   0.180     1334 0.155     33078         0.911
##  9 California… Vale… CA     9135…   0.236       NA 0         64686         0.611
## 10 Charles R … Los … CA     90059   0.25        NA 0            NA         0.8  
## # … with 329 more rows, and 1 more variable: PFTFTUG1_EF <dbl>
```

10. If you wanted to get a degree in biological or biomedical sciences, which campus confers the majority of these degrees? Produce a numerical summary and an appropriate plot.




## Knit Your Output and Post to [GitHub](https://github.com/FRS417-DataScienceBiologists)
