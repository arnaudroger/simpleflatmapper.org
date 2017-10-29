---
layout: default
title: Getting Started Jdbc
short_title: Jdbc
category: getting_started
module: jdbc
description: SimpleFlatMapper java micro orm jdbc ResultSet mapper
---

# Getting Started Jdbc

{% include maven_dependency.md %}

## ResultSet Mapping

All you need to do is instantiate a mapper via the JdbcMapperFactory. 
The JdbcMapper should be instantiated only once as it does a lot of reflection work on instantiation. 
It is thread-safe and can be called from multiple threads without synchronisation.


### Dynamic Mapping

{% highlight java %}
JdbcMapper<MyObject> mapper = 
    JdbcMapperFactory.newInstance().newMapper(MyObject.class);
{% endhighlight %}

you can then get a Stream of MyObject from the ResultSet, the mapping will be done using the ResultSetMetaData.

{% highlight java %}
mapper.stream(rs).forEach(System.out::println);
{% endhighlight %}

You can see more information on the [Property Mapping here](0201-property-mapping.html)

### Static Mapping

If you don't want to pay the cost of building the mapping from the ResultSetMetaData you can create a static mapper.

{% highlight java %}
JdbcMapper<MyObject> mapper = 
    JdbcMapperFactory.newInstance().newBuilder(MyObject.class)
				.addMapping("id")
				.addMapping("name")
				.addMapping("email")
				.addMapping("year_started").mapper();
{% endhighlight %}

### 1-N relationship

[See Page](https://github.com/arnaudroger/SimpleFlatMapper/wiki/SimpleFlatMapper-JdbcMapper-1-N-relationship)
{% highlight java %}
JdbcMapper<MyObject> mapper = 
    JdbcMapperFactory.newInstance().addKeys("id").newMapper(MyObject.class);
{% endhighlight %}

### Custom Reader

If you are not happy with the way the mapper get the value from the result set you can provide your own Getter<ResultSet, P> either on the factory attached to the column name or on the builder for a static mapping.

{% highlight java %}
JdbcMapper<MyObject> mapper = 
    JdbcMapperFactory
        .newInstance()
        .addCustomGetter("id",
            (rs) -> rs.getLong("id") + 2000).newMapper(MyObject.class);

JdbcMapper<MyObject> staticMapper = 
    JdbcMapperFactory
        .newInstance()
        .newBuilder(MyObject.class)
        .addMapping("id", 
            FieldMapperColumnDefinition.customGetter((rs) -> rs.getLong("id") + 2000))
        .mapper();

{% endhighlight %}

### Cleanup resources

The mapper does not close the result set, it is still your responsibility to close the used resource.

### Error handling
By default, if an exception is thrown during the building of the mapper, the mapping or the for each loop the error is rethrown. It is possible to provide your own handler error that will override that behaviour.

#### Field Mapping Error

{% highlight java %}
JdbcMapperFactory
    .newInstance()
    .fieldMapperErrorHandler((key, source, target, error) -> {
        System.out.println("Error ! on " + key + " " + error);
    });
{% endhighlight %}

#### Mapper Builder Error

{% highlight java %}
JdbcMapperFactory
    .newInstance()
    .mapperBuilderErrorHandler(
        new MapperBuilderErrorHandler() {
	    @Override
            public void getterNotFound(String msg) {}
	    @Override
	    public void propertyNotFound(Type target, String property) {}
	});
{% endhighlight %}

#### Row Error Handler


{% highlight java %}
JdbcMapperFactory
    .newInstance()
    .rowHandlerErrorHandler((t, target) -> System.out.println("Error ! " + t ));

{% endhighlight %}

## Crud 

### Crud on mapped object

It is now possible to create a Crud object from the metadata in the database.
You will need to provide 
 - the target object type - needs to match the table
 - the key type - needs to match the primary keys of the table
 - a connection to the db
 - the table name the object is mapped too.

It uses the metadata to detect the primary key and generated keys.

{% highlight java %}
Crud<DbObject, Long> crud = 
    JdbcMapperFactory
        .newInstance()
        .crud(DbObject.class, Long.class)
        .table(connection, "mytable");

crud.create(connection, object);
crud.read  (connection, object.getId());
crud.update(connection, object);
crud.delete(connection, object.getId());

crud.createOrUpdate(connection, object);

List<DbObject> objects = ...;
List<Long> keys = ...;

crud.create(connection, objects);
crud.read  (connection, keys, new ListCollectorHandler<>()).getList();
crud.update(connection, objects);
crud.delete(connection, keys);

crud.createOrUpdate(connection, objects);

{% endhighlight %}

### Generated keys

it is possible to get a callback with the value of the generated key.

{% highlight java %}
crud.create(connection, object, (key) -> object.setId(key));
{% endhighlight %}


see [CrudTest](https://github.com/arnaudroger/SimpleFlatMapper/blob/master/sfm-jdbc/src/test/java/org/simpleflatmapper/jdbc/test/CrudTest.java) for more samples


## NamedQuery

Because there are no available names from the PreparedStatement parameter metadata we can only builder a PreparedStatement mapper manually by adding the column name in order.
The NamedQuery is there to solve that by using named parameter - :name in place of ?. If the ? placeholder is used then it will try to extrapolate the name, that should work fine for inserts and updates. But it's not guaranteed to work perfectly on a complex select query for which it's better to use a named parameter.


{% highlight java %}
NamedSqlQuery // 1 => id
    .parse("select id, name, email, creation_time, type_ordinal, type_name "
           + " from TEST_DB_OBJECT where id = ?");

NamedSqlQuery // 1 => id, 2 => name, 3 => email, 4 => creation_time, 5 => type_ordinal, 6 => typenamed
    .parse("INSERT INTO test_db_object(id, name, email, creation_time, type_ordinal, type_name) "
           + " values(?, ?, ?, ?, ?, :typenamed)");
{% endhighlight %}

### QueryPreparer

The QueryPreparer is created from a NamedQuery. 
It is important to note that there now type information available when creating the preparer. For most 
mapping it is not an issue, but for some like Enum it will matter.
You can specify the type in the mapper factory by adding a SqlTypeColumnProperty
{% highlight java %}
JdbcMapperFactory
    .newInstance()
    .addColumnProperty("type_ordinal", SqlTypeColumnProperty.of(Types.NUMERIC));
{% endhighlight %}

### Sample

{% highlight java %}
NamedSqlQuery selectQuery = 
    NamedSqlQuery
        .parse("select id, name, email, creation_time, type_ordinal, type_name "
               + " from TEST_DB_OBJECT where id = ?");

QueryPreparer<DbObject> selectQueryPreparer =
    JdbcMapperFactory
        .newInstance()
        .from(DbObject.class)
        .to(selectQuery);

JdbcMapper<DbObject> dbObjectMapper = 
    JdbcMapperFactory
        .newInstance()
        .newMapper(DbObject.class)


try (PreparedStatement ps : selectQueryPreparer.prepare(connection).bind(dbObject);
    ResultSet rs = ps.executeQuery()) {
    dbObjectMapper.forEach(rs, System.out::println);
}
{% endhighlight %}


