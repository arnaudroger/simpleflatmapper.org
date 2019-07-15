---
layout: default
title: Custom Getters
short_title: Custom Getters
category: docs
description: SimpleFlatMapper java library Custom Getters
---


Sometime sfm will not find a way to instantiate a property from the source - ResultSet, Row ... - to the property type, or you might want to override the default strategy - for example for enums that don't use the enum name or the ordinal value.

when that happens you can specify a custom getter for a type.


```java 
JdbcMapperFactory.newInstance()
    .addGetterForType(
        MyEnum.class, 
        (rs, i) -> {
            String val = rs.getString(i);
            if (val != null) 
                switch (val) {
                    case "enum1": return MyEnum.VALUE_1;
                }
            return null;
        }
    ).newMapper(MyClass.class);

```

if you want the getter override to only apply to a specific set of field, for example to trim a string only for some column.

```java 
JdbcMapperFactory.newInstance()
        .addColumnProperty(
            "trimmed",
            GetterFactoryProperty.<ResultSet, String>forType(
                String.class, 
                (rs, i) -> {
                    String val = rs.getString(i);
                    if (val != null) return val.trim();
                    return null;
                }
            )
        ).newMapper(MyClass.class);
```

Note that the generic type information on GetterFactoryProperty.forType cannot be inferred by the compiler and need to be provided manually.