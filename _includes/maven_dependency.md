
[![Maven Central](https://img.shields.io/maven-central/v/org.simpleflatmapper/sfm-{{page.module}}.svg)](http://search.maven.org/#artifactdetails%7Corg.simpleflatmapper%7Csfm-{{page.module}}%7C{% include currentversion.html %}%7C)
[![JavaDoc](https://img.shields.io/badge/javadoc-{{ site.libraryVersion }}-blue.svg)](http://www.javadoc.io/doc/org.simpleflatmapper/sfm-{{page.module}})

## Setting up the environment 

Add the [Dependency](http://search.maven.org/#artifactdetails%7Corg.simpleflatmapper%7Csfm-{{page.module}}%7C{% include currentversion.html %}%7C) to your build.
 
for Maven:

### Java 8, 9, 10 , 11 no module-info
{% highlight xml %}
<dependency>
    <groupId>org.simpleflatmapper</groupId>
    <artifactId>sfm-{{page.module}}</artifactId>
    <version>{% include currentversion.html %}</version>
</dependency>
{% endhighlight %}

### Java 6, 7

{% highlight xml %}
<dependency>
    <groupId>org.simpleflatmapper</groupId>
    <artifactId>sfm-{{page.module}}-jre6</artifactId>
    <version>{% include currentversion.html %}</version>
</dependency>
{% endhighlight %}

### Java 9, 10 , 11 with module-info 

{% highlight xml %}
<dependency>
    <groupId>org.simpleflatmapper</groupId>
    <artifactId>sfm-{{page.module}}-jre9</artifactId>
    <version>{% include currentversion.html %}</version>
</dependency>
{% endhighlight %}