---
layout: default
title: Property Mapping
short_title: Property Mapping
category: docs
description: SimpleFlatMapper java library property mapping rules
---


# The rules

The property finder will look for a column to property match first if none is found it will try to do a partial match on the property to look for a sub-property.

## property name matching

is a case-insensitive match ignoring '_' and ' '.

the following column will match the property myProperty.

* 'my_property'
* 'myproperty'
* 'my property'

## property name aliases

If the name of the property does not match the column name it is possible to specify a alias for the property using the `addAlias` method on the `MapperFactory`.

```java
mapperFactory.addAlias("columnName", "actualPropertyName");

```

### JPA @Column annotation
sfm also supports the JPA `@Column` annotation to map a property to a column.

```java
	@Column(name="id")
	private long nothingToSeeHere;
```

the column `id` will map to the `nothingToSeeHere` property. 
The annotation works on fields, getters and setters.

### Datastax @Column annotation

the sfm-datastax module provides the same functionality for the datastax @Column annotation.

## inner object matching

the inner object matching is done if the complete matching did not found any property.
The finder will try to match the start of the column to a property of the object and then will do a match against the column name left over.

for a property property.subProperty the following will match

* 'property_sub_property'
* 'propertysubproperty'
* 'property sub property'

## list/array

if the type of the object is an array or a list then the finder will look for the first number in the column and use at as the index to put the element in.
if there is a sub-property specify after the number that will be matched against the element class. If not it will look for a 1 argument constructor.

* List<String> myList, 'my_list_3' -> will match against the 3rd element of the list as a String.
* List<MyObject> myList, 'my_list_3_id' -> will match against property id of the 3rd element of the list.

## Tuples

Tuples work in the same way as list/array but the size is limited and the types can be different depending on the element.

## index discovery

If no index is found it will try to find an empty index where the element has not been injected the property yet.

ie

my_list_id, my_list_0_name, my_list_name will map to 

* myList[0].id
* myList[0].name
* myList[1].name

# Optional 

Optional properties are also supported.

the column name will map to 

{% highlight java %}
class MyObject {
  Optional<String> name;
}
{% endhighlight %}

if the name is null it will create an empty optional.

# Injection 
The property mapping will look for an injection vector in the following order

## Constructor/Factory method Injection
### Factory method
Sfm looks for a static method for which the returns type is that same as the expected type.

{% highlight java %}
class MyClass {
   public static MyClass of(String val, int index) {
   ...
   }
}
{% endhighlight %}

## Setter Injection
## Field Injection
## Builder Pattern
Sfm will look for a static method that return a type that will return the builder. A builder needs to have a method that instantiates the targeted class and have a setter method that modifies the builder or return a new version of the builder.

{% highlight java %}
class MyClass {
   public static MyClassBuilder builder() {
   ...
   }

   public static class MyClassBuilder {
        MyClassBuilder property1(String value) {}
        MyClassBuilder index(int value) {}
        MyClass build() {}
   }
}
{% endhighlight %}

It is also possible to manually specify the method/constructor to instantiate the builder.

{% highlight java %}
mapperFactory.newMapper(
    mapperFactory.getClassMetaWithExtraInstantiator(
        MyClass.class, 
        MyClassBuilder.class.getConstructor()));
{% endhighlight %}

## finals 
final field semantic is respected. The constructor injection will need the asm library to extract the name of the parameters from the debug symbol. If not present then it falls on more expensive strategy on injecting a value through the constructor and looking for a getter that returns the same value.

# Customise the property matching

Can inject your own [PropertyNameMatcherFactory](http://static.javadoc.io/org.simpleflatmapper/sfm-map/{{ site.libraryVersion }}/index.html?org/simpleflatmapper/map/PropertyNameMatcherFactory.html) in the mapper factory.

ie to use have exact and case sensitive match 

{% highlight java %}
CsvMapperFactory.newInstance().propertyNameMatcherFactory(
new DefaultPropertyNameMatcherFactory(true, true)
);
{% endhighlight %}

# Property Finder

The property matching is done by the PropertyFinder. 
{% highlight java %}

ClassMeta<MyObject> classMeta = new ReflectionService().getClassMeta(MyObject.class);
PropertyFinder<MyObject> propertyFinder = classMeta.newPropertyFinder();

PropertyMeta<MyObject, ?> prop = propertyFinder.findProperty("my_property");

{% endhighlight %}
