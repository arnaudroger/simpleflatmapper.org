---
layout: default
title: Getting Started Cassandra Datastax
short_title: Cassandra
category: getting_started
module: datastax
description: SimpleFlatMapper java cassandra mapper and crud
---
# Getting Started Cassandra

{% include maven_dependency.md %}

Works with datastax 2.1.x to 3.0.x, from [7.0.0](/11-v7-migration.md) you will need explicitly include the datastax dependency in your pom.

## What about the Datastax Mapper
* SFM is at about 20% faster
* More flexible
* No configuration

## Mapper

{% highlight java %}
final DatastaxMapper<DbObject> parameterGetterMap =
    DatastaxMapperFactory.newInstance().mapTo(DbObject.class);

...

    ResultSet rs =
        session.execute(
            "select id, name, email, creation_time, type_ordinal, type_name"
            + " from dbobjects");
    final Iterator<DbObject> iterator = parameterGetterMap.iterator(rs);


final DatastaxBinder<DbObject> datastaxBinder =
    DatastaxMapperFactory.newInstance().mapFrom(DbObject.class);

...
    PreparedStatement preparedStatement = session.prepare(
       "insert into " +
       "dbobjects(id, name, email, creation_time, type_ordinal, type_name) " +
       "values(?, ?, ?, ?, ?, ?)"
    );

    session.execute(datastaxBinder.mapTo(dbObjects, preparedStatement));
{% endhighlight %}

## Crud

{% highlight java %}
DatastaxCrud<DbObject, Long> crud =
    DatastaxMapperFactory
        .newInstance()
        .crud(DbObject.class, Long.class)
        .to(session, "dbobjects");

crud.save(session, object);
DbObject object = crud.read(session, object.getId());
crud.delete(session, object.getId());
{% endhighlight %}
