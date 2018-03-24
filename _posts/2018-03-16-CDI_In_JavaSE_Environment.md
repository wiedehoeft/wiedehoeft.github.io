---
author: Christian Wiedehöft
title: (Context) Dependency Injection in a Java SE Environment
date: 2018-03-16
layout: default
comments: true
---

# Setting up (Context) Dependency Injection with Weld, Guice and/or HK2

Last week I tried to setup Dependency Injection in one of our Dropwizard services. Because it was not as trivial as expected,
I created a separate project for this task and tried different frameworks.

Starting point was a default gradle project with some classes. The class where bootstrapping should start is `Person.java`:
```
public class Person {

  private Adress adress;
  
  @Inject
  private IPrinter printer;

  public Person(IPrinter printer) {
    this.adress = adress;
    this.printer = printer;
  }

  public void setAdress(Adress adress) {
    this.adress = adress;
  }

  public void print() {
    printer.print("Initialized with adress: " + adress);
  }
}
```

It has an interface which should be injected with an implementation at runtime and an adress which should be printed by injected class.
For testing purpose the class will be used in Main-method and a JUnit test, which is executed by gradle-build (stay tuned, these differences will matter!).

`PersonTest.java`
```
public class PersonTest {

  @Mock
  private IPrinter printer;

  @Test
  public void testPersonPrinter() throws Exception {

    // Given
    MockitoAnnotations.initMocks(this);
    Person person = new Person(printer);
    person.setAdress(new Adress(12345, "la Rue"));

    // When
    person.print();

    // Then
    verify(printer).print("Initialized with adress: model.Adress{postalCode=12345, road='la Rue'}");
  }
}
```

`Main.java`
```
public class Main {

  public static void main(String[] args) {
    //bootstrapWithGuice();
    //bootstrapWithWeld();
    //bootstrapWithHk2();
  }

  private static void printPersonDetails(Person person) {
    person.setAdress(new Adress(12345, "Rue de la rue"));

    person.print();
  }
}
```

The application should be bootstrapped by the different frameworks and then the adress of the person should be printed by injected printer.
To get all classes of project clone this [Github Project] [] -branch.

[Github Project]: https://github.com/wiedehoeft/TemplatesSnipptesAndExperiments/tree/cdiInJavaSeEnv


## Dependency injection with Guice
Caused by most experience with Guice I started with it. The solution was straight forward.
Append to `build.gradle`

```
    compile group: 'com.google.inject', name: 'guice', version: '4.2.0'
```

Code in main class:
```
  /**
   * For Guice setup see
   * https://github.com/google/guice/wiki/GettingStarted
   */
  private static void bootstrapWithGuice() {
    Injector injector = Guice.createInjector(new PersonModule());
    Person person = injector.getInstance(Person.class);

    printPersonDetails(person);
  }
```

The `PersonModule.java`:
```
import com.google.inject.AbstractModule;

public class PersonModule extends AbstractModule {

  @Override
  protected void configure() {
    bind(IPrinter.class).to(AdressPrinter.class);
  }
}
```

That's it. Guice will now bootstrap all binded classes and try to inject constructors and/or fields with new objects when possible.

**Advantages**
* Easy and straightforward to use
* Good documentation
* Flat learning curve

**Drawbacks:**
* Not JSR 365-compatible: Google has its complete own way for DI. Just the `@Inject` and `@Provide`-annotation can be used from `javax`
* No **Context** Dependency injection
* Needs a Guice bridge in Dropwizard projects

## HK2
Because HK2 is already used in Dropwizard projects it seems to be obviously to use it.

This is the needed setup:

`build.gradle`
```
    compile group: 'org.glassfish.hk2', name: 'hk2', version: '2.4.0'
```

`Main class`
```
  /**
   * For HK2-setup, see
   * https://javaee.github.io/hk2/inhabitant-generator.html and
   * https://javaee.github.io/hk2/getting-started.html
   */
  private static void bootstrapWithHk2() {

    ServiceLocator serviceLocator = ServiceLocatorUtilities.createAndPopulateServiceLocator();
    Person person = serviceLocator.getService(Person.class);

    printPersonDetails(person);
  }
```

HK2 needs additional annotations for bootstrapping. Interfaces are annotated with `@Contract` and objects where dependencies should be injected
needs to be annotated with `@Service`.
Unfortunately this is not all.
HK2 expects a file named `default` under `src/main/resources/META-INF/hk2-locator` for a correct bootstrapping process.
This file is not created by the developer (a fact which was quite hard to find) but created by HK2 metadata generator.

The easiest solution is to add it to `build.gradle`
```
    // HK2: Metadata-generator
    compile group: 'javax.inject', name: 'javax.inject', version: '1'
    compile group: 'org.glassfish.hk2', name: 'hk2-utils', version: '2.4.0-b14'
    compile group: 'org.glassfish.hk2', name: 'hk2-api', version: '2.4.0-b14'
    compile group: 'org.glassfish.hk2', name: 'hk2-metadata-generator', version: '2.4.0-b14'
```

While running `build.gradle` the generator will analyze the compiled classes for `@Service` and `@Contract` annotations and generate
the file in `build/classes/java/main/META-INF/hk2-locator/default`.
After copying this file to `src/main/resources/META-INF/hk2-locator` the bootstrapping process works as expected.

**Advantages**
* Should be usable in Dropwizard without glue code; currently not tested

**Drawbacks**
* Bad documentation, e.g. a table of contents is completely missing
* The annotations are HK2 specific
* Needs separate configuration file
* Unclear how to include this solution to Dropwizard project

## Weld
The last solution is Weld. It is CDI 2.0 compatible in its newest version and e.g. the reference implementation for JBoss application server.
The integration seemed straightforward.

Add to `build.gradle` (be careful to take JavaSE-version)

```
compile "org.jboss.weld.se:weld-se:2.4.6.Final"
```

Add to `Main class`
```
  /**
   * For weld setup,see
   * https://stackoverflow.com/questions/45174989/building-with-intellij-2017-2-out-directory-duplicates-files-in-build-director and
   * https://issues.jboss.org/browse/WELD-2040 and
   * http://docs.jboss.org/weld/reference/latest/en-US/html_single/#_java_se
   */
  private static void bootstrapWithWeld() {
    WeldContainer container = new Weld().initialize();
    Person person = container.select(Person.class).get();
    printPersonDetails(person);
    container.shutdown();
  }
```

The classes to be injected have to be annotated with `@ApplicationScoped`. We do not need a scope in JavaSE environment, but it is needed to make
Weld-CDI inspect our classes.

Weld needs a configuration file like HK2, named beans.xml. It has to be placed in `src/main/resources/META-INF` and can have e.g. following content
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:weld="http://jboss.org/schema/weld/beans" bean-discovery-mode="all"
       xmlns="http://xmlns.jcp.org/xml/ns/javaee">
    <weld:scan>
        <weld:include name="model.**"/>
    </weld:scan>
</beans>
```

This is were trouble with gradle and IntelliJ starts.
Because Weld only scans sources in directory where the `beans.xml` is located, it will not find any source files in IntelliJ or while gradle build.

**Solution for IntelliJ**

Copy the `META-INF`-folder from `out/resources` to `out/classes`.

**Solution for Gradle**

Like in IntelliJ the file has to placed at different location. Append following lines to `build.gradle`:
```
sourceSets {
    main {
        output.resourcesDir = 'build/classes/main'
    }
}

configurations {
    integrationTestCompile.extendsFrom testCompile
    integrationTestRuntime.extendsFrom testRuntime
}
```

When working with additional source sets. the setup is even more complex:
```
sourceSets {
    integrationTest {
        java {
            compileClasspath += main.output + test.output
            runtimeClasspath += main.output + test.output
            srcDir file('src/test/integration/java')
        }
        resources {
            srcDir file('src/test/integration/resources')
        }

        // Workaround for Weld
        output.resourcesDir = 'build/classes/java/integrationTest'
    }
    // Workaround for Weld
    main {
        output.resourcesDir = 'build/classes/main'
    }
}

configurations {
    integrationTestCompile.extendsFrom testCompile
    integrationTestRuntime.extendsFrom testRuntime
}

task integrationTest(type: Test) {
    testClassesDirs = sourceSets.integrationTest.output
    classpath = sourceSets.integrationTest.runtimeClasspath
    outputs.upToDateWhen { false }

    // Workaround for Weld
    doFirst {
        copy {
            from "build/classes/java/integrationTest/META-INF/beans.xml"
            into "build/classes/java/main/META-INF"
        }
    }
}
```

**Advantages**
* Can be used in Dropwizard without special glue code
* JSR 365 compatible
* Good documentation

**Disadvantages**
* Needs complex setup when used with Gradle
* Needs additional setup when used in IntelliJ
* Needs extra configuration file

# Conclusion
There is no one fit it all solution. But in JavaSE environment Guice seems to be the easiest solution which works out of the box.
Next step will be to integrate the three solutions in Dropwizard project.

{% if page.comments %}
<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://wiedehoeft-github-io.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
{% endif %}
