---
author: Christian Wiedehöft
title: Test-Driven-Javascript development
date: 2017-10-25
layout: default
---

Ziel dieses Blog-Eintrages:
* Javascript-Code Test-Driven in Intellij entwickeln

* Als Java-Entwickler ist man die IDE-Unterstützung beim Entwickeln von Tests 
gewohnt. Eclipse und JUnit lassen sich Hand in Hand bedienen und schnell lassen
sich die Tests ausführen, um zu verfizieren ob die implementierte Logik, den 
erstellten Testfällen entspricht.
* Im Zuge der Javascript-Entwicklung sind wir im Team auf Intellij umgeschwenkt, da
die IDE-Unterstützung von Eclipse nicht die Erwartungen erfüllten
* Anfangs haben wir mit Jasmin und Karma die Tests geschrieben, aber auch hier 
Probleme mit dem sauberen TDD-Zyklus gehabt, mal wurde der Browser nicht sauber 
gestartet und ein anderes mal hat Karma vergessen, dass bereits eine Browserinstanz
geöffnet wurde
* Zudem ist es nicht befriedigend immer auf einen Browser angewiesen zu sein, um 
seine Testergebnisse einsehen zu können, obwohl der erstellte JS-Code noch gar keine
DOM-Operationen ausführt (in SPAs erstellen wir den Großteil der Application-Logik
in Javascript und damit auch viel Code der nicht mit dem DOM operiert)

Deswegen will ich hier einen Weg aufzeigen, wie wir in Intellij wie gewohnt 
TDD entwicklen können.

Dazu benötigt ihr zuerst [Intellij](https://www.jetbrains.com/idea/download/#section=linux)
und eine entsprechende Lizenz. Nach dem Download entpacken.

Als nächstes legen wir ein leeres Projekt auf der Festplatte an, mit dem Namen Katas.
Dann wechseln wir in den Ordner und initieren das Projekt mit npm. 
Besondere Eingaben sind hier nicht erforderlich. Zudem legen wir einen 
Ordner src an und erzeugen eine Javascript-Datei FizzBuzzTest.js.
Initial sollte das Projekt so aussehen:
![Intellij project structure](/assets/img/TddJs_InitialFolderStructure.png "InitialFolderStructure")

Nun richten wir Intellij ein wie auf dieser [Intellij-Seite beschrieben](https://www.jetbrains.com/help/idea/testing-javascript-with-mocha.html).

Ich werde Mocha als lokale Dev-Dependency mit
`npm install --save-dev mocha` installieren. Zudem installieren wir noch
chai um komfortablere und besser lesbare Assertions erstellen zu können:
`npm install --save-dev chai`.

Anschließend beginenn wir unseren Testfall zu definieren:

`describe("We want to create a FizzBuzz-Kata TDD with modern JS",() => {});`

Damit wir diese Javascript-Syntax verwenden können ist es notwending Intellij,
auf Ecmascript6-Syntax umzustellen File => Settings => Languages & Frameworks => Javascript.
Hier wählen wir auch gleich noch aus das wir im "strict mode arbeiten möchten".

Nun erstellen wir unseren ersten Testfall:
{% highlight Javascript %}
"use strict";

describe("We want to create a FizzBuzz-Kata TDD with modern JS",() => {

    it("Should return Fizz when digit is dividable by three", () => {
           expect(new FizzBuzz().convertString("3")).to.equal("Fizz")
    });
});
{% endhighlight %}

Den Test können wir mit ctrl+F5 ausführen. Allerdings werden wir jetzt noch
einige Probleme beheben müsen, wenn wir nach neuerem Ecmascript-Standard arbeiten wollen.
Zuerst beschwert sich Intellij das "expect" nicht aufgelöst werden kann,
diese Abhängigkeit zu chai müssen wir importieren:

{% highlight Javascript %}
import {expect} from "chai";
{% endhighlight %}

Obwohl wir in Intellij angegeben haben mit Ecmascript6 zu arbeiten, kann der 
Test-Runner den Test nicht ausführen, da "import" ein unbekanntes Statement ist. 
Die meisten Browser könnten den Code übrigens auch nicht ausführen, 
wir werden den Code in jedem Fall transpilieren müssen.

Auch hier greifen wir auf die Unterstützung zurück, die Intellij uns bietet.
https://blog.jetbrains.com/webstorm/2015/05/ecmascript-6-in-webstorm-transpiling/

* Das File-Watchers-Plugin müsst ihr separat installieren, es ist noch nicht
direkt in der heruntergeladenen Version vorhanden.
* Wir installieren wie beschrieben babel-cli und konfigurieren unseren File-Watcher.

Nach einer kleinen Änderung am Code, zum Beispuel dem ergänzen des fehlenden Semikolons,
{% highlight Javascript %}
expect(new FizzBuzz().convertString("3")).to.equal("Fizz");
{% endhighlight %}
wird unser Code in Browser-lesbare Form transpiliert.

Wenn wir jetzt den Test erneut laufen lassen bekommen wir die erwarteten
Fehlermeldungen, dass unsere Klasse noch nicht existiert und die Methode
auch noch zu implementieren ist. Machen wir also unseren ersten Test grün:

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

Initial ist es vollkommen in Ordnung die Implementierung direkt mit in unseren
Test zu schreiben, unsere IDE wird uns beim späeren Refactoring unterstützen.

Lasst uns einen zweiten Testfall ergänzen
{% highlight Javascript %}
    it("Should return digit if it is not dividiable by three or five", () => {
        expect(new FizzBuzz().convertString("1")).to.equal("1");
    });
{% endhighlight %}

Rot... Also passen wir die Implementierung an:
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
Schon sind wir wieder grün (denkt daran die Tests können mit alt+shift+r erneut ausgeführt werden).

Die vollständige Implementierung überlasse ich nun euch. Ihr könnt jetzt in Intellij
mit dem gewohnten TDD-Zyklus arbeiten und eure Logik in Javascript nach 
neuestem Ecmascript-Standard erstellen.

Link Collection
* [Javascript-Imports]("https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Statements/import")
* 
* 


Anmerkungen:
* NOdejs-Plugin installiert