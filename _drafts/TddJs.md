---
author: Christian Wiedehöft
title: Test-Driven-Javascript development
date: 2017-10-25
layout: default
---

As java middleware developer we expect our IDE to support us while writing automatic
Unit-Tests, for our Test-Driven-Development-Workflow (TDD). Eclipse or Intellij and JUnit work side by side for a fast development workflow, where tests verfiy our implemented business logic.

While using Javascript for SPA development, we found some leaks in our
TDD workflow. Some were caused by the IDE (Eclipse) and some by the
new programming language and extra frameworks like Angular2.

Angular2 uses Karma for running tests, this seems to be an unnecessary workaround, while just testing business logic. Everytime we wrote a test and want to execute it, we need to wait until our project is build and executed by Karma. For a better view to our test results, we also have to switch to our browser every time, tests were executed.

An other way is to use Intellij with some plugins, to implement our business logic in fast Test-Driven-Development-Workflow. That means:
* Create a testcase in IDE
* Execute tests with IDE-shortcut
* See test results in IDE (red)
* Implement business logic
* Execute tests again (green)
* Refactor our code, or write the next red testcase

First, [Intellij](https://www.jetbrains.com/idea/download/#section=linux) and also an Intellj license are needed. After extracting Intellij, create a new empty project at your disk, named "Katas". Katas are short programming exercises, which can be used well to evaluate new features from IDE to new programming library. Switch in the project folder and init project with 

`npm init`. 

The default values are enough for our showcase. We also create a folder `src` and in this folder a file `FizzBuzzTest.js`. After the inital setup the project has following structure:

![Intellij project structure](/assets/img/TddJs_InitialFolderStructure.png "InitialFolderStructure")

Now we need some extra [Intellij Setup](https://www.jetbrains.com/help/idea/testing-javascript-with-mocha.html).
Install Mocha as local dependency 

`npm install --save-dev mocha` 

and also "Chai" for better assertions:

`npm install --save-dev chai`

Now start implementing the testcase in the created Javascript-File `FizzBuzzTest.js`.

{% highlight Javascript %}
describe("We want to create a FizzBuzz-Kata TDD with modern JS",() => {
});
{% endhighlight %}

Intellij will ask you to change Ecmascript to newer version "Ecmascript6". You can also change this option at `File => Settings => Languages & Frameworks => Javascript.` Also set check box for using "strict mode", for newly written Javascript you should also prefer this option.
Now implement the first tescase:

{% highlight Javascript %}
"use strict";

describe("We want to create a FizzBuzz-Kata TDD with modern JS",() => {

    it("Should return Fizz when digit is dividable by three", () => {
           expect(new FizzBuzz().convertString("3")).to.equal("Fizz")
    });
});
{% endhighlight %}

Run test with ctrl+F5, we see some extra problems with this setup. `Expect` is an unknown dependency we need to import, by adding this direct after `use strict` line:

{% highlight Javascript %}
import {expect} from "chai";
{% endhighlight %}

Inteliij knows that we are using newer Javascript-Version, but our TestRunner currently not. Also most browsers would decline Javascript-files in Ecmascript6 version. That is the reason wy we need an extra transpile step, to generate regular Javacsript-files.

Again we use [Intellij-Support](https://blog.jetbrains.com/webstorm/2015/05/ecmascript-6-in-webstorm-transpiling/).

Some hints:
* The File-Watcher plugin needs to be installed separatly, after downloading new Intellij-package
* Just need to install babel-cli as described and configure File-Watcher-Plugin

For starting code transpilation, we need a change at our code base. Add missing semicolon at end of the test case.

{% highlight Javascript %}
expect(new FizzBuzz().convertString("3")).to.equal("Fizz");
{% endhighlight %}

Now Intellij transpiles the Javascript-File in a dist-Folder and we can execute our testcase again (add Screensshot here).
Now there are no syntax or compile errors, just the expected error message, that our "Fizz-Buzz"-implemenation is missing. This is the point were our TDD-cycle can start. We make our first test-case green:

{% highlight Javascript %}
"use strict";

import {expect} from "chai";

class FizzBuzz {
    convertString(userInput) {
        return "Fizz";
    }
}

describe("We want to create a FizzBuzz-Kata TDD with modern JS",() => {

    it("Should return Fizz when digit is dividable by three", () => {
           expect(new FizzBuzz().convertString("3")).to.be.equal("Fizz");
    });
});
{% endhighlight %}

At the beginning of our development cycle we can add our business logic to the same file as the testcase, so we avoid file-switching. Our IDE can help us refactoring this later.
After running the test it becomes green first time. For implementing further business logic we need a red testcase:

{% highlight Javascript %}
    it("Should return digit if it is not dividiable by three or five", () => {
        expect(new FizzBuzz().convertString("1")).to.equal("1");
    });
{% endhighlight %}

Executing again: red. That`s the only point where we are allowd to add new business logic:

{% highlight Javascript %}
class FizzBuzz {
    convertString(userInput) {
        if (userInput % 3 == 0) {
            return "Fizz";
        }
        return userInput;
    }
}
{% endhighlight %}

Green again. Now the code could be refactored or a new testcase could be added. Don`t forget to use shortcuts for reexcuting tests (alt+shift+r).

Now we are ready to finish our code in fast TDD-cycle. An exercise the reader can complete from here on.

Link Collection
* [Javascript-Imports]("https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Statements/import")
* 
* 


Anmerkungen:
* NOdejs-Plugin installiert
* Katas-Referenz (Fizz-Buzz)
* Vorkenntnisse (npm mit URL)
* TDD-Intro-Artikel verlinken
* Link auf Javascript-Strict-Mode
* Link auf Ecmascript 6-JS
* Hinweis auf Testabdeckung mit aufnehmen, sollte jetzt auch gehen