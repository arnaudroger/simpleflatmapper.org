---
layout: default
title: Performance comparison
description: SimpleFlatMapper java library fast csv parser performance comparison jackson-csv univocity
---
[Benchmark Code](https://github.com/arnaudroger/mapping-benchmark/blob/master/sfm-csv)

The benchmarks ran on a P500 Lenovo, result might differ - and do, ran on old i5, Univocity is faster than sfm.


* [Unescaped/Escaped/Parallel](#csv-parsing-unescapedescaped-and-parallel)
* [Number of rows effect](#number-of-rows-effect-on-parsingmapping-no-quotes)

Univocity is the benchmark with readInputOnSeparateThread set to false, 
ConcurrentUnivocity has that flag set to true. 
All the parallel test appart from ConcurrentUnivocity uses a [ParallelReader](https://github.com/arnaudroger/SimpleFlatMapper/blob/master/sfm-util/src/main/java/org/simpleflatmapper/util/ParallelReader.java).


# Csv Parsing Unescaped/Escaped and Parallel

The score are time in ms to parse the file, the lower the better. The file is 145 MB uncompressed.

Why only those 3? because the other that I tested are pretty slow in compaison. If you think your csv parser is worth benchmark [Open an issue](https://github.com/arnaudroger/mapping-benchmark/issues/new).

## Parsing a unescaped [Csv](http://www.maxmind.com/download/worldcities/worldcitiespop.txt.gz)

| Parser        | avgt | 90th | 95th | 99th |  avgt MB/s |
| ------------- | ----:| -----:| -----:| -----:| ----:|
| Jackson       | 1237 | 1281  |  1300 |  1346 | 117 | 
| Sfm           | 854  | 858   | 859   | 865   | 169 |
| Univocity     | 958  | 969   | 974   | 994   | 151 |

![Parce csv no quotes](/assets/perf/3.5/no_quotes.png)

## Parsing a escaped version of [Csv](http://www.maxmind.com/download/worldcities/worldcitiespop.txt.gz)

| Parser        | avgt | 90th | 95th | 99th | avgt MB/s |
| ------------- | ----:| -----:| -----:| -----:| ----:|
| Jackson       | 1490 | 1726 | 1778 | 1810 |97 |
| Sfm           |1267 | 1502 | 1538 | 1550 |114|
| Univocity     |1846 | 1883 | 1906 | 1963 |78|

![Parce csv with quotes](/assets/perf/3.5/quotes.png)

## Parsing a unescaped [Csv](http://www.maxmind.com/download/worldcities/worldcitiespop.txt.gz) with [ParallelReader](https://github.com/arnaudroger/SimpleFlatMapper/blob/master/sfm-util/src/main/java/org/simpleflatmapper/util/ParallelReader.java)

ConcurrentUnivocity uses readInputOnSeparateThread set to true and no ParallelReader.

| Parser        | avgt | 90th | 95th | 99th |  avgt MB/s |
| ------------- | ----:| -----:| -----:| -----:| ----:|
| Jackson       | 1360 | 1545 | 1589 | 1875 | 106|
| Sfm           |749 | 825 | 891 | 999 | 193 |
| Univocity     |852 | 895 | 965 | 1097 | 170 |
| ConcurrentUnivocity     |795 | 806 | 813 | 849 | 182 |

![Parce csv, Parallel reader, no quotes](/assets/perf/3.5/par_noquotes.png)

## Parsing a escaped version of [Csv](http://www.maxmind.com/download/worldcities/worldcitiespop.txt.gz) with [ParallelReader](https://github.com/arnaudroger/SimpleFlatMapper/blob/master/sfm-util/src/main/java/org/simpleflatmapper/util/ParallelReader.java)

ConcurrentUnivocity uses readInputOnSeparateThread set to true and no ParallelReader.

| Parser        | avgt | 90th | 95th | 99th | avgt MB/s |
| ------------- | ----:| -----:| -----:| -----:| -----:|
| Jackson       | 1368 | 1493 | 1560 | 1699 | 105 |
| Sfm           |951 | 1048 | 1109 | 1230 | 152 |
| Univocity     |1378 | 1480 | 1545 | 1828 | 105 |
| ConcurrentUnivocity     |1302 | 1330 | 1365 | 1589 | 11|

![Parce csv, Parallel reader, with quotes](/assets/perf/3.5/par_quotes.png)


## Summary 

Sfm is wining on all measures appart from the 90th percentile on unescaped parallel - That is likely link to the implementation of the ParallelReader.
The Sfm non parallel escaped reading is even faster than the ConcurrentUnivocity. and only 60ms behind on the non escaped content - might not worth wasting a cpu.

Jackson is better than Univocity on escaped content, Univocity is better than Jackson on unescaped.
For some reason the advantages that Jackson possesses disappear in the parallel treatment.


# Number of rows effect on parsing/mapping no quotes

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