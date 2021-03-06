---
layout: default
title: Getting Started jOOQ
short_title: jOOQ
category: getting_started
module: jooq
description: SimpleFlatMapper java jooq RecordMapper deep object
---
# Getting Started jOOQ

{% include maven_dependency.md %}

From [7.0.0](/11-v7-migration.html) you will need explicitly include the jOOQ dependency in your pom.

Before :

If you are using jOOQ commercial edition you will need to exclude the dependency as it's currently scope compiled - will be corrected in next release.

{% highlight xml %}
<dependency>
    <groupId>org.simpleflatmapper</groupId>
    <artifactId>sfm-{{page.module}}</artifactId>
    <version>{% include currentversion.html %}</version>
    <exclusions>
        <exclusion>
            <artifactId>jooq</artifactId>
            <groupId>org.jooq</groupId>
        </exclusion>
    </exclusions>
</dependency>
{% endhighlight %}



There are 2 mapping strategies available in jOOQ.

* the generated classes from the db information. You will get a record that will have named getters matching the column names.
* the fetchInto(Class) method that will call the jOOQ mapper or a third party mapper specified in the configuration.

because fetchInto has to go through the Record creation, its use will mainly be when your query does not map to a generated object - i.e. a subset of fields or joins -.

# SFM as a RecordMapperProvider

Sfm can be plugged into the fetchInto mapping. 

All you need to do is 

{% highlight java %}
configuration.set(JooqMapperFactory.newInstance().ignorePropertyNotFound().newRecordMapperProvider()));
{% endhighlight %}

when instantiating your DSL. You will then be able to use fetchInto with a cost very close to fetching the record - see the performance section - and the flexibility that SFM offers.

# map subset of select fields

{% highlight java %}
configuration.set(JooqMapperFactory.newInstance().ignorePropertyNotFound().newRecordMapperProvider()));
{% endhighlight %}


#SFM as a RecordUnmapperProvider

From [8.2.0](https://simpleflatmapper.org/2019/11/12/v8.2.0.html) we now have a RecordUnmapper available from the JooqMapperFactory.

```java
		cfg.set(JooqMapperFactory.newInstance().newRecordUnmapperProvider(cfg));
```

### SelectQueryMapper
In [8.2.0](https://simpleflatmapper.org/2019/11/12/v8.2.0.html) we also introduced the SelectQueryMapper that can deals 
with join with minimum of effort. It will uses the jOOQ model to determined which field is a key, and also activate the
speculative object mapping removing the need for aliasing. The following will now just work out of the box: 

```java 
SelectQueryMapper<Author> authorMapper = SelectQueryMapperFactory.newInstance().newMapper(Author.class);

List<Author> authors = authorMapper.asList(DSL.using(connection)
        .select(AUTHOR.ID, AUTHOR.FIRST_NAME, AUTHOR.LAST_NAME, AUTHOR.DATE_OF_BIRTH,
                BOOK.ID, BOOK.TITLE)
        .from(AUTHOR).leftJoin(BOOK).on(BOOK.AUTHOR_ID.eq(AUTHOR.ID))
        .orderBy(AUTHOR.ID));
```

no need for key definitions or aliasing anymore.

# SFM on the ResultSet

Because the RecordMapperProvider works on the record that we don't need we are not as performant as working directly with the ResultSet. Fortunately, jOOQ allows you to fetch the ResultSet directly avoiding the cost of the transition from ResultSet to Record.

{% highlight java %}
JdbcMapper mapper = JdbcMapperFactory.newInstance().newMapper(MyObject.class);

ResultQuery<MyRecord> query = dsl.select().from(TABLE);
try (ResultSet rs = query.fetchResultSet()) {
    mapper.stream(rs).forEach(System.out::println);
}
{% endhighlight %}

That will allow you to get the query generation power of jOOQ and the close to pure Jdbc performance of SimpleFlatMapper.

# Joins

The join aggregation is only available when using SFM on the result set as the RecordMapper interface expect one object per row. 