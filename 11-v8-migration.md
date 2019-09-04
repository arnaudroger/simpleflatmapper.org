---
layout: default
title: Migrating to v8.
comments: true
redirect_from:
  - /11-v3-migration.html
  - /11-v4-migration.html
  - /11-v5-migration.html
  - /11-v6-migration.html
  - /11-v7-migration.html
---
# Migration v7 to [v8](/2019/09/01/v8.0.0.html)
* It does not use the classifier anymore. The artifactId is different for each jre version using -jre6 or -jre9 suffix.

### Java 8, 9, 10 , 11 no module-info
{% highlight xml %}
<dependency>
    <groupId>org.simpleflatmapper</groupId>
    <artifactId>sfm-jdbc</artifactId>
    <version>{% include currentversion.html %}</version>
</dependency>
{% endhighlight %}

### Java 6, 7

{% highlight xml %}
<dependency>
    <groupId>org.simpleflatmapper</groupId>
    <artifactId>sfm-jdbc-jre6</artifactId>
    <version>{% include currentversion.html %}</version>
</dependency>
{% endhighlight %}

### Java 9, 10 , 11 with module-info 

{% highlight xml %}
<dependency>
    <groupId>org.simpleflatmapper</groupId>
    <artifactId>sfm-jdbc-jre9</artifactId>
    <version>{% include currentversion.html %}</version>
</dependency>
{% endhighlight %}

* No api change

# Migration v6 to v7

* No api change
* [the speculative index property finding is disable by default](https://github.com/arnaudroger/SimpleFlatMapper/issues/665), you can reenable it by calling `enableSpeculativeArrayIndexResolution()` on the `MapperFactory`
* jOOQ, spring-jdbc, datastax, jdbi, sql2o, poi and querydsl dependency are not [mark as provided](https://github.com/arnaudroger/SimpleFlatMapper/issues/659) and need to be included in your pom. 

# Migration v5 to v6

## for the mappers user
there should not be any change needed as the api should be backward compatible, if you encounter an issue please raise a ticket
Just make sure all the module are on the same version, as the generic type change will lead to weird errors if not.

## for mapper producers 
* the mapper config now as a new parameterized type representing the source object
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



 