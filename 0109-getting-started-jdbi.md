---
layout: default
title: Getting Started JDBI
short_title: JDBI
category: getting_started
module: jdbi
description: SimpleFlatMapper java jdbi ResultSetMapper mapper deep object
---

# Getting Started JDBI

{% include maven_dependency.md %}

From [7.0.0](/11-v7-migration.html) you will need explicitly include the jdbi dependency in your pom.


## Register SfmResultSetMapperFactory

You can register sfm as a ResultSetMapperFactory, then use the mapTo - in place of map - it will 
JDBI will look for a registered Factory and is the SFM one is the only or first one it will use it
to map the object.

{% highlight java %}
DBI dbi = new DBI(datasource);
dbi.registerMapper(new SfmResultSetMapperFactory());
Handle handle = dbi.open();
try {
    DbObject dbObject = handle.createQuery("SELECT * FROM T1").mapTo(DbObject.class).first();
} finally {
    handle.close();
}
{% endhighlight %}

## Annotation @RegisterMapperFactory

You can also annotate your method or dao interface with 

{% highlight java %}
@RegisterMapperFactory(value = {SfmResultSetMapperFactory.class})
public interface MyDao
{
    @SqlQuery("SELECT * FROM TEST_DB_OBJECT where id = :id")
    DbObject selectOne(@Bind("id") long id);

}
{% endhighlight %}


## Annotation @SfmBind

SFM also provide a binding annotation equivalent of @BindBean but using the SFM mapping.

{% highlight java %}
public interface MyDao
{
    @SqlUpdate("insert into TEST_DB_OBJECT (id, name, email, creation_time, type_ordinal, type_name) values (:id, :name, :email, :creation_time, :type_ordinal, :type_name)")
    void insert(@SfmBind(sqlTypes = {@SqlType(name ="type_ordinal", type=Types.NUMERIC)}) DbObject s);
}
{% endhighlight %}


