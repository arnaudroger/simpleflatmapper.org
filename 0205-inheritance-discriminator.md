---
layout: default
title: Inheritance via discriminator field
short_title: Inheritance
category: docs
description: SimpleFlatMapper Inheritance via discriminator field
---

Since version [6.0.1](/2018/10/10/v6.0.0.html) - effectively from 6.0.0 but the following doc is for 6.0.1 - SimpleFlatMapper support inheritance via discriminator field at any level of the object tree for all mappers - csv, jdbc etc... -.


To configure the inheritance you will just need to call one of the discriminator method on the [`XXXXMapperFactory`](http://static.javadoc.io/org.simpleflatmapper/sfm-map/6.0.0/org/simpleflatmapper/map/mapper/AbstractMapperFactory.html#discriminator-java.lang.Class-org.simpleflatmapper.util.Consumer-).

For example

```java
JdbcMapper<Person> mapper =
        JdbcMapperFactoryHelper.asm()
                .addKeys("id", "students_id")
                .discriminator(Person.class, 
                            "person_type", 
                            ResultSet::getString, 
                            builder -> 
                                builder
                                    .when("student", Student.class)
                                    .when("professor", Professor.class)
                )
                .newMapper(Foo.class);
```

That will create a mapper that will either create a `Student` object when the `person_type` column is equals to `"student"` or a `Person` when equals to `"professor"`.

It is also possible to use a Predicate instead of a value to match.

```java
JdbcMapper<Person> mapper =
        JdbcMapperFactoryHelper.asm()
                .addKeys("id", "students_id")
                .discriminator(
                            Person.class, 
                            "person_type", 
                            ResultSet::getString, 
                            builder -> builder
                                .when(v -> "student".equals(v), Student.class)
                                .when(v -> true, Professor.class)
                )
                .newMapper(Foo.class);
```

In that case it will instantiate a `Student` when `person_type` is `"student"` otherwise it will default to Professor.

If you need to discriminate on more that one column you will need to use the Predicate<S>.

```java

JdbcMapper<Person> mapper =
        JdbcMapperFactoryHelper.asm()
                .addKeys("id", "students_id")
                .discriminator(
                        Person.class, 
                        builder -> 
                            builder
                                .when(rs -> {
                                    try {
                                        return 
                                            "student"
                                                .equals(rs.getString("person_type"));
                                    } catch (Exception e) {
                                        return ErrorHelper.rethrow(e);
                                    }
                                }, Student.class)
                                .when(rs -> true, Professor.class)
                )
                .newMapper(Foo.class);
``` 