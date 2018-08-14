---
layout: default
title: Migrating to v4.
comments: true
redirect_from:
  - /11-v3-migration.html
---

# Migration v3 to v4

* create new `lightning-csv` module with only the csv parser
* `org.simpleflatmapper.csv.parser` moved to `org.simpleflatmapper.lightningcsv.parser` in `lightning-csv` module
* the following classes in `org.simpleflatmapper.csv` moved to `org.simpleflatmapper.lightningcsv` in `lightning-csv` module
  * `CellWriter`
  * `CsvReader`
  * `CloseableCsvReader`
  * `Row`
  * `StringReader`
* a `CsvParser` dsl without out the mapping part has been added to `org.simpleflatmapper.lightningcsv`, `there is still one in org.simpleflatmapper.csv`.
* the mappers returned don't exposed the `mapTo` method anymore to allow for `ImmutableList` support


# Migration v2 to v3 

# sfm module has been split into

* sfm-csv for the Csv related code, CsvParser, CsvMapper, CsvWritter.
* sfm-jdbc for the Jdbc related code, JdbcMapper, PreparedStatementMapper, Crud.

# the packages have been renamed

* org.sfm -> org.simpleflatmapper
* org.sfm.utils -> org.simpleflatmapper.util

# Joda time support

You know need to include the sfm-converter-joda-time module.



 