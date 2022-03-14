---
title: "Lab 8 Homework"
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
```

## Install `here`
The package `here` is a nice option for keeping directories clear when loading files. I will demonstrate below and let you decide if it is something you want to use.  

## Data
For this homework, we will use a data set compiled by the Office of Environment and Heritage in New South Whales, Australia. It contains the enterococci counts in water samples obtained from Sydney beaches as part of the Beachwatch Water Quality Program. Enterococci are bacteria common in the intestines of mammals; they are rarely present in clean water. So, enterococci values are a measurement of pollution. `cfu` stands for colony forming units and measures the number of viable bacteria in a sample [cfu](https://en.wikipedia.org/wiki/Colony-forming_unit).   

This homework loosely follows the tutorial of [R Ladies Sydney](https://rladiessydney.org/). If you get stuck, check it out!  

1. Start by loading the data `sydneybeaches`. Do some exploratory analysis to get an idea of the data structure.

```r
sydneybeaches <- readr::read_csv("data/sydneybeaches.csv")
```

```
## Rows: 3690 Columns: 8
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (4): Region, Council, Site, Date
## dbl (4): BeachId, Longitude, Latitude, Enterococci (cfu/100ml)
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

If you want to try `here`, first notice the output when you load the `here` library. It gives you information on the current working directory. You can then use it to easily and intuitively load files.

```r
library(here)
```

```
## here() starts at /Users/rajesvismac/Desktop/BIS15
```

The quotes show the folder structure from the root directory.

```r
read_csv(here("lab8", "data", "sydneybeaches.csv")) %>% janitor::clean_names()
```

```
## Rows: 3690 Columns: 8
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (4): Region, Council, Site, Date
## dbl (4): BeachId, Longitude, Latitude, Enterococci (cfu/100ml)
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```
## # A tibble: 3,690 × 8
##    beach_id region       council site  longitude latitude date  enterococci_cfu…
##       <dbl> <chr>        <chr>   <chr>     <dbl>    <dbl> <chr>            <dbl>
##  1       25 Sydney City… Randwi… Clov…      151.    -33.9 02/0…               19
##  2       25 Sydney City… Randwi… Clov…      151.    -33.9 06/0…                3
##  3       25 Sydney City… Randwi… Clov…      151.    -33.9 12/0…                2
##  4       25 Sydney City… Randwi… Clov…      151.    -33.9 18/0…               13
##  5       25 Sydney City… Randwi… Clov…      151.    -33.9 30/0…                8
##  6       25 Sydney City… Randwi… Clov…      151.    -33.9 05/0…                7
##  7       25 Sydney City… Randwi… Clov…      151.    -33.9 11/0…               11
##  8       25 Sydney City… Randwi… Clov…      151.    -33.9 23/0…               97
##  9       25 Sydney City… Randwi… Clov…      151.    -33.9 07/0…                3
## 10       25 Sydney City… Randwi… Clov…      151.    -33.9 25/0…                0
## # … with 3,680 more rows
```

2. Are these data "tidy" per the definitions of the tidyverse? How do you know? Are they in wide or long format?

These data are tidy as every observation has its own row, and each variable does have its own column. Additionally, each data does have its own cell. The data is in wide format. 

```r
names(sydneybeaches)
```

```
## [1] "BeachId"                 "Region"                 
## [3] "Council"                 "Site"                   
## [5] "Longitude"               "Latitude"               
## [7] "Date"                    "Enterococci (cfu/100ml)"
```

3. We are only interested in the variables site, date, and enterococci_cfu_100ml. Make a new object focused on these variables only. Name the object `sydneybeaches_long`

4. Pivot the data such that the dates are column names and each beach only appears once. Name the object `sydneybeaches_wide`

```r
sydneybeaches2 <-
sydneybeaches  %>%
  pivot_longer(Date)
sydneybeaches2
```

```
## # A tibble: 3,690 × 9
##    BeachId Region  Council Site  Longitude Latitude `Enterococci (…` name  value
##      <dbl> <chr>   <chr>   <chr>     <dbl>    <dbl>            <dbl> <chr> <chr>
##  1      25 Sydney… Randwi… Clov…      151.    -33.9               19 Date  02/0…
##  2      25 Sydney… Randwi… Clov…      151.    -33.9                3 Date  06/0…
##  3      25 Sydney… Randwi… Clov…      151.    -33.9                2 Date  12/0…
##  4      25 Sydney… Randwi… Clov…      151.    -33.9               13 Date  18/0…
##  5      25 Sydney… Randwi… Clov…      151.    -33.9                8 Date  30/0…
##  6      25 Sydney… Randwi… Clov…      151.    -33.9                7 Date  05/0…
##  7      25 Sydney… Randwi… Clov…      151.    -33.9               11 Date  11/0…
##  8      25 Sydney… Randwi… Clov…      151.    -33.9               97 Date  23/0…
##  9      25 Sydney… Randwi… Clov…      151.    -33.9                3 Date  07/0…
## 10      25 Sydney… Randwi… Clov…      151.    -33.9                0 Date  25/0…
## # … with 3,680 more rows
```

5. Pivot the data back so that the dates are data and not column names.

```r
sydneybeaches
```

```
## # A tibble: 3,690 × 8
##    BeachId Region        Council Site  Longitude Latitude Date  `Enterococci (…`
##      <dbl> <chr>         <chr>   <chr>     <dbl>    <dbl> <chr>            <dbl>
##  1      25 Sydney City … Randwi… Clov…      151.    -33.9 02/0…               19
##  2      25 Sydney City … Randwi… Clov…      151.    -33.9 06/0…                3
##  3      25 Sydney City … Randwi… Clov…      151.    -33.9 12/0…                2
##  4      25 Sydney City … Randwi… Clov…      151.    -33.9 18/0…               13
##  5      25 Sydney City … Randwi… Clov…      151.    -33.9 30/0…                8
##  6      25 Sydney City … Randwi… Clov…      151.    -33.9 05/0…                7
##  7      25 Sydney City … Randwi… Clov…      151.    -33.9 11/0…               11
##  8      25 Sydney City … Randwi… Clov…      151.    -33.9 23/0…               97
##  9      25 Sydney City … Randwi… Clov…      151.    -33.9 07/0…                3
## 10      25 Sydney City … Randwi… Clov…      151.    -33.9 25/0…                0
## # … with 3,680 more rows
```

6. We haven't dealt much with dates yet, but separate the date into columns day, month, and year. Do this on the `sydneybeaches_long` data.

```r
sydneybeaches3 <- sydneybeaches %>% 
  separate(Date, into= c("day", "month", "year"), sep = "/")
sydneybeaches3
```

```
## # A tibble: 3,690 × 10
##    BeachId Region             Council Site  Longitude Latitude day   month year 
##      <dbl> <chr>              <chr>   <chr>     <dbl>    <dbl> <chr> <chr> <chr>
##  1      25 Sydney City Ocean… Randwi… Clov…      151.    -33.9 02    01    2013 
##  2      25 Sydney City Ocean… Randwi… Clov…      151.    -33.9 06    01    2013 
##  3      25 Sydney City Ocean… Randwi… Clov…      151.    -33.9 12    01    2013 
##  4      25 Sydney City Ocean… Randwi… Clov…      151.    -33.9 18    01    2013 
##  5      25 Sydney City Ocean… Randwi… Clov…      151.    -33.9 30    01    2013 
##  6      25 Sydney City Ocean… Randwi… Clov…      151.    -33.9 05    02    2013 
##  7      25 Sydney City Ocean… Randwi… Clov…      151.    -33.9 11    02    2013 
##  8      25 Sydney City Ocean… Randwi… Clov…      151.    -33.9 23    02    2013 
##  9      25 Sydney City Ocean… Randwi… Clov…      151.    -33.9 07    03    2013 
## 10      25 Sydney City Ocean… Randwi… Clov…      151.    -33.9 25    03    2013 
## # … with 3,680 more rows, and 1 more variable: `Enterococci (cfu/100ml)` <dbl>
```


7. What is the average `enterococci_cfu_100ml` by year for each beach. Think about which data you will use- long or wide.

```r
sydneybeaches4 <-
sydneybeaches3  %>%
  pivot_longer( year)
sydneybeaches4
```

```
## # A tibble: 3,690 × 11
##    BeachId Region  Council Site  Longitude Latitude day   month `Enterococci (…`
##      <dbl> <chr>   <chr>   <chr>     <dbl>    <dbl> <chr> <chr>            <dbl>
##  1      25 Sydney… Randwi… Clov…      151.    -33.9 02    01                  19
##  2      25 Sydney… Randwi… Clov…      151.    -33.9 06    01                   3
##  3      25 Sydney… Randwi… Clov…      151.    -33.9 12    01                   2
##  4      25 Sydney… Randwi… Clov…      151.    -33.9 18    01                  13
##  5      25 Sydney… Randwi… Clov…      151.    -33.9 30    01                   8
##  6      25 Sydney… Randwi… Clov…      151.    -33.9 05    02                   7
##  7      25 Sydney… Randwi… Clov…      151.    -33.9 11    02                  11
##  8      25 Sydney… Randwi… Clov…      151.    -33.9 23    02                  97
##  9      25 Sydney… Randwi… Clov…      151.    -33.9 07    03                   3
## 10      25 Sydney… Randwi… Clov…      151.    -33.9 25    03                   0
## # … with 3,680 more rows, and 2 more variables: name <chr>, value <chr>
```


8. Make the output from question 7 easier to read by pivoting it to wide format.


9. What was the most polluted beach in 2018?


```r
sydneybeaches3 %>%
  select(BeachId)
```

```
## # A tibble: 3,690 × 1
##    BeachId
##      <dbl>
##  1      25
##  2      25
##  3      25
##  4      25
##  5      25
##  6      25
##  7      25
##  8      25
##  9      25
## 10      25
## # … with 3,680 more rows
```

10. Please complete the class project survey at: [BIS 15L Group Project](https://forms.gle/H2j69Z3ZtbLH3efW6)


## Push your final code to GitHub!
Please be sure that you check the `keep md` file in the knit preferences.   
