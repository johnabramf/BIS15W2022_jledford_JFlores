---
title: "Tidyr 1: Tidy data and `pivot_long()`"
date: "2022-02-02"
output:
  html_document: 
    theme: spacelab
    toc: yes
    toc_float: yes
    keep_md: yes
  pdf_document:
    toc: yes
---

> **For Big-Data Scientists, "Janitor Work" Is Key Hurdle to Insights.**  
> "Data scientists, according to interviews and expert estimates, spend from 50 percent to 80 percent of their time mired in the mundane labor of collecting and preparing data, before it can be explored for useful information."  
> [New York Times (2014)](http://www.nytimes.com/2014/08/18/technology/for-big-data-scientists-hurdle-to-insights-is-janitor-work.html)

## Learning Goals
*At the end of this exercise, you will be able to:*    
1. Explain the difference between tidy and messy data.  
2. Evaluate a data set as tidy or messy.    
3. Use the `pivot_longer()` function of `tidyr` to transform data from wide to long format.  
4. Use `separate()` to split observations within a column.  

## Overview
The quote above sums up much of the work in data science. Simply put, most of the data that you end up working with will be messy, disorganized, and not **tidy**. By the end of this week, you will have the tools necessary to wrangle messy data into tidy data that are ready for analysis. I know that we have spent a lot of time of data transformation, but this last step is essential to building plots and performing other analyses.  

## Resources
- [tidyr `pivot_longer()`](https://tidyr.tidyverse.org/reference/pivot_longer.html)  
- [pivoting](https://cran.r-project.org/web/packages/tidyr/vignettes/pivot.html)  

## Load the tidyverse

```r
library(tidyverse)
library(here)
```

## Tidy data
Most "wild" data that you will encounter are organized incorrectly for work in R and, as you might expect, the tools used to transform data are a core part of the tidyverse. I will attempt to summarize the most important points below, but you should read [chapter 12 of the data science text](https://r4ds.had.co.nz/tidy-data.html) for a more thorough explanation.  

`Tidy` data in the sense of the tidyverse follows three conventions:   
(1) each variable has its own column  
(2) each observation has its own row  
(3) each value has its own cell  

This is summarized in the image below. The package used to tidy data is called **tidyr** and is a core part of the tidyverse.  
![*Tidy Data.*](tidy-1.png)

## `pivot_longer()`  
Scientists frequently use spreadsheets that are organized to make data entry efficient. This is often referred to as **wide format**. Unfortunately, the wide format creates a problem because column names may actually represent values of a variable. The command `pivot_longer()` shifts data from wide to long format.   

Rules:  
+ `pivot_longer`(cols, names_to, values_to)
+ `cols` - Columns to pivot to longer format
+ `names_to` - Name of the new column; it will contain the column names of gathered columns as values
+ `values_to` - Name of the new column; it will contain the data stored in the values of gathered columns

## Example 1: column names are data
The following data show results from a drug trial with four treatments on six patients. The values represent resting heart rate.  

```r
heartrate <- readr::read_csv("data/heartrate.csv")
```

```
## Rows: 6 Columns: 5
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr (1): patient
## dbl (4): a, b, c, d
```

```
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
heartrate
```

```
## # A tibble: 6 x 5
##   patient      a     b     c     d
##   <chr>    <dbl> <dbl> <dbl> <dbl>
## 1 Margaret    72    74    80    68
## 2 Frank       84    84    88    76
## 3 Hawkeye     64    66    68    64
## 4 Trapper     60    58    64    58
## 5 Radar       74    72    78    70
## 6 Henry       88    87    88    72
```

Want to try the `here` package?

```r
readr::read_csv(here("lab8", "data", "heartrate.csv"))
```

```
## Rows: 6 Columns: 5
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr (1): patient
## dbl (4): a, b, c, d
```

```
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```
## # A tibble: 6 x 5
##   patient      a     b     c     d
##   <chr>    <dbl> <dbl> <dbl> <dbl>
## 1 Margaret    72    74    80    68
## 2 Frank       84    84    88    76
## 3 Hawkeye     64    66    68    64
## 4 Trapper     60    58    64    58
## 5 Radar       74    72    78    70
## 6 Henry       88    87    88    72
```

Let's assess whether or not these data are tidy.  
(1) each variable has its own column  
*No. Some of the column names are actually variables (treatment a, b, c, or d).*  
(2) each observation has its own row  
*No. The observations are grouped in a single row by patient.*  
(3) each value has its own cell  
*Yes. There are no unusual combinations of data in each cell.*    

To fix this problem, we need to reshape the table to long format while keeping track of column names and values. We do this using `pivot_longer()`. Notice that the dimensions of the data frame change.  

```r
heartrate %>% 
  pivot_longer(-patient, #patient does not pivot
               names_to = "drug", 
               values_to = "heartrate"
               )
```

```
## # A tibble: 24 x 3
##    patient  drug  heartrate
##    <chr>    <chr>     <dbl>
##  1 Margaret a            72
##  2 Margaret b            74
##  3 Margaret c            80
##  4 Margaret d            68
##  5 Frank    a            84
##  6 Frank    b            84
##  7 Frank    c            88
##  8 Frank    d            76
##  9 Hawkeye  a            64
## 10 Hawkeye  b            66
## # ... with 14 more rows
```

## Practice
1. Import the file `relig_income.csv` and store it as a new object `relig_income`.  

```r
relig_income <- readr::read_csv("data/relig_income.csv")
```

```
## Rows: 18 Columns: 11
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr  (1): religion
## dbl (10): <$10k, $10-20k, $20-30k, $30-40k, $40-50k, $50-75k, $75-100k, $100...
```

```
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
relig_income
```

```
## # A tibble: 18 x 11
##    religion `<$10k` `$10-20k` `$20-30k` `$30-40k` `$40-50k` `$50-75k` `$75-100k`
##    <chr>      <dbl>     <dbl>     <dbl>     <dbl>     <dbl>     <dbl>      <dbl>
##  1 Agnostic      27        34        60        81        76       137        122
##  2 Atheist       12        27        37        52        35        70         73
##  3 Buddhist      27        21        30        34        33        58         62
##  4 Catholic     418       617       732       670       638      1116        949
##  5 Don’t k~      15        14        15        11        10        35         21
##  6 Evangel~     575       869      1064       982       881      1486        949
##  7 Hindu          1         9         7         9        11        34         47
##  8 Histori~     228       244       236       238       197       223        131
##  9 Jehovah~      20        27        24        24        21        30         15
## 10 Jewish        19        19        25        25        30        95         69
## 11 Mainlin~     289       495       619       655       651      1107        939
## 12 Mormon        29        40        48        51        56       112         85
## 13 Muslim         6         7         9        10         9        23         16
## 14 Orthodox      13        17        23        32        32        47         38
## 15 Other C~       9         7        11        13        13        14         18
## 16 Other F~      20        33        40        46        49        63         46
## 17 Other W~       5         2         3         4         2         7          3
## 18 Unaffil~     217       299       374       365       341       528        407
## # ... with 3 more variables: $100-150k <dbl>, >150k <dbl>,
## #   Don't know/refused <dbl>
```
2. Why are these data untidy?  

**These data are untidy because the variable of income is split into each own variable. In addition, each observation does NOT have its own row.**

3. Use `pivot_longer()` to make the data tidy.  

```r
relig_income %>%
  pivot_longer(-religion,
               names_to = "income",
               values_to = "entries")
```

```
## # A tibble: 180 x 3
##    religion income             entries
##    <chr>    <chr>                <dbl>
##  1 Agnostic <$10k                   27
##  2 Agnostic $10-20k                 34
##  3 Agnostic $20-30k                 60
##  4 Agnostic $30-40k                 81
##  5 Agnostic $40-50k                 76
##  6 Agnostic $50-75k                137
##  7 Agnostic $75-100k               122
##  8 Agnostic $100-150k              109
##  9 Agnostic >150k                   84
## 10 Agnostic Don't know/refused      96
## # ... with 170 more rows
```

## Example 2: some column names are data
Some (but not all) of the column names are data. We also have NA's.

```r
billboard <- readr::read_csv("data/billboard.csv")
```

```
## Rows: 317 Columns: 79
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr   (2): artist, track
## dbl  (65): wk1, wk2, wk3, wk4, wk5, wk6, wk7, wk8, wk9, wk10, wk11, wk12, wk...
## lgl  (11): wk66, wk67, wk68, wk69, wk70, wk71, wk72, wk73, wk74, wk75, wk76
## date  (1): date.entered
```

```
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
billboard
```

```
## # A tibble: 317 x 79
##    artist   track   date.entered   wk1   wk2   wk3   wk4   wk5   wk6   wk7   wk8
##    <chr>    <chr>   <date>       <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
##  1 2 Pac    Baby D~ 2000-02-26      87    82    72    77    87    94    99    NA
##  2 2Ge+her  The Ha~ 2000-09-02      91    87    92    NA    NA    NA    NA    NA
##  3 3 Doors~ Krypto~ 2000-04-08      81    70    68    67    66    57    54    53
##  4 3 Doors~ Loser   2000-10-21      76    76    72    69    67    65    55    59
##  5 504 Boyz Wobble~ 2000-04-15      57    34    25    17    17    31    36    49
##  6 98^0     Give M~ 2000-08-19      51    39    34    26    26    19     2     2
##  7 A*Teens  Dancin~ 2000-07-08      97    97    96    95   100    NA    NA    NA
##  8 Aaliyah  I Don'~ 2000-01-29      84    62    51    41    38    35    35    38
##  9 Aaliyah  Try Ag~ 2000-03-18      59    53    38    28    21    18    16    14
## 10 Adams, ~ Open M~ 2000-08-26      76    76    74    69    68    67    61    58
## # ... with 307 more rows, and 68 more variables: wk9 <dbl>, wk10 <dbl>,
## #   wk11 <dbl>, wk12 <dbl>, wk13 <dbl>, wk14 <dbl>, wk15 <dbl>, wk16 <dbl>,
## #   wk17 <dbl>, wk18 <dbl>, wk19 <dbl>, wk20 <dbl>, wk21 <dbl>, wk22 <dbl>,
## #   wk23 <dbl>, wk24 <dbl>, wk25 <dbl>, wk26 <dbl>, wk27 <dbl>, wk28 <dbl>,
## #   wk29 <dbl>, wk30 <dbl>, wk31 <dbl>, wk32 <dbl>, wk33 <dbl>, wk34 <dbl>,
## #   wk35 <dbl>, wk36 <dbl>, wk37 <dbl>, wk38 <dbl>, wk39 <dbl>, wk40 <dbl>,
## #   wk41 <dbl>, wk42 <dbl>, wk43 <dbl>, wk44 <dbl>, wk45 <dbl>, wk46 <dbl>, ...
```

Solution 1: specify a range of columns that you want to pivot.

```r
billboard2 <- 
  billboard %>% 
  pivot_longer(wk1:wk76, # a range of columns
               names_to = "week",
               values_to = "rank",
               values_drop_na = TRUE #this will drop the NA's
               )
billboard2
```

```
## # A tibble: 5,307 x 5
##    artist  track                   date.entered week   rank
##    <chr>   <chr>                   <date>       <chr> <dbl>
##  1 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk1      87
##  2 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk2      82
##  3 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk3      72
##  4 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk4      77
##  5 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk5      87
##  6 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk6      94
##  7 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk7      99
##  8 2Ge+her The Hardest Part Of ... 2000-09-02   wk1      91
##  9 2Ge+her The Hardest Part Of ... 2000-09-02   wk2      87
## 10 2Ge+her The Hardest Part Of ... 2000-09-02   wk3      92
## # ... with 5,297 more rows
```

Solution 2: OR, specify columns that you want to stay fixed.

```r
billboard3 <- 
  billboard %>% 
  pivot_longer(-c(artist, track, date.entered), #specific columns
               names_to = "week",
               values_to = "rank",
               values_drop_na = TRUE
               )
billboard3
```

```
## # A tibble: 5,307 x 5
##    artist  track                   date.entered week   rank
##    <chr>   <chr>                   <date>       <chr> <dbl>
##  1 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk1      87
##  2 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk2      82
##  3 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk3      72
##  4 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk4      77
##  5 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk5      87
##  6 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk6      94
##  7 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk7      99
##  8 2Ge+her The Hardest Part Of ... 2000-09-02   wk1      91
##  9 2Ge+her The Hardest Part Of ... 2000-09-02   wk2      87
## 10 2Ge+her The Hardest Part Of ... 2000-09-02   wk3      92
## # ... with 5,297 more rows
```

Solution 3: identify columns by a prefix, remove the prefix and all NA's.

```r
billboard %>% 
   pivot_longer(
   cols = starts_with("wk"),
   names_to = "week",
   names_prefix = "wk",
   values_to = "rank",
   values_drop_na = TRUE)
```

```
## # A tibble: 5,307 x 5
##    artist  track                   date.entered week   rank
##    <chr>   <chr>                   <date>       <chr> <dbl>
##  1 2 Pac   Baby Don't Cry (Keep... 2000-02-26   1        87
##  2 2 Pac   Baby Don't Cry (Keep... 2000-02-26   2        82
##  3 2 Pac   Baby Don't Cry (Keep... 2000-02-26   3        72
##  4 2 Pac   Baby Don't Cry (Keep... 2000-02-26   4        77
##  5 2 Pac   Baby Don't Cry (Keep... 2000-02-26   5        87
##  6 2 Pac   Baby Don't Cry (Keep... 2000-02-26   6        94
##  7 2 Pac   Baby Don't Cry (Keep... 2000-02-26   7        99
##  8 2Ge+her The Hardest Part Of ... 2000-09-02   1        91
##  9 2Ge+her The Hardest Part Of ... 2000-09-02   2        87
## 10 2Ge+her The Hardest Part Of ... 2000-09-02   3        92
## # ... with 5,297 more rows
```

## Practice  
1. Import `plant_data.csv` as a new object `plant_data`.  

```r
plant_data <- readr::read_csv("data/plant_data.csv")
```

```
## Rows: 3 Columns: 33
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr  (3): genotype, water_sched_prog, greenhouse
## dbl (30): day1, day2, day3, day4, day5, day6, day7, day8, day9, day10, day11...
```

```
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
plant_data
```

```
## # A tibble: 3 x 33
##   genotype water_sched_prog greenhouse  day1  day2  day3  day4  day5  day6  day7
##   <chr>    <chr>            <chr>      <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
## 1 control  A                A761        21.7  19.9  20.7  19.4  20.2  19.2  20.6
## 2 mutant1  A                A761        24.3  24.9  25.2  25.3  27.4  25.5  25.4
## 3 mutant2  A                A761        20.7  21.3  21.3  22.5  19.8  21.4  21.9
## # ... with 23 more variables: day8 <dbl>, day9 <dbl>, day10 <dbl>, day11 <dbl>,
## #   day12 <dbl>, day13 <dbl>, day14 <dbl>, day15 <dbl>, day16 <dbl>,
## #   day17 <dbl>, day18 <dbl>, day19 <dbl>, day20 <dbl>, day21 <dbl>,
## #   day22 <dbl>, day23 <dbl>, day24 <dbl>, day25 <dbl>, day26 <dbl>,
## #   day27 <dbl>, day28 <dbl>, day29 <dbl>, day30 <dbl>
```

2. Why are these data not tidy?  

**These plant data are not tidy because each variable has its values as column names (day1, day2, .etc). **

3. Use `pivot_longer()` to make the data tidy. Focus the data only on genotype, day, and measurement.  

```r
plant_data %>%
  pivot_longer(day1:day30,
               names_to = "day",
               values_to = "water")
```

```
## # A tibble: 90 x 5
##    genotype water_sched_prog greenhouse day   water
##    <chr>    <chr>            <chr>      <chr> <dbl>
##  1 control  A                A761       day1   21.7
##  2 control  A                A761       day2   19.9
##  3 control  A                A761       day3   20.7
##  4 control  A                A761       day4   19.4
##  5 control  A                A761       day5   20.2
##  6 control  A                A761       day6   19.2
##  7 control  A                A761       day7   20.6
##  8 control  A                A761       day8   19.9
##  9 control  A                A761       day9   19.2
## 10 control  A                A761       day10  20.4
## # ... with 80 more rows
```

## Example 3: more than one variable in a column name
In this case, there are two observations in each column name.

```r
qpcr_untidy <- read_csv("data/qpcr_untidy.csv")
```

```
## Rows: 5 Columns: 10
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr (1): gene
## dbl (9): exp1_rep1, exp1_rep2, exp1_rep3, exp2_rep1, exp2_rep2, exp2_rep3, e...
```

```
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
qpcr_untidy
```

```
## # A tibble: 5 x 10
##   gene  exp1_rep1 exp1_rep2 exp1_rep3 exp2_rep1 exp2_rep2 exp2_rep3 exp3_rep1
##   <chr>     <dbl>     <dbl>     <dbl>     <dbl>     <dbl>     <dbl>     <dbl>
## 1 A          21.7      19.8      20.7      18.3      20.4      17.6      20.6
## 2 B          24.3      24.8      25.2      26.0      29.9      26.4      25.4
## 3 C          20.7      21.5      21.3      25.5      18.7      22.3      21.9
## 4 D          26.9      28.0      27.7      33.1      24.3      28.9      28.5
## 5 E          25.0      22.7      23.8      21.1      23.4      20.2      23.7
## # ... with 2 more variables: exp3_rep2 <dbl>, exp3_rep3 <dbl>
```

`names_sep` helps pull these apart, but we still have "exp" and "rep" to deal with.  

```r
qpcr_untidy %>% 
  pivot_longer(
    exp1_rep1:exp3_rep3,
    names_to= c("experiment", "replicate"),
    names_sep="_",
    values_to="mRNA_expression"
  )
```

```
## # A tibble: 45 x 4
##    gene  experiment replicate mRNA_expression
##    <chr> <chr>      <chr>               <dbl>
##  1 A     exp1       rep1                 21.7
##  2 A     exp1       rep2                 19.8
##  3 A     exp1       rep3                 20.7
##  4 A     exp2       rep1                 18.3
##  5 A     exp2       rep2                 20.4
##  6 A     exp2       rep3                 17.6
##  7 A     exp3       rep1                 20.6
##  8 A     exp3       rep2                 19.9
##  9 A     exp3       rep3                 19.2
## 10 B     exp1       rep1                 24.3
## # ... with 35 more rows
```

## Example 4: more than one value or observation in a row
This is often caused by an entry error, but R will not be able to work with it unless the values are separated. This doesn't use `pivot_longer()` but is a common problem.  

```r
length_data <- readr::read_csv("data/length_data.csv")
```

```
## Rows: 5 Columns: 2
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr (2): sample, length
```

```
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
length_data
```

```
## # A tibble: 5 x 2
##   sample length  
##   <chr>  <chr>   
## 1 A      34      
## 2 B      23;45;68
## 3 C      <NA>    
## 4 D      32;29;61
## 5 E      41
```


```r
length_data %>% 
  transform(length = str_split(length, ";")) %>%
  unnest(length)
```

```
## # A tibble: 9 x 2
##   sample length
##   <chr>  <chr> 
## 1 A      34    
## 2 B      23    
## 3 B      45    
## 4 B      68    
## 5 C      <NA>  
## 6 D      32    
## 7 D      29    
## 8 D      61    
## 9 E      41
```

## That's it! Take a break and I will see you on Zoom!  

-->[Home](https://jmledford3115.github.io/datascibiol/)
