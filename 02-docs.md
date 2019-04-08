---
layout: default
title: Docs
description: SimpleFlatMapper documentation
---

## [Property Matching](0201-property-mapping.html)
## [Converters](0202-converters.html)
## [Joins](0203-joins.html)
## [Inheritance via discriminator field](0205-inheritance-discriminator.html)
## [Google Protocol Buffers](0204-protobuf.html)

# Blog entry
* [Filling the one-to-many, many-to-many jOOQ mapping gap](https://arnaudroger.github.io/blog/2017/02/27/jooq-one-to-many.html)
* [jOOQ DTO-less one-to-many](https://arnaudroger.github.io/blog/2017/03/02/jooq-one-to-many-without-dto.html)

# Community content
* [Spring Tips : JDBC](https://spring.io/blog/2018/05/30/spring-tips-jdbc)
* [jOOQ Tips: Implementing a Read-Only One-to-Many Relationship](https://www.petrikainulainen.net/programming/jooq/jooq-tips-implementing-a-read-only-one-to-many-relationship/)
* [How to alias prefixed CSV columns to a Map with SimpleFlatMapper?](https://stackoverflow.com/questions/55541609/how-to-alias-prefixed-csv-columns-to-a-map-with-simpleflatmapper)

# modules
## Mappers

| Module | Maven | Javadoc |
|----|---:|---:|
{% include doc-table-row.md module='lightning-csv' gs='01' %}
{% include doc-table-row.md module='csv' gs='01' %}
{% include doc-table-row.md module='jdbc' gs='02' %}
{% include doc-table-row.md module='datastax' gs='03' %}
{% include doc-table-row.md module='jooq' gs='06' %}
{% include doc-table-row.md module='springjdbc' gs='04' %}
{% include doc-table-row.md module='poi' gs='05' %}
{% include doc-table-row.md module='querydsl' gs='07' %}
{% include doc-table-row.md module='sql2o' gs='08' %}
{% include doc-table-row.md module='jdbi' gs='09' %}
{% include doc-table-row.md module='jdbi3' gs='10' %}

## Libraries

|Module|Maven|Javadoc|
|----|---|---|
{% include doc-table-row.md module='converter' %}
{% include doc-table-row.md module='map' %}
{% include doc-table-row.md module='reflect' %}
{% include doc-table-row.md module='util' %}
{% include doc-table-row.md module='converter-joda-time' %}

# Google Groups
 
 <iframe id="forum_embed"
   src="javascript:void(0)"
   scrolling="no"
   frameborder="0"
   width="900"
   height="700">
 </iframe>
 <script type="text/javascript">
   document.getElementById('forum_embed').src =
      'https://groups.google.com/forum/embed/?place=forum/simpleflatmapper'
      + '&showsearch=true&showpopout=true&showtabs=false'
      + '&parenturl=' + encodeURIComponent(window.location.href);
 </script> 

# Known issues

## Spring boot issue 1.4.3

Tomcat embedded [See](https://github.com/grails/grails-data-mapping/issues/845) broke the service loaders. upgrade to last spring boot or move to jetty.

```java
2017-02-21 16:35:31.564 ERROR [http-nio-8080-exec-9] [] [dispatcherServlet]: Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Handler dispatch failed; nested exception is java.lang.NoClassDefFoundError: Could not initia
java.lang.NoClassDefFoundError: Could not initialize class org.simpleflatmapper.converter.ConverterService
        at org.simpleflatmapper.map.mapper.ConstantSourceMapperBuilder.<init>(ConstantSourceMapperBuilder.java:91)
        at org.simpleflatmapper.map.mapper.ConstantSourceMapperBuilder.<init>(ConstantSourceMapperBuilder.java:79)

...
2017-02-21 16:19:37.308 ERROR [http-nio-8080-exec-10] [] [dispatcherServlet]: Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Handler dispatch failed; nested exception is java.util.ServiceConfigurationError: org.simple
        java.io.FileNotFoundException: JAR entry !/META-INF/services/org.simpleflatmapper.converter.ConverterFactoryProducer not found in /tmp/jar_cache2379861323892080478.tmp


```