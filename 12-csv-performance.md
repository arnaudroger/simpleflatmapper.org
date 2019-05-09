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
| Sfm Raw      | 747  | 194   | 
| Sfm Callback | 1040 | 139   | 
| Sfm Iterate  | 1127 | 128   | 
| Univocity     | 1256 | 115   | 
| Jackson       | 1593 |  90   |  

## Parsing an escaped version of [Csv](https://github.com/arnaudroger/mapping-benchmark/raw/master/sfm-csv/src/main/resources/worldcitiespop.txt.gz)

| Parser        | avgt ms | avgt MB/s |
| ------------- | ----:| -----:|
| Sfm Raw       | 921  | 204  |
| Sfm Callback  | 1103 | 170  |
| Sfm Iterate   | 1140 | 164  |
| Univocity     | 1491 | 126  |
| Jackson       | 1592 | 118  |

## Parsing an unescaped [Csv](https://github.com/arnaudroger/mapping-benchmark/raw/master/sfm-csv/src/main/resources/worldcitiespop.txt.gz) with [ParallelReader](https://github.com/arnaudroger/SimpleFlatMapper/blob/master/sfm-util/src/main/java/org/simpleflatmapper/util/ParallelReader.java)

ConcurrentUnivocity uses readInputOnSeparateThread set to true and no ParallelReader.

| Parser              | avgt ms | avgt MB/s |
| -------------       | ----:| -----:|
| Sfm Raw             |  530 | 273 |
| Sfm Callback        |  740 | 195 |
| Sfm Iterate         |  759 | 190 |
| ConcurrentUnivocity |  844 | 171 |
| Univocity           |  890 | 162 |
| Jackson             | 1243 | 116 |


## Parsing a escaped version of [Csv](https://github.com/arnaudroger/mapping-benchmark/raw/master/sfm-csv/src/main/resources/worldcitiespop.txt.gz) with [ParallelReader](https://github.com/arnaudroger/SimpleFlatMapper/blob/master/sfm-util/src/main/java/org/simpleflatmapper/util/ParallelReader.java)

ConcurrentUnivocity uses readInputOnSeparateThread set to true and no ParallelReader.

| Parser        | avgt ms | avgt MB/s |
| ------------- | ----:| -----:|
| Sfm Raw             |  610 | 307 |
| Sfm Callback        |  812 | 231 |
| Sfm Iterate         |  826 | 227 |
| ConcurrentUnivocity | 1054 | 178 |
| Univocity           | 1105 | 170 |
| Jackson             | 1342 | 140 |


## Notes

The UTF8 decoding performance varied quite a bit depending on C2 performance. that variability is not represented here as I just display the average.
I'm planning to investigate further and find out where it's coming from. 