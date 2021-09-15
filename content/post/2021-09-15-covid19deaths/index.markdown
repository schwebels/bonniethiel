---
title: Covid19 Deaths
author: Bonnie Thiel
date: '2021-09-15'
slug: covid19deaths
categories: []
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2021-09-15T17:48:59-04:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---



# Strategy: 
  - Demonstrate how to get data from an internet source 
  - do some data wrangling and simple statistical analysis 
  - plot results


Let's calculate some statistics about deaths from Covid-19 based on CDC data:
<https://data.cdc.gov/NCHS/Provisional-COVID-19-Deaths-by-Sex-and-Age/9bhg-hcku>

## step 1:
On the CDC page click on the EXPORT button on the top right of the page to download the csv file called 'Provisional_COVID-19_Death_Counts_by_Sex__Age.csv'
download it to your desktop or another folder and copy the path to the clipboard

## step 2:
Open R studio and click on File -> New File -> Rscript 

You will need some packages so either use the "install.packages()" function, i.e. install.packages(c("tidyverse", "magrittr"))

**or** the easier way is to go to the right-side window of Rstudio where it shows tabs for Files-Plots-Packages-Help-Viewer, and click on Packages. 
Underneath there is a button for "Install". Click install and type in the names of the packages: "magrittr" , "tidyverse". These only have to be installed once until there is an update.

## step 3:
Load the libraries: this has to be done every time the script is run

**magrittr** is for piping - explained later but just so you know the %>% in R is called a pipe. You know the picture by Rene Magritte? of the pipe? 
**tidyverse** contains many functions for data wrangling


```r
library(magrittr) 
library(tidyverse) 
```

```
## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --
```

```
## v ggplot2 3.3.2     v purrr   0.3.4
## v tibble  3.0.4     v dplyr   1.0.2
## v tidyr   1.1.2     v stringr 1.4.0
## v readr   1.4.0     v forcats 0.5.0
```

```
## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
## x tidyr::extract()   masks magrittr::extract()
## x dplyr::filter()    masks stats::filter()
## x dplyr::lag()       masks stats::lag()
## x purrr::set_names() masks magrittr::set_names()
```

## step 4 
Load the dataset using the read.csv() or read.table() function

*NOTE:* the path to your file needs forward slashes / for R not back slashes like windows uses
Copy the path from the clipboard in the read.table() function so that R knows where to find your data

read.csv() is the same function but assumes that the file is comma delimited
str() is a very important function that shows the structure of the data file
head() shows the first n rows of data


```r
covidDeaths <- read.table(file="Provisional_COVID-19_Deaths_by_Sex_and_Age.csv", sep=",", header=TRUE) 
str(covidDeaths)
```

```
## 'data.frame':	66096 obs. of  16 variables:
##  $ Data.As.Of                              : chr  "09/08/2021" "09/08/2021" "09/08/2021" "09/08/2021" ...
##  $ Start.Date                              : chr  "01/01/2020" "01/01/2020" "01/01/2020" "01/01/2020" ...
##  $ End.Date                                : chr  "09/04/2021" "09/04/2021" "09/04/2021" "09/04/2021" ...
##  $ Group                                   : chr  "By Total" "By Total" "By Total" "By Total" ...
##  $ Year                                    : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ Month                                   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ State                                   : chr  "United States" "United States" "United States" "United States" ...
##  $ Sex                                     : chr  "All Sexes" "All Sexes" "All Sexes" "All Sexes" ...
##  $ Age.Group                               : chr  "All Ages" "Under 1 year" "0-17 years" "1-4 years" ...
##  $ COVID.19.Deaths                         : int  643858 98 412 50 138 1232 3043 5395 8634 13567 ...
##  $ Total.Deaths                            : int  5507901 31347 55352 5798 9259 59389 104738 123138 150055 177545 ...
##  $ Pneumonia.Deaths                        : int  583698 336 924 187 270 1317 3071 5042 7728 11823 ...
##  $ Pneumonia.and.COVID.19.Deaths           : int  320617 12 93 10 40 496 1367 2551 4167 6714 ...
##  $ Influenza.Deaths                        : int  9272 22 188 65 80 80 148 236 324 366 ...
##  $ Pneumonia..Influenza..or.COVID.19.Deaths: int  914900 444 1431 292 448 2128 4883 8104 12494 19003 ...
##  $ Footnote                                : chr  "" "" "" "" ...
```

```r
head(covidDeaths, n=5)
```

```
##   Data.As.Of Start.Date   End.Date    Group Year Month         State       Sex
## 1 09/08/2021 01/01/2020 09/04/2021 By Total   NA    NA United States All Sexes
## 2 09/08/2021 01/01/2020 09/04/2021 By Total   NA    NA United States All Sexes
## 3 09/08/2021 01/01/2020 09/04/2021 By Total   NA    NA United States All Sexes
## 4 09/08/2021 01/01/2020 09/04/2021 By Total   NA    NA United States All Sexes
## 5 09/08/2021 01/01/2020 09/04/2021 By Total   NA    NA United States All Sexes
##      Age.Group COVID.19.Deaths Total.Deaths Pneumonia.Deaths
## 1     All Ages          643858      5507901           583698
## 2 Under 1 year              98        31347              336
## 3   0-17 years             412        55352              924
## 4    1-4 years              50         5798              187
## 5   5-14 years             138         9259              270
##   Pneumonia.and.COVID.19.Deaths Influenza.Deaths
## 1                        320617             9272
## 2                            12               22
## 3                            93              188
## 4                            10               65
## 5                            40               80
##   Pneumonia..Influenza..or.COVID.19.Deaths Footnote
## 1                                   914900         
## 2                                      444         
## 3                                     1431         
## 4                                      292         
## 5                                      448
```


## step 5 

Look at some of the variables to get a handle on what we've got:

```r
table(covidDeaths$Sex) 
```

```
## 
## All Sexes    Female      Male 
##     22032     22032     22032
```


```r
table(covidDeaths$Age.Group) 
```

```
## 
##        0-17 years         1-4 years       15-24 years       18-29 years 
##              3888              3888              3888              3888 
##       25-34 years       30-39 years       35-44 years       40-49 years 
##              3888              3888              3888              3888 
##       45-54 years        5-14 years       50-64 years       55-64 years 
##              3888              3888              3888              3888 
##       65-74 years       75-84 years 85 years and over          All Ages 
##              3888              3888              3888              3888 
##      Under 1 year 
##              3888
```

If you can see the table frequencies then everything seems to be working so let's move on to data wrangling


## FILTER the dataset
if you want to filter to a specific group to calculate the deaths, there are many, *many* ways in R. I think the best way is to use the functions from "tidyverse" along with "magrittr" which allows you to string together commands:

for example, to get only the numbers for males and females 18-29 years:

```r
covidDeaths.MF.18to29 <- covidDeaths %>% filter(Age.Group == "18-29 years" & Sex %in% (c("Male", "Female")))
head(covidDeaths.MF.18to29)
```

```
##   Data.As.Of Start.Date   End.Date    Group Year Month         State    Sex
## 1 09/08/2021 01/01/2020 09/04/2021 By Total   NA    NA United States   Male
## 2 09/08/2021 01/01/2020 09/04/2021 By Total   NA    NA United States Female
## 3 09/08/2021 01/01/2020 09/04/2021 By Total   NA    NA       Alabama   Male
## 4 09/08/2021 01/01/2020 09/04/2021 By Total   NA    NA       Alabama Female
## 5 09/08/2021 01/01/2020 09/04/2021 By Total   NA    NA        Alaska   Male
## 6 09/08/2021 01/01/2020 09/04/2021 By Total   NA    NA        Alaska Female
##     Age.Group COVID.19.Deaths Total.Deaths Pneumonia.Deaths
## 1 18-29 years            1886        76679             1885
## 2 18-29 years            1157        28059             1186
## 3 18-29 years              39         1484               31
## 4 18-29 years              22          545               24
## 5 18-29 years              NA          215               NA
## 6 18-29 years              NA           95               NA
##   Pneumonia.and.COVID.19.Deaths Influenza.Deaths
## 1                           828               78
## 2                           539               70
## 3                            14               NA
## 4                            NA                0
## 5                            NA               NA
## 6                            NA                0
##   Pneumonia..Influenza..or.COVID.19.Deaths
## 1                                     3012
## 2                                     1871
## 3                                       59
## 4                                       38
## 5                                       NA
## 6                                       NA
##                                                                                                                     Footnote
## 1                                                                                                                           
## 2                                                                                                                           
## 3 One or more data cells have counts between 1-9 and have been suppressed in accordance with NCHS confidentiality standards.
## 4 One or more data cells have counts between 1-9 and have been suppressed in accordance with NCHS confidentiality standards.
## 5 One or more data cells have counts between 1-9 and have been suppressed in accordance with NCHS confidentiality standards.
## 6 One or more data cells have counts between 1-9 and have been suppressed in accordance with NCHS confidentiality standards.
```

The '<-' symbol is prounounced 'gets' and the %>% is a pipe from the magrittr package and is called 'and then' so the syntax in words is:

#covidDeaths.MF.18to29 'gets' covidDeaths object 'and then' filters to Age.group= 18-29 and Male or Female sex. **Remember to use '==' not just '='**

## now do a little analysis:

what is the mean number of covid deaths across all states by male and female for 50 years and older?
1. have to get rid of the 'United States' total number and include only the state totals
2. have to filter to only  age 50-64 years plus age 65-74 years plus age 75-84 years plus age 85 years and over 


```r
covidDeaths.MF.over50 <- covidDeaths %>% 
  filter(Age.Group %in% c("50-64 years", "65-74 years", "75-84 years","85 years and over") & 
Sex %in% (c("Male", "Female"))) %>% 
  filter(!State ==  "United States") %>% 
  select(State, Age.Group, Sex, COVID.19.Deaths, Total.Deaths, Pneumonia.Deaths, Pneumonia.and.COVID.19.Deaths, Influenza.Deaths, Pneumonia..Influenza..or.COVID.19.Deaths) 
head(covidDeaths.MF.over50) 
```

```
##     State         Age.Group    Sex COVID.19.Deaths Total.Deaths
## 1 Alabama       50-64 years   Male            1338        11936
## 2 Alabama       65-74 years   Male            1863        13523
## 3 Alabama       75-84 years   Male            1874        13418
## 4 Alabama 85 years and over   Male            1104         9070
## 5 Alabama       50-64 years Female            1007         7945
## 6 Alabama       65-74 years Female            1335         9984
##   Pneumonia.Deaths Pneumonia.and.COVID.19.Deaths Influenza.Deaths
## 1              948                           448               34
## 2             1400                           667               27
## 3             1390                           677               13
## 4              842                           371               NA
## 5              726                           351               21
## 6              962                           468               17
##   Pneumonia..Influenza..or.COVID.19.Deaths
## 1                                     1869
## 2                                     2621
## 3                                     2597
## 4                                     1581
## 5                                     1402
## 6                                     1845
```


```r
covidDeaths.MF.over50 %>% group_by(Sex) %>% 
  summarize(meanDeaths=mean(COVID.19.Deaths), medianDeaths=median(COVID.19.Deaths))
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

```
## # A tibble: 2 x 3
##   Sex    meanDeaths medianDeaths
##   <chr>       <dbl>        <int>
## 1 Female         NA           NA
## 2 Male           NA           NA
```

UGH it didn't work! - probably because there are some states with no COVID deaths so it can't get the mean

to figure out what to do just ##google the problem##. I looked at this page <https://www.rdocumentation.org/packages/stats/versions/3.6.2/topics/median>
it says the syntax is: 'median(x, na.rm = FALSE). That means by default it will not remove the NA values. Let's change to TRUE


```r
covidDeaths.MF.over50 %>% group_by(Sex) %>% 
  summarize(meanDeaths=mean(COVID.19.Deaths, na.rm=TRUE), medianDeaths=median(COVID.19.Deaths, na.rm=TRUE))
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

```
## # A tibble: 2 x 3
##   Sex    meanDeaths medianDeaths
##   <chr>       <dbl>        <int>
## 1 Female       206.           39
## 2 Male         238.           45
```


first, look at this object. It's called a 'tibble' which is a silly name but it's basically like a data.frame which you can think of as a single Excel sheet with rows of observations and columns of variables (variables that don't have to be all numeric or all character)

But it also looks like a table, right? 
let's give it a name by assigning it a name with the '<-' operator:

```r
covidDeathsTable1 <- covidDeaths.MF.over50 %>% group_by(Sex) %>% 
  summarize(meanDeaths=mean(COVID.19.Deaths, na.rm=TRUE), medianDeaths=median(COVID.19.Deaths, na.rm=TRUE))
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

```r
str(covidDeathsTable1)
```

```
## tibble [2 x 3] (S3: tbl_df/tbl/data.frame)
##  $ Sex         : chr [1:2] "Female" "Male"
##  $ meanDeaths  : num [1:2] 206 238
##  $ medianDeaths: int [1:2] 39 45
```


#now the table is an R object so we can apply functions to it. Like the mean:

```r
mean(covidDeathsTable1$meanDeaths)
```

```
## [1] 222.3604
```

this is the mean of the mean deaths in the table. So you can analyze datasets created from other datasets!

## make a graph of the state numbers by sex

get a graphing package called 'ggpubr' that's pretty easy to use. See <http://www.sthda.com/english/articles/24-ggpubr-publication-ready-plots/>
install the package "ggpubr" using the lower right hand box in Rstudio


```r
library(ggpubr)
```

to plot each state by deaths over 50 years we have to first condense all the deaths by sex within the state because we want only 2 rows for each state; one for M and one for F because right now the data look like this:

```r
covidDeaths.MF.over50 %>% select(State, Age.Group, Sex, COVID.19.Deaths) %>% 
head()
```

```
##     State         Age.Group    Sex COVID.19.Deaths
## 1 Alabama       50-64 years   Male            1338
## 2 Alabama       65-74 years   Male            1863
## 3 Alabama       75-84 years   Male            1874
## 4 Alabama 85 years and over   Male            1104
## 5 Alabama       50-64 years Female            1007
## 6 Alabama       65-74 years Female            1335
```


```r
covidDeaths.StateSummary.Over50 <- covidDeaths.MF.over50 %>% group_by(State,Sex) %>%
  summarise(Total.Deaths=sum(COVID.19.Deaths, na.rm=TRUE)) 
```

```
## `summarise()` regrouping output by 'State' (override with `.groups` argument)
```

```r
head(covidDeaths.StateSummary.Over50)
```

```
## # A tibble: 6 x 3
## # Groups:   State [3]
##   State   Sex    Total.Deaths
##   <chr>   <chr>         <int>
## 1 Alabama Female        16262
## 2 Alabama Male          18497
## 3 Alaska  Female          334
## 4 Alaska  Male            512
## 5 Arizona Female        19469
## 6 Arizona Male          27718
```

See, it's easy to summarise with the 'tidyverse' package. (actually summarize() works too) 

so now we can try plotting:

```r
CovidStates <- ggbarplot(covidDeaths.StateSummary.Over50, x = "State", y = "Total.Deaths",
          fill = "Sex", color = "Sex",           
          palette = c("pink", "blue"),  
          position = position_dodge(0.9),
          x.text.angle = 90          
          )
```

to see the plot in the lower right frame just type the name (guess what - it's an R object too!)


```r
CovidStates
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-15-1.png" width="672" />
*HINT* pull the lower right frame sideways to make it bigger

to export the plot to a file - change to your path using code like this:
CovidStates %>% 
  ggexport(width = 800, height = 500, filename = "yourpath/CovidStates.png")

