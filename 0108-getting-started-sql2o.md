---
layout: default
title: Getting Started Sql2o
short_title: Sql2o
category: getting_started
module: sql2o
description: SimpleFlatMapper java sql2 ResultSetHandler mapper deep object
---
# Why?

Although Sql2o provides a [mapping](http://www.sql2o.org/docs/fetching-data/) implementation. It does not support inner objects, factory methods, or joins...

By adding just a 2 lines of code to your queries, you will gain the [mapping power](0201-property-mapping.html) and
the [performance](https://github.com/arnaudroger/SimpleFlatMapper/wiki/Jdbc-Performance-Local-Mysql) of SimpleFlatMapper.

Give it a try.

# Getting Started Sql2o

{% include maven_dependency.md %}

From [7.0.0](/11-v7-migration.md) you will need explicitly include the sql2o dependency in your pom.


# SQL2o integration

{% highlight java %}
Query query = sql2o.open().createQuery("select * from table");
query.setAutoDeriveColumnNames(true);
query.setResultSetHandlerFactoryBuilder(new SfmResultSetHandlerFactoryBuilder());

List<DbObject> dbObjects = query.executeAndFetch(DbObject.class);
{% endhighlight %}