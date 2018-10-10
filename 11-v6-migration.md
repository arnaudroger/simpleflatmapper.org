---
layout: default
title: Migrating to v6.
comments: true
redirect_from:
  - /11-v3-migration.html
  - /11-v4-migration.html
  - /11-v5-migration.html
---

# Migration v5 to v6

* for mapper there should not be any change needed the api should be backward compatible, if you encounter an issue please raise a ticket

* the mapper producer the mapper config now as a new parameterized type representing the source object
* the ReflectionService is now a abstract class, you can use the static method or instantiate the DefaultReflectionService
* PropertyMeta and ClassMeta now implements a withReflectionService method    

# Migration v4 to v5

* for mapper there should not be any change needed the api should be backward compatible, if you encounter an issue please raise a ticket
* the mapper producer the getter/setter factory have changed, but I really doubt anybody is doing that if you do drop me an email.   

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



 