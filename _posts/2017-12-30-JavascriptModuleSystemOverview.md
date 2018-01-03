---
author: Christian Wiedehöft
title: An overview of the Javascript module systems
date: 2017-12-29
layout: default
comments: true
---

This article will give a brief introduction in common Javascript module systems. 

For longer descriptions see [this great article](https://medium.freecodecamp.org/javascript-modules-a-beginner-s-guide-783f7d7a5fcc).

### Term definitions
* **Bundler:** Ties up modularized Javascript-files to, in most cases, one file, which can be included in HTML-Page
* **Loader:** loads modularized Javascript-files, while HTML-Page is loading
* **Transpiler:** Translates Javascript files in Browser-compatible syntax, e.g. for ES6, Typescript or Coffeescript
* **AMD:** Asynchronous module definition
* **UMD:** Universal module definition
* **ES6:** ECMAScript 6

### Overview

| Module-System                     | Module-Bundler   |Module-Loader                  |Module-Transpiler        | Native Interface             | Favourite testing tool    |
| ---                               | ---              |---                            |---                      | ---                          | ---                       |  
| Module pattern                    | -                |-                              |-                        | HTML with ```<script>```-Tags| Qunit                     |
| [CommonJS] [1]: exports + require | [Browserify] [2] |-                              |-                        | Node                         | Mocha                     |
| [AMD] [3]: define + require       | R.js (Requirejs) |[RequireJS] [4] or [Curl] [5]  |-                        | (HTML with loader-dep)       | -                         |
| [ES6] [6]: import and export      | -                |-                              | [Babel] [7]/[Rollup] [8]| Node*, HTML**                | Mocha after transpilation |
| [UMD] [9]: Design pattern to support AMD and CJS | - | - | -                                                   | Node, HTML                   | Qunit/Mocha               |

\* Node supports [nearly all features] [10] since 8.2.1 <br />
\** Currently transpiler are still needed

Three tools are still missing in the table, because they have a special role:
1. [SystemJS][]: A module loader which supports all module systems
2. [Webpack][]: Main task is module bundling, but it has also support for module loading and transpiling (with help of plugins)
3. [Parcel][]: A new star at the module horizon? Promise is same functional range as Webpack but without configuration => Currently not used by the author

Some example source code for using the different module systems can be found at [my Github example project](https://github.com/wiedehoeft/TemplatesSnipptesAndExperiments/tree/testJsModuleSystems)

[1]: https://en.wikipedia.org/wiki/CommonJS
[2]: http://browserify.org/
[3]: http://requirejs.org/docs/whyamd.html
[4]: http://requirejs.org
[5]: https://github.com/cujojs/curl
[6]: http://exploringjs.com/es6/ch_modules.html
[7]: https://babeljs.io/
[8]: https://rollupjs.org/
[9]: https://github.com/umdjs/umd
[10]: http://node.green/
[SystemJS]: https://github.com/systemjs/systemjs
[Webpack]: https://webpack.js.org/
[Parcel]: https://medium.freecodecamp.org/all-you-need-to-know-about-parcel-dbe151b70082

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
