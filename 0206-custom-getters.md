---
layout: default
title: Converters
short_title: Converters
category: docs
description: SimpleFlatMapper java library converters
---

When mapping a property sfm will looking for a getter/setter from the object
to the source/target flat structure - ResultSet, Csv Row, etc ...-. If it can't find 
one it will then look for a Converter that gets transform to a supported type.

the default converter or in 

{% include doc-table-row.md module='converter' %}

the joda time one in 

{% include doc-table-row.md module='converter-joda-time' %}

the protobuf in 

{% include doc-table-row.md module='converter-protobuf' %}

It's also possible to supply your own Converter<I, O> for a specific field by adding a ConverterProperty.

```java 
    JdbcMapperFactory.newInstance()
        .addColumnProperty("my_enum_column", ConverterProperty.of(MyEnum::factoryMethod))
        .newMapper(MyClass.class);
```

you can also provide your converters using the [`ServiceLoader`](https://docs.oracle.com/javase/8/docs/api/java/util/ServiceLoader.html)

1. Create a [`org.simpleflatmapper.converter.ConverterFactoryProducer`](http://static.javadoc.io/org.simpleflatmapper/sfm-converter/3.7/index.html?org/simpleflatmapper/converter/ConverterFactoryProducer.html)
```java
public class MyConverterFactoryProducer extends AbstractConverterFactoryProducer {

    @Override
    public void produce(Consumer<? super ConverterFactory<?, ?>> consumer) {
        constantConverter(consumer, Date.class, MyType.class, new DateToMyTypeConverter());
    }
}
```
2. register the service in the `META-INF/services/org.simpleflatmapper.converter.ConverterFactoryProducer file`
```
mypackage.MyConverterFactoryProducer
```

Note that providing a Converter does not guarantee it will be the one being used. The ConverterService will elect one according to path length and type matching.
I you want to predictably changed the way a property is instantiated you will need to set a [custom getter for that type](0206-custom-getters.html) 