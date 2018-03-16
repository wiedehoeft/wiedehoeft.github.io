---
author: Christian Wiedehöft
title: Daily blog
date: 2018-01-22
layout: default
---

### 16.03.2018
**Most important topics since last entry**
* Finished TestDrivenDevelopment with JUnit & Fit
* Started Professional Javascript development by Philip Ackermann
* Created Showcase-Project for (Context) Dependency Injection in Java SE environment with Weld (Gradle + IntelliJ), HK2 and Guice
  * Weld: Needed workarounds for Gradle and IntelliJ 
* Append different Gradle Source-Sets to Sonarqube analysis
  * Test coverage is taken from Jacoco plugin which is appended by additional source sets (First property)
  * Test classes should be shown in Sonarqube, too (Second property) which is currently not working
  * Example code of `build.gradle`
  ```
    sonarqube() {
        properties {
            property "sonar.jacoco.reportPaths", ["$buildDir/jacoco/test.exec", "$buildDir/jacoco/integrationTest.exec"]
            // TODO: Why is not this working?, see doc at https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner+for+Gradle
            // and StackOverflow at https://stackoverflow.com/questions/49128969/sonarqube-does-not-show-tests-from-different-gradle-source-sets
            properties["sonar.tests"] += sourceSets.integrationTest.allSource.srcDirs.findAll({ it.exists() })
        }
    }    
  ```
* For creating additional source sets in Gradle, see [UsefulLinks]({{ site.baseurl }}{% link _posts/2017-12-30-UsefulLinks.md %})  

### 22.01.2018
Upload project for exploring book examples.
Current book is [TestDrivenDevelopment With JUnit & Fit] [] from [Frank Westphal] [].

**Outstands**
* Object oriented design while TDD
* Study JUnit-Framework Code!
* Sort testcases around the testfixture
* Interface substitution by Tammo Freese
* Try JUnit-cardiogram
* Lookup further literature
    * [Domain Driven Design] from Eric Evans
    * [Refactorings from big software projects] from Stefan Roock and Martin Lippert
    * [Is Design dead] from Martin Fowler
    * [Refactoring] from Kent Beck and Martin Fowler] 
* Look for some material for REPL-testing

[TestDrivenDevelopment With JUnit & Fit]: https://github.com/wiedehoeft/bookexamples/tree/testDrivenDevelopmentWithJUnitAndFit
[Frank Westphal]: http://www.frankwestphal.de/
[Refactorings from big software projects]: https://www.amazon.de/Refactorings-grossen-Softwareprojekten-Restrukturierungen-erfolgreich/dp/3898642070
[Refactoring]: https://www.amazon.de/Refactoring-Improving-Existing-Addison-Wesley-Technology-ebook/dp/B007WTFWJ6/ref=sr_1_2?s=books&ie=UTF8&qid=1516642349&sr=1-2&keywords=martin+fowler+refactoring
[Domain Driven Design]: https://www.amazon.de/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215/ref=pd_sim_14_1?_encoding=UTF8&pd_rd_i=0321125215&pd_rd_r=JVZ0ABCKBHXAZT8XFNM5&pd_rd_w=S9UUa&pd_rd_wg=U5nAU&psc=1&refRID=JVZ0ABCKBHXAZT8XFNM5
[Is Design dead]: http://martinfowler.com/articles/designDead.html

### 15.01.2018

Added two branches in TemplatesSnippetsAndExperiments
* One for [exploring apache commons] []
* One for [exploring simplest observer pattern] []

[exploring apache commons]: https://github.com/wiedehoeft/TemplatesSnipptesAndExperiments/tree/exploreApacheCommons
[exploring simplest observer pattern]: https://github.com/wiedehoeft/TemplatesSnipptesAndExperiments/tree/exploreMvcPattern 