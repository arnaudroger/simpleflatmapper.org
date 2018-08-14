---
layout: default
title: Getting Started Csv
short_title: Csv
category: getting_started
module: csv
description: SimpleFlatMapper java Csv Parser Csv Mapper
---

# Version 4 changes

the following code should still work with version 4.
The only difference is the lightning-csv module is lighter and does not contains any mapping related code. 

# Why?

Are you looking for a very [performant](http://simpleflatmapper.org/12-csv-performance.html) Csv Parser, 
or even better an easy to use CsvMapper? 

sfm-csv provides the most [flexible](0201-property-mapping.html) CsvMapper available. 
It supports Constructor injection, Factory method, Builder Pattern... The new java8 time API, the old joda time API.
Inner object mapping, join mapping.
 
Give it a try.
 
# Getting Started Csv

{% include maven_dependency.md %}

# Reading a csv file

The [`CsvParser`](http://static.javadoc.io/org.simpleflatmapper/sfm-csv/{% include currentversion.html %}/index.html?org/simpleflatmapper/csv/CsvParser.html) api allows you to read from a `File`, a `Reader` or a `CharSequence`
via a [`CheckedConsumer`](http://static.javadoc.io/org.simpleflatmapper/sfm-util/{% include currentversion.html %}/index.html?org/simpleflatmapper/util/CheckedConsumer.html) callback, 
an `Iterator` or a `Stream` of `String[]`

[Source](https://github.com/arnaudroger/SimpleFlatMapper/blob/master/sfm-csv/src/test/java/org/simpleflatmapper/csv/test/samples/GettingStartedCsv_csvParser.java)
{% highlight java%}
// Callback
CsvParser
        .forEach(file, row -> System.out.println(Arrays.toString(row)));

// Iterator
try (CloseableIterator<String[]> it = CsvParser.iterator(file)) {
    while(it.hasNext()) {
        System.out.println(Arrays.toString(it.next()));
    }
}

// Stream
try (Stream<String[]> stream = CsvParser.stream(file)) {
    stream.forEach(row -> System.out.println(Arrays.toString(row)));
}
{% endhighlight %}

## Mapping a csv to an object

You can also ask the row to be mapped to an object. You can then read the csv from a `File`, a `Reader` 
or a `CharSequence` via a [CheckedConsumer](http://static.javadoc.io/org.simpleflatmapper/sfm-util/{% include currentversion.html %}/index.html?org/simpleflatmapper/util/CheckedConsumer.html) callback, 
an `Iterator` or a `Stream` of your type.
The mapper will use the header row - the first one - to match against the property of the object. You can also specify the headers
manually if there none or if you want to skip them.

[Source](https://github.com/arnaudroger/SimpleFlatMapper/blob/master/sfm-csv/src/test/java/org/simpleflatmapper/csv/test/samples/GettingStartedCsv_csvMapper.java)
{% highlight java %}
// Callback
CsvParser
        .mapTo(MyObject.class)
        .forEach(file, System.out::println);

// Iterator
try (CloseableIterator<MyObject> it =
             CsvParser.mapTo(MyObject.class).iterator(file)) {
    while(it.hasNext()) {
        System.out.println(it.next());
    }
}

// Stream
CsvParser
        .mapTo(MyObject.class)
        .stream(
            file, 
            (s) -> { s.forEach(System.out::println); return null; }
        )

// override headers
CsvParser
        .skip(1)
        .mapTo(MyObject.class)
        .headers("id", "email", "name")
        .forEach(file, System.out::println);
{% endhighlight %}

### Customizing the date format

By default it will use parse the date using a `"yyyy-MM-dd HH:mm:ss"` formatter.
That obviously won't work for everybody and it is possible to override it on a column by column basis.

```java
// overrides the default format
CsvParser
        .mapWith(
                CsvMapperFactory
			.newInstance()
			.defaultDateFormat("yyyy-MM-dd")
			.newMapper(MyClass.class))
        .forEach(file, System.out::println);

// overrides the format for a specific column
CsvParser
        .mapWith(
                CsvMapperFactory
                        .newInstance()
			.addColumnProperty("my_date_col", new DateFormatProperty("yyyy-MM-dd"))
                        .newMapper(MyClass.class))
        .forEach(file, System.out::println);

// overrides the format for any column with date in the name 
CsvParser
        .mapWith(
                CsvMapperFactory
                        .newInstance()
                        .addColumnProperty(k -> k.getName().contains("date"), new DateFormatProperty("yyyy-MM-dd"))
                        .newMapper(MyClass.class))
        .forEach(file, System.out::println);
```

## Writing a csv from an object

The [CsvWriter](http://static.javadoc.io/org.simpleflatmapper/sfm-csv/{% include currentversion.html %}/index.html?org/simpleflatmapper/csv/CsvWriter.html) allows you to create append object to an [Appendable](https://docs.oracle.com/javase/8/docs/api/index.html?java/lang/Appendable.html).
If no headers are specified it will generate a list of headers from the properties of the object. Though it is
better to specify the headers manually.

[Source](https://github.com/arnaudroger/SimpleFlatMapper/blob/master/sfm-csv/src/test/java/org/simpleflatmapper/csv/test/samples/GettingStartedCsv_csvWriter.java)
{% highlight java%}
// better to cache the dsl with the from 
// to avoid recomputing the object metadata
CsvWriter.CsvWriterDSL<MyObject> writerDsl =
    CsvWriter.from(MyObject.class).columns("id" ,"name", "email");

public void writeCsv(Collection<MyObject> objects, File file) 
                                                throws IOException {
    try (FileWriter fileWriter = new FileWriter(file)) {
        CsvWriter<MyObject> writer=
                writerDsl.to(fileWriter);
        objects.forEach(CheckedConsumer.toConsumer(writer::append));
    }
}
{% endhighlight %}

# writing with headers not matching the property name

if you want to use a header that does not match the name of the property,
for example, if you need the email header to be "contact" you will need to 
add an alias by adding RenameProperty on the column.

{% highlight java%}
// better to cache the dsl with the from 
// to avoid recomputing the object metadata
CsvWriter.CsvWriterDSL<MyObject> writerDsl =
    CsvWriter
        .from(MyObject.class)
        .columns("id" ,"name")
        .column("contact", new RenameProperty("email"));

public void writeCsv(Collection<MyObject> objects, File file) 
                                                throws IOException {
    try (FileWriter fileWriter = new FileWriter(file)) {
        CsvWriter<MyObject> writer=
                writerDsl.to(fileWriter);
        objects.forEach(CheckedConsumer.toConsumer(writer::append));
    }
}
{% endhighlight %}
