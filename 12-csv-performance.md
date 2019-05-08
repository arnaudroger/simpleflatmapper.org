---
layout: default
title: Performance comparison
description: SimpleFlatMapper java library fast csv parser performance comparison jackson-csv univocity
---

 

[Benchmark Code](https://github.com/arnaudroger/mapping-benchmark/blob/master/sfm-csv)

The benchmarks ran on a P500 Lenovo using java 11 and a fixed processor Frequency of 3GHz.

* [Unescaped/Escaped/Parallel](#csv-parsing-unescapedescaped-and-parallel)
* [Number of rows effect](#number-of-rows-effect-on-parsingmapping-no-quotes)

Univocity is the benchmark with readInputOnSeparateThread set to false, 
ConcurrentUnivocity has that flag set to true. 
All the parallel test appart from ConcurrentUnivocity uses a [ParallelReader](https://github.com/arnaudroger/SimpleFlatMapper/blob/master/sfm-util/src/main/java/org/simpleflatmapper/util/ParallelReader.java).

# Csv Parsing Unescaped/Escaped and Parallel


|     Library    | Version |
| ------------- | ----:|
| Jackson       | 2.9.8 | 
| Sfm           | 6.7.0 |
| Univocity     | 2.8.1 |


The [csv file](https://github.com/arnaudroger/mapping-benchmark/raw/master/sfm-csv/src/main/resources/worldcitiespop.txt.gz) parsed is 145 MB unescaped and 188MB with quotes.

Why only those 3? because the other that I tested are pretty slow in comparison.
If you think your csv parser is worth benchmark [Open an issue](https://github.com/arnaudroger/mapping-benchmark/issues/new).

## Parsing an unescaped [Csv](https://github.com/arnaudroger/mapping-benchmark/raw/master/sfm-csv/src/main/resources/worldcitiespop.txt.gz)

| Parser        | avgt ms |  avgt MB/s |
| ------------- | ----:| -----:| 
| Jackson       | 1593 |  90   |  
| Sfm           | 1040 | 139   | 
| Univocity     | 1256 | 115   | 

## Parsing an escaped version of [Csv](https://github.com/arnaudroger/mapping-benchmark/raw/master/sfm-csv/src/main/resources/worldcitiespop.txt.gz)

| Parser        | avgt ms | avgt MB/s |
| ------------- | ----:| -----:|
| Jackson       | 1592 | 118  |
| Sfm           | 1103 | 170  |
| Univocity     | 1491 | 126  |

## Parsing an unescaped [Csv](https://github.com/arnaudroger/mapping-benchmark/raw/master/sfm-csv/src/main/resources/worldcitiespop.txt.gz) with [ParallelReader](https://github.com/arnaudroger/SimpleFlatMapper/blob/master/sfm-util/src/main/java/org/simpleflatmapper/util/ParallelReader.java)

ConcurrentUnivocity uses readInputOnSeparateThread set to true and no ParallelReader.

| Parser              | avgt ms | avgt MB/s |
| -------------       | ----:| -----:|
| Jackson             | 1243 | 116 |
| Sfm                 |  740 | 195 |
| Univocity           |  890 | 162 |
| ConcurrentUnivocity |  844 | 171 |


## Parsing a escaped version of [Csv](https://github.com/arnaudroger/mapping-benchmark/raw/master/sfm-csv/src/main/resources/worldcitiespop.txt.gz) with [ParallelReader](https://github.com/arnaudroger/SimpleFlatMapper/blob/master/sfm-util/src/main/java/org/simpleflatmapper/util/ParallelReader.java)

ConcurrentUnivocity uses readInputOnSeparateThread set to true and no ParallelReader.

| Parser        | avgt ms | avgt MB/s |
| ------------- | ----:| -----:|
| Jackson             | 1342 | 140 |
| Sfm                 |  812 | 231 |
| Univocity           | 1105 | 170 |
| ConcurrentUnivocity | 1054 | 178 |


# Number of rows effect on parsing/mapping no quotes

Those results are from Jan 2017 and needs updating, take with caution.


|     Library    | Version |
| ------------- | ----:|
| Jackson       | 2.8.5 | 
| Sfm           |   3.5 |
| Univocity     | 2.2.3 |


[Raw Data](https://github.com/arnaudroger/mapping-benchmark/blob/master/sfm-csv/jmh-result-3.5-rows.csv)


All chart represent the time in us taken to read a file with the specified number of rows, the lower the better.
* Orange csv mapping
* Brown csv mapping with parallel reader or concurrent univocity
* blue csv parser
* dark blue csv parser with parallel reader or concurrent univocity


## 1 row
![1 Row](/assets/perf/3.5/row_1.png)

## 10 rows
![10 Row](/assets/perf/3.5/row_10.png)

## 1000 rows
![1000 Row](/assets/perf/3.5/row_1000.png)

## 100,000 rows
![100,000 Row](/assets/perf/3.5/row_100000.png)

## 1.5m rows
![1.5m Row](/assets/perf/3.5/row_1_5m.png)

## Summary

Sfm and Jackson have similar performances with small size files, Univocity has a fix cost that show on those file
make it not very suitable for those.
Using parallel for small size file is a bad idea.

Jackson mapper underperform and lag behind from 1000 rows. Sfm mapper is clearly in front on most sizes from 1000.

Also surprisingly jackson perform slower with the parallel reader that would need more investigation to 
identify where the contention comes from.