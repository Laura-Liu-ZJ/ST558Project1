Project1
================
Zhijun Liu
2020-06-11

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
  - [Exploratory Data Analysis](#exploratory-data-analysis)
      - [winPct & futureWin in
        teamtotalData](#winpct-futurewin-in-teamtotaldata)
          - [Treat the winPct as categorical
            data](#treat-the-winpct-as-categorical-data)
          - [Treat the winPct as numeric
            data](#treat-the-winpct-as-numeric-data)
      - [mfWinRatio & steadyWin in
        seasonData](#mfwinratio-steadywin-in-seasondata)
          - [Treat the winPct as categorical
            data](#treat-the-winpct-as-categorical-data-1)
          - [Treat the winPct as numeric
            data](#treat-the-winpct-as-numeric-data-1)
      - [The best and Worst id](#the-best-and-worst-id)
      - [The difference in goalie of 38 and
        28](#the-difference-in-goalie-of-38-and-28)
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

# Exploratory Data Analysis

## winPct & futureWin in teamtotalData

### Treat the winPct as categorical data

I create a new variable called winPct in teamtotalData, if winPct is
equal or greater than 0.5, which means the total number of wins is
greater than other kinds of situations (losses or ties), we can conclude
that those franchises are more likely to win in the future. Thus, I
would create a new variable called futureWin which is an indicator
according to whether winPct for each franchise is less than 0.5.

\[futureWin=
\begin{cases}
1,& winPct \ge 0.5 \\
0,& \text{O.W.}
\end{cases}\]

According to the Contingency table of futureWin, we can know that there
are three franchises greater than or equal to 0.5, which means those
three franchises are more likely to win in the future. Also, those three
franchises are active. And the number of active franchises is more than
that of inactive franchises.

|   | 0 |  1 |
| - | -: | -: |
| 0 | 7 | 28 |
| 1 | 0 |  3 |

Contingency table of futureWin

![](project1_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

To sort out the best three franchises and the worst three franchises
according to the winPct, I arrange the data sets and combine them with
franchiseData, which help us to connect the franchiseId with its name.
From the Worst three table of futurnWin, we know that the worst three
franchises are not active, which might be the reason for their winPct.
Besides, the best three franchiseId are 1,16,38, and the worst three
franchiseId are 2,9,13.

| franchiseId | teamCommonName | teamPlaceName | sumWins | sumGames | activeFranchise | winPct | futureWin |
| ----------: | :------------- | :------------ | ------: | -------: | --------------: | -----: | --------: |
|           1 | Canadiens      | Montréal      |    3878 |     7480 |               1 |   0.52 |         1 |
|          16 | Flyers         | Philadelphia  |    2275 |     4548 |               1 |   0.50 |         1 |
|          38 | Golden Knights | Vegas         |     149 |      262 |               1 |   0.57 |         1 |

Best three table of futurnWin

| franchiseId | teamCommonName | teamPlaceName | sumWins | sumGames | activeFranchise | winPct | futureWin |
| ----------: | :------------- | :------------ | ------: | -------: | --------------: | -----: | --------: |
|           2 | Wanderers      | Montreal      |       1 |        6 |               0 |   0.17 |         0 |
|           9 | Quakers        | Philadelphia  |      72 |      260 |               0 |   0.28 |         0 |
|          13 | Barons         | Cleveland     |     232 |      869 |               0 |   0.27 |         0 |

Worst three table of futurnWin

### Treat the winPct as numeric data

From the summaries, we can know that the mean of winPct is 0.4326, which
is less than 0.5, which means most values of winPct are less than 0.5.
According to the histogram, we can see that the distribution of winPct
is left-skewed. From the side-by-side boxplot, we can notice that, the
winPct of seven inactives franchises are lesser than that of active
franchises.

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##  0.1700  0.4225  0.4500  0.4326  0.4675  0.5700

![](project1_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->![](project1_files/figure-gfm/unnamed-chunk-8-2.png)<!-- -->

## mfWinRatio & steadyWin in seasonData

### Treat the winPct as categorical data

I also create a new variable called mfWinRatio in seasonData, if
mfWinRatio more equal to 1, which means, the total number of the most
wins are more closer to that of the fewest wins. However, there are some
missing values, which need to be picked out considering not knowing the
true story behind the those missing values. Thus, I would drop those
missing records first, them I would create a new variable called
steadyWin which is a indicator according to whether mfWinRatio for each
franchise is closer to 1.

\[steadyWin=
\begin{cases}
1,& mfWinRatio < 2 \\
0,& \text{O.W.}
\end{cases}\]

According to the Contingency table of steadyWin, we can know that there
are three franchises less than 2, which means those three franchises are
playing more stably. Also, those three franchises are active. And the
number of active franchises is more than that of inactive franchises.

| Var1 | Freq |
| :--- | ---: |
| 0    |   29 |
| 1    |    3 |

Contingency table of steadyWin

![](project1_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

To sort out the best three franchises and the worst three franchises
according to the mfWinRatio, I arrange the data sets and combine them
with franchiseData, which help us to connect the franchiseId with its
name. Besides, the best three franchiseId are 34,37,38, and the worst
three franchiseId are 24,28,30. However, all of the most franchises are
active.

| franchiseId | teamCommonName | teamPlaceName | fewestWins | mostWins | mfWinRatio | steadyWin | activeFranchise |
| ----------: | :------------- | :------------ | ---------: | -------: | ---------: | --------: | --------------: |
|          34 | Predators      | Nashville     |         27 |       53 |       1.96 |         1 |               1 |
|          37 | Wild           | Minnesota     |         25 |       49 |       1.96 |         1 |               1 |
|          38 | Golden Knights | Vegas         |         39 |       51 |       1.31 |         1 |               1 |

Best three table of steadyWin

| franchiseId | teamCommonName | teamPlaceName | fewestWins | mostWins | mfWinRatio | steadyWin | activeFranchise |
| ----------: | :------------- | :------------ | ---------: | -------: | ---------: | --------: | --------------: |
|          24 | Capitals       | Washington    |          8 |       56 |       7.00 |         0 |               1 |
|          28 | Coyotes        | Arizona       |          9 |       50 |       5.56 |         0 |               1 |
|          30 | Senators       | Ottawa        |         10 |       52 |       5.20 |         0 |               1 |

Worst three table of steadyWin

### Treat the winPct as numeric data

From the summaries, we can know that the mean of mfWinRatio is 3.327,
which is greater than 1, which means most values of mfWinRatio are
greater than 1. According to the histogram, we can see that the
distribution of mfWinRatio is right-skewed. From the side-by-side
boxplot, we can notice that, except those missing records, only one
inactive franchise exists in the seasonData, which might be the season
why this record seems more steady than other data points.

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
    ##   1.310   2.353   3.120   3.327   4.115   7.000       6

![](project1_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->![](project1_files/figure-gfm/unnamed-chunk-12-2.png)<!-- -->

## The best and Worst id

    ## # A tibble: 1 x 3
    ##   franchiseId winPct mfWinRatio
    ##         <int>  <dbl>      <dbl>
    ## 1          38  0.570       1.31

    ## # A tibble: 1 x 3
    ##   franchiseId winPct mfWinRatio
    ##         <int>  <dbl>      <dbl>
    ## 1          28   0.41       5.56

## The difference in goalie of 38 and 28

![](project1_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->![](project1_files/figure-gfm/unnamed-chunk-15-2.png)<!-- -->

# Conclusion

# Discussion on this project

## what would you do differently?

## what was the most difficult part for you?

## what are your big take-aways from this project?
