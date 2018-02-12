---
author: Christian Wiedehöft
title: Creating acceptance tests with Gradle, Cucumber and Fit
date: 2018-02-12
layout: default
---

In this post we will setup gradle to run acceptance tests
in a specific gradle tasks. We will have a brief look at Fit
and Cucumber and will write and run our first test with
both frameworks.

### Expected knowledge base
* Gradle and Gradle project structure
* Git and Github
* Java, JUnit, Mockito

### Basic setup
At first we need a new empty gradle project.
After the setup the structure should look like this

![Intellij project structure](/assets/img/AcceptanceTests_InitialFolderStructure.png "InitialFolderStructure")

I like to start with a build.gradle like this:
```
apply plugin: 'java'
apply plugin: 'eclipse'

repositories {
    jcenter()
}

eclipse {
	classpath {
		downloadJavadoc = true
		downloadSources = true
	}
}

sourceCompatibility = 1.8
version = '1.0'

dependencies {
	testCompile 'org.assertj:assertj-core:3.8.0'
	testCompile 'junit:junit:4.12'
	testCompile "org.mockito:mockito-core:2.8.47"
}
```

It is possible to import this project in eclipse after `./gradlew eclipse` and to Intellij, which should auto-build the Gradle project.
We also have a minimal dependency set for creating Unit-Tests. But Unit-Tests won't be in scope for this article.
Instead I will describe in the next to sections how to setup the project for creating Acceptance-Tests. First with Fit and after that
with Cucumber.

### Setup for Fit
At first I will answer a question some readers might have: why Fit, or what is Fit?
[Fit] is an extreme simple and lightweight Acceptance-Testing-"Framework" by [Ward Cunningham] and the ancestor of [FitNesse], a very popular testing framework today.
I will start with Fit because in my opinion it shows best how simple it can be to start writing Acceptance-Tests. There is no need of much infrastructure
or complex test specification, instead we just need a Jar, some HTML files and glue code to manage our application.



[Fit]: http://fit.c2.com/
[Fitnesse]: http://docs.fitnesse.org/FrontPage
[Ward Cunningham]: http://fit.c2.com/wiki.cgi?WardCunningham




At first we need to add the jar to the application. Currently it is only hosted at Maven-Central, so we need to edit the repository
specification, too:
```
repositories {
    jcenter()
    mavenCentral()
}

[...]

dependencies {
    [...]
    testCompile 'com.c2.fit:fit:1.1'
}
```

Because acceptance tests might me even slower than unit-tests we will create a separate gradle build-step for them and don't run the tests, while
default gradle build.

For these, we need the following changes in our project:
At first create a new folder under `test/` named `fit`. Here we will place our test specification and fixture code later.
Because this differs from default gradle project structure, we need to change the build.gradle-file.

First, we declare a new source folder:

```
sourceSets {
    integrationTest {
        java {
            compileClasspath += srcDirs 'src/test/fit'
        }
    }
}
```

Because in real life applications we want to have access to the application under test, we need to extend the classpath for the new source set:
```
configurations {
    integrationTestCompile.extendsFrom testCompile
    integrationTestRuntime.extendsFrom testRuntime
}
```

Now we are ready to write our first tests, but before we will create a separate gradle build step to launch them:
```
task fit(type: JavaExec) {
    classpath = sourceSets.integrationTest.runtimeClasspath

    main = 'fit.FileRunner'

    args 'src/test/fit/alltests.html', 'src/test/fit/alltests-result.html'
}
```

Because Fit has no gradle runner plugin we create a separate gradle step which executes the Fit-FileRunner class from the Fit-Jar.
Before we start the gradle build step place the specified files `alltests.html` and `alltests-result.html` in the fit test folder we created before.

After that we are ready to execute the gradle step the first time: type `./gradlew fit` to launch tests.
This should give following error:
```
FAILURE: Build failed with an exception.
   ./gradlew fit
   :compileIntegrationTestJava NO-SOURCE
   :processIntegrationTestResources NO-SOURCE
   :integrationTestClasses UP-TO-DATE
   :fit
   0 right, 0 wrong, 0 ignored, 1 exceptions
   :fit FAILED
   
   FAILURE: Build failed with an exception.
```

Open the `alltests-result.html` in Browser and you will see that we correctly launched the Fit FileRunner, but it was unable to parse our HTML-file:
![](/assets/img/Fit_Test_Error_No_TestTable.png "FitTestError")

### Implement first Fit Acceptance-Test
Copy the following content to `alltests.html`:
```
<html>
<head>
    <title> FIT-Tests </title>
    <meta charset="utf-8"/>
</head>
<body>

<h1>Manage DVD store inventory</h1>
<p>Create new title in DVD store. We need title of DVD and price category. The number for every movie is created by system in ascending order.</p>

<table border cellspacing="0" cellpadding="3">
    <tr><td colspan="3">fit.ActionFixture</td></tr>
    <tr> <td> start </td> <td colspan="2"> MovieAdministration </td> </tr>
    <tr> <td> press </td> <td colspan="2"> new movie </td> </tr>
    <tr> <td> enter </td> <td> movie title </td> <td> Pulp Fiction </td> </tr>
    <tr> <td> enter </td> <td> price category </td> <td> regular price </td> </tr>
    <tr> <td> check </td> <td> movie number </td> <td> 1 </td> </tr>
</table>

</body>
</html>
```

The great thing with acceptance test is that it does not need further explanation. The test describes what the task is, just open our
description in Browser.
![](/assets/img/Fit_Testcase.png "Fit Test-Case Description")


We create a DVD Store, where we want to be able to add new Movies to the inventory.
I will not go deep to Fit specific things like the `fit.ActionFixture`, this would be an other complete blog article and Frank Westphal 
still wrote a great description (see linked PDF below). In short it is
a class from Fit which parses every table row and executes the specified command. The commands in row one are defined by Fit and needs to
be overwritten from us to specify the scenario specific behaviour.

It's time to create our Fixture class where we place our test code. You can name it e.g. 'MovieAdministration.java'. I always prefer class names
specific to the problem domain, because every developer who needs to work with our code is able to find the needed classes when she/he needs
to add new behaviour to our code.

After creating the empty Java class execute the tests again.
Still an exception, but we move forward; refresh the `alltests-result.html` page in Browser. It should look like this
![](/assets/img/Fit_Test_Error_CantCastToFixture.png "FitTestError_CantCastToFixture")

The new test class has to extend the Fit-Fixture class:
```
import fit.Fixture;

public class MovieAdministration extends Fixture {
[...]
```

We also have many NPE, because Fit expects a method for our test description in column 2. Fit delivers the code like `press` and `enter`, but we have
to deliver the code, how Fit has to behave in this scenario.
Add following to the class:
```
private int movieNumber;
  private String movieTitle;
  private String priceCategory;

  public void newMovie() {

  }

  public int movieNumber() {
    return 1;
  }

  public void movieTitle(String title) {
    movieTitle = title;
  }

  public void priceCategory(String category) {
    priceCategory = category;
  }
```

Now run tests again. They should succeed now, because the enter rows don't really test something.
The only test we currently have is the `check` row. It checks if movie has No. 1. The value is currently returned hard coded. 
![](/assets/img/Fit_Test_Success.png "FitTestError_CantCastToFixture")

When changing it the test will fail.
![](/assets/img/Fit_Test_Failure.png "FitTestError_CantCastToFixture")

From this point you are ready to write Acceptance-Tests with Fit and launch them in your build pipeline with Gradle. For a 
deeper look to Fit I recommend the [Free PDF from Frank Westphal]. It gives a great introduction in Testing Java applications.

[Free PDF from Frank Westphal]: http://www.frankwestphal.de/ftp/Westphal_Testgetriebene_Entwicklung.pdf



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