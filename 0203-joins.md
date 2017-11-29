---
layout: default
title: Joins
short_title: Joins
category: docs
description: SimpleFlatMapper java library joins one to many mapping
---

SimpleFlatMapper can aggregate join objects in `List`s or `Map`s. The CSV mapper still has limitation and 
can only handle 1 join as it relies on id changes to create new objects, with more than 1 joins the second joins id can't be ordered continuously and that will create duplicates.
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

For the aggregation to work you will need to be sure to order by the root object - ORDER BY u.id - and also 
tell the mapper what are the keys of the different objects. Also, it is better to specify all the keys if none is specified the mapper will assume that each row will create a different object.
There are two ways to specify the keys the first one is on the MapperFactory, like the following with a JDBC mapper.
```java
    JdbcMapper<Object> jdbcMapper = 
            JdbcMapperFactory
                .newInstance()
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

The aggregation can deal with any number of inner level, and any number like the following - except for CSV -:

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