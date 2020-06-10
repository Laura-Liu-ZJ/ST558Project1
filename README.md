Project1
================
Zhijun Liu
2020-06-10

  - [Abstract](#abstract)
  - [Introduction](#introduction)
      - [Description of JSON Data](#description-of-json-data)
          - [What it is?](#what-it-is)
          - [Where does it get used?](#where-does-it-get-used)
          - [Why is it a good way to store
            data?](#why-is-it-a-good-way-to-store-data)
      - [Discussion on R packages for reading JSON
        data](#discussion-on-r-packages-for-reading-json-data)
  - [NHL & NHL Data](#nhl-nhl-data)
      - [What is NHL?](#what-is-nhl)
      - [View the NHL data](#view-the-nhl-data)
      - [The reason of variable I
        choose](#the-reason-of-variable-i-choose)
  - [Exploratory Data Analysis](#exploratory-data-analysis)
      - [Categorical data](#categorical-data)
          - [Statistical Summary for categorical
            data](#statistical-summary-for-categorical-data)
          - [Graph display for categorical
            data](#graph-display-for-categorical-data)
      - [Numeric data](#numeric-data)
          - [Statistical summary for numeric
            data](#statistical-summary-for-numeric-data)
          - [Graph display for numeric
            data](#graph-display-for-numeric-data)
  - [Conclusion](#conclusion)
  - [Discussion on this project](#discussion-on-this-project)
      - [what would you do differently?](#what-would-you-do-differently)
      - [what was the most difficult part for
        you?](#what-was-the-most-difficult-part-for-you)
      - [what are your big take-aways from this
        project?](#what-are-your-big-take-aways-from-this-project)

# Abstract

*What did I do + brief results*

# Introduction

## Description of JSON Data

### What it is?

JSON (JavaScript Object Notation) is an open standard file format, and
data interchange format, that uses human-readable text to store and
transmit data objects consisting of attribute–value pairs and array data
types (or any other serializable value). JSON is a language-independent
data format. It was derived from JavaScript, but many modern programming
languages include code to generate and parse JSON-format
data.(<https://en.wikipedia.org/wiki/JSON>)

### Where does it get used?

JSON is commonly used for transmitting data in web applications (e.g.,
sending some data from the server to the client, so it can be displayed
on a web page, or vice versa). And It’s also becoming an increasingly
common format for database migration from modern apps (such as MixPanel,
SalesForce, and Shopify) over to SQL
databases.(<https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/JSON>)

### Why is it a good way to store data?

JSON is perfect for storing temporary data that’s consumed by the entity
that creates the data. A good example is user-generated data such as
filling out a form or information exchange between an API and an app.
Stored JSON data must be text but this means JSON can be used as a data
format for any programming language, providing a high level of
interoperability. It also means data stored in JSON files are easily
sent between servers. This is a bonus for database
migration.(<https://blog.sqlizer.io/posts/json-store-data/>)

## Discussion on R packages for reading JSON data

  - `rjson`: This does not use S4/S3 methods and so is not readily
    extensible, but still useful. Unfortunately, it does not used
    vectorized operations and so is too slow for non-trivial data.
    Similarly, for reading JSON data into R, it is somewhat slow and so
    does not scale to large data, should this be an issue.

  - `RJSONIO`: This is an alternative to `rjson` package. Originally,
    that was too slow for converting large R objects to JSON and was not
    extensible. rjson’s performance is now similar to this package, and
    perhaps slightly faster in some cases. The two packages
    intentionally share the same basic interface. This package
    (`RJSONIO`) has many additional options to allow customizing the
    generation and processing of JSON content. This package uses libjson
    rather than implementing yet another JSON parser.

  - `jsonlite`: This is a fork of the `RJSONIO` package. It builds on
    the parser from `RJSONIO` but implements a different mapping between
    R objects and JSON strings. The C code in this package is mostly
    from the `RJSONIO` Package, the R code has been rewritten from
    scratch. In addition to drop-in replacements for `fromJSON` and
    `toJSON`, the package has functions to serialize objects.
    Furthermore, the package contains a lot of unit tests to make sure
    that all edge cases are encoded and decoded consistently for use
    with dynamic data in systems and applications. Thus, I choose
    `jsonlite`.

# NHL & NHL Data

## What is NHL?

``` r
include_graphics("D:/2020_3rd_semester/ST558/6. Project/WeaverLankow.jpg")
```

![](D:/2020_3rd_semester/ST558/6.%20Project/WeaverLankow.jpg)<!-- -->

The National Hockey League (NHL) is a professional ice hockey league in
North America, currently comprising 31 teams: 24 in the United States
and seven in Canada. The NHL is considered to be the premier
professional ice hockey league in the world, and one of the major
professional sports leagues in the United States and Canada. The Stanley
Cup, the oldest professional sports trophy in North America, is awarded
annually to the league playoff champion at the end of each
season.(<https://en.wikipedia.org/wiki/National_Hockey_League>)

The NHL divides the 31 teams into two conferences: the Eastern
Conference and the Western Conference. Each conference is split into two
divisions: the Eastern Conference contains 16 teams (eight per
division), while the Western Conference has 15 teams (seven in the
Central Division and eight in the Pacific Division). The current
alignment has existed since the 2017–18
season.(<https://en.wikipedia.org/wiki/Category:National_Hockey_League_teams>)

## View the NHL data

``` r
# write a function to get specific url
url <- function(kind,ID=NULL){
  base_url <- "https://records.nhl.com/site/api/franchise"
  if(kind == "base"){
    return("https://records.nhl.com/site/api/franchise")}
  else if(kind == "TeamTotal"){
    return( paste(base_url, "team","totals", sep="-"))
  }else{
    return(paste0(paste(base_url,kind,"records",sep="-"),"?",paste("cayenneExp","franchiseId",ID,sep="=")))
  }
}
# write a function to get data from specific url
GetData <- function(url){
  url %>%
    GET(.) %>%
    # turn it into JSON text form
    content(as="text") %>%
    # convert it to a list
    fromJSON(flatten=TRUE) 
}
```

``` r
# call above two functions together to get specific data
Franchise <- GetData(url("base"))$data
TeamTotal <- GetData(url("TeamTotal"))$data
# write a function to combine all the id(1-38) of specific table
CData <- function(kind){
  y <- NULL
  for(i in 1:38){
    x <- GetData(url(kind,ID=as.character(i)))$data
    y <- rbind(x,y)
  }
  return(y)
}
# get the season table of all id
Season <- CData("season")
# get the goalie table of all id
Goalie <- CData("goalie")
# get the skater table of all id
Skater <- CData("skater")
```

``` r
# view the data set
tbl_df(Franchise) %>% 
  select(id,contains("Name")) %>% 
  rename("franchiseId"=id)
#> # A tibble: 38 x 3
#>    franchiseId teamCommonName teamPlaceName
#>          <int> <chr>          <chr>        
#>  1           1 Canadiens      Montréal     
#>  2           2 Wanderers      Montreal     
#>  3           3 Eagles         St. Louis    
#>  4           4 Tigers         Hamilton     
#>  5           5 Maple Leafs    Toronto      
#>  6           6 Bruins         Boston       
#>  7           7 Maroons        Montreal     
#>  8           8 Americans      Brooklyn     
#>  9           9 Quakers        Philadelphia 
#> 10          10 Rangers        New York     
#> # ... with 28 more rows
tbl_df(TeamTotal) %>% 
  select(franchiseId,teamId,activeFranchise,gameTypeId,losses,wins,ties,points,pointPctg) %>%
  mutate(winPct= wins/(losses+wins+ties))
#> # A tibble: 104 x 10
#>    franchiseId teamId activeFranchise gameTypeId losses  wins  ties points
#>          <int>  <int>           <int>      <int>  <int> <int> <int>  <int>
#>  1          23      1               1          2   1181  1375   219   3131
#>  2          23      1               1          3    120   137    NA      2
#>  3          22      2               1          2   1570  1656   347   3818
#>  4          22      2               1          3    124   148    NA      8
#>  5          10      3               1          2   2693  2856   808   6667
#>  6          10      3               1          3    263   244     8      0
#>  7          16      4               1          3    212   221    NA      4
#>  8          16      4               1          2   1429  2054   457   4740
#>  9          17      5               1          2   1718  1866   383   4263
#> 10          17      5               1          3    175   206    NA     12
#> # ... with 94 more rows, and 2 more variables: pointPctg <dbl>, winPct <dbl>
tbl_df(Season) %>% 
  select(14,6,8,10,12,13,mostLosses,mostPoints,mostTies,mostWins,mostWinsSeasons)
#> # A tibble: 38 x 11
#>    franchiseId fewestLosses fewestPoints fewestTies fewestWins fewestWinsSeaso~
#>          <int>        <int>        <int>      <int>      <int> <chr>           
#>  1          38           24           86         NA         39 2019-20 (82)    
#>  2          37           25           68         10         25 2000-01 (82)    
#>  3          36           22           57          8         22 2001-02 (82)    
#>  4          35           20           39          7         14 1999-00 (82)    
#>  5          34           18           63          7         27 2002-03 (82)    
#>  6          33           26           60          6         22 2000-01 (82), 2~
#>  7          32           20           65          5         25 2000-01 (82)    
#>  8          31           16           44          6         17 1997-98 (82)    
#>  9          30           21           24          4         10 1992-93 (84)    
#> 10          29           18           24          2         11 1992-93 (84)    
#> # ... with 28 more rows, and 5 more variables: mostLosses <int>,
#> #   mostPoints <int>, mostTies <int>, mostWins <int>, mostWinsSeasons <chr>
  
tbl_df(Goalie) %>% 
  select(4,3,losses,ties,wins) %>%
  mutate(winPct= wins/(losses+wins+ties))
#> # A tibble: 1,056 x 6
#>    franchiseId firstName  losses  ties  wins  winPct
#>          <int> <chr>       <int> <int> <int>   <dbl>
#>  1          38 Marc-Andre     50     0    91   0.645
#>  2          38 Maxime          8     0     6   0.429
#>  3          38 Oscar           1     0     3   0.75 
#>  4          38 Malcolm        21     0    30   0.588
#>  5          38 Dylan           0     0     0 NaN    
#>  6          38 Garret          0     0     0 NaN    
#>  7          38 Robin           0     0     3   1    
#>  8          37 Josh           59    NA    60  NA    
#>  9          37 Niklas        142    NA   194  NA    
#> 10          37 Dwayne         71    26    62   0.390
#> # ... with 1,046 more rows

tbl_df(Skater) %>% 
  select(5,4,goals,points)
#> # A tibble: 16,871 x 4
#>    franchiseId firstName goals points
#>          <int> <chr>     <int>  <int>
#>  1          38 Deryk         8     41
#>  2          38 James        25     44
#>  3          38 Ryan         17     37
#>  4          38 David        16     66
#>  5          38 Jason         0      1
#>  6          38 Luca          2     14
#>  7          38 Brayden      11     40
#>  8          38 Reilly       68    167
#>  9          38 Tomas         4      6
#> 10          38 Brandon      15     23
#> # ... with 16,861 more rows
```

## The reason of variable I choose

# Exploratory Data Analysis

## Categorical data

*which data is categorical data* *why we choose this categorical data*

### Statistical Summary for categorical data

*contingency table*

``` r
# how many teams are in each franchise
table(TeamTotal$franchiseId)
#> 
#>  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 
#>  2  1  3  2  6  2  2  3  3  2  2  6  4  2  4  2  2  2  2  2  4  2  5  2  2  4 
#> 27 28 29 30 31 32 33 34 35 36 37 38 
#>  4  5  2  2  2  2  2  2  4  2  2  2
# how many teams are in each game type
table(TeamTotal$gameTypeId)
#> 
#>  2  3 
#> 57 47
# how many teams are in each condition of franchise
table(TeamTotal$activeFranchise)
#> 
#>  0  1 
#> 18 86

table(TeamTotal$franchiseId,TeamTotal$activeFranchise)
#>     
#>      0 1
#>   1  0 2
#>   2  1 0
#>   3  3 0
#>   4  2 0
#>   5  0 6
#>   6  0 2
#>   7  2 0
#>   8  3 0
#>   9  3 0
#>   10 0 2
#>   11 0 2
#>   12 0 6
#>   13 4 0
#>   14 0 2
#>   15 0 4
#>   16 0 2
#>   17 0 2
#>   18 0 2
#>   19 0 2
#>   20 0 2
#>   21 0 4
#>   22 0 2
#>   23 0 5
#>   24 0 2
#>   25 0 2
#>   26 0 4
#>   27 0 4
#>   28 0 5
#>   29 0 2
#>   30 0 2
#>   31 0 2
#>   32 0 2
#>   33 0 2
#>   34 0 2
#>   35 0 4
#>   36 0 2
#>   37 0 2
#>   38 0 2
# table(TeamTotal$firstSeasonId,TeamTotal$activeFranchise)
```

``` r
# sapply(NHLid,function(x){summary(x)})
# sapply(TeamTotal,function(x){summary(x)})
```

### Graph display for categorical data

*bar chart*

## Numeric data

*which data is categorical data* *why we choose this categorical data*

### Statistical summary for numeric data

*summary function*

### Graph display for numeric data

*box plot, histogram, scatterplot*

# Conclusion

# Discussion on this project

## what would you do differently?

## what was the most difficult part for you?

## what are your big take-aways from this project?
