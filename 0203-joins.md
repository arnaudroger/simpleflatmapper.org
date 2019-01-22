---
layout: default
title: Joins
short_title: Joins
category: docs
description: SimpleFlatMapper java library joins one to many mapping
---


SimpleFlatMapper can aggregate join objects in `List`s or `Map`s. 
A root object is created on id change, so it is important to have the query ordered by fields of the root object and the id, otherwise you might end up with multiple instance of the same id.

let's say you have the followings tables

* User
  * id - the key
  * name
* Role
  * user_id foreign key to user(id)
  * name
* Phone
  * user_id foreign key to user(id)
  * number

The following SQL query will retrieve each user with his roles and Phone numbers 

```sql
SELECT u.id as id, u.name as name, 
    r.id as roles_id, r.name as roles_name, 
    p.id as phones_id, p.number as phones_number
FROM user u 
    left join role r on r.user_id = u.id
    left join phone p on p.user_id = r.id
ORDER BY u.id
```

It can be map to the following object 

```java
class User {
    List<Role> roles;
    List<PhoneNumber> phones;
}
```

By default for the aggregation to work you will need to be sure to order by the root object - ORDER BY u.id - and also 
tell the mapper what are the keys of the different objects. Also, it is better to specify all the keys if none is specified the mapper will assume that each row will create a different object.
There are two ways to specify the keys the first one is on the MapperFactory, like the following with a JDBC mapper.


```java
    JdbcMapper<Object> jdbcMapper = 
            JdbcMapperFactory
                .newInstance()
                .addKeys("id", "roles_id", "phones_id")
                .newMapper(Object.class);
```

If you cannot order by id, or another field of the root object, you can since [6.2.0]() enable the `unorderedJoin()` feature.
It is more expensive and will mean all the object will need to be prefetch.

```java
    JdbcMapper<Object> jdbcMapper = 
            JdbcMapperFactory
                .newInstance()
                .unorderedJoin()
                .addKeys("id", "roles_id", "phones_id")
                .newMapper(Object.class);
```



The second way is to annotate the object with @Key on the field, the setter or the getter.

```java
class User {
    @Key
    int id;
}
```

The aggregation can deal with any number of inner level, and any number like the following:

```java
class Root {
    List<Child> children;
    List<Car> cars;
}

class Child {
    List<Toy> toys;
}

class Toy {
    List<Language> laguages;
}
```

The join support might be limited third party framework integration if it expect one object per row, check the specific docs for more detail.


# Null elimination - Just added

 For a Foo { List<Bar> }. you only need to specify the id of Foo for the aggregation to work. But if you do a left join and the Bar value are null
 sfm will not know if the object Bar is null or if it's a Bar object with null values. If you specify the bar id as a key then sfm will consider Bar to be null if the key is null.
 
 
 |id|name|bars_id|bars_name|
 |----|---|---|----|
 |1|foo|null|null|
 
with
```java
JdbcMapperFactory.newInstance().addKeys("id").newMapper(Foo.bar);
```
will return `{ id : 1, name : 'foo', bars : [ { id : null, name :null } ] }`

but with 

```java
JdbcMapperFactory.newInstance().addKeys("id", "bars_id").newMapper(Foo.bar);
```
it will return `{ id : 1, name : 'foo', bars : [] }`
