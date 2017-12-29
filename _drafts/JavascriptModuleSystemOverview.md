---
author: Christian Wiedehöft
title: An overview of the Javascript module systems
date: 2017-12-29
layout: default
---

# An overview of the Javascript module systems


This article will give a brief introduction in every Javascript module
system. For longer descriptions see [this great article]("https://medium.freecodecamp.org/javascript-modules-a-beginner-s-guide-783f7d7a5fcc").

### Term definitions
* **Bundler:** Ties up modularized Javascript-files to, in most cases, one file, which can be included in HTML-Page
* **Loader:** loads modularized Javascript-files, while HTML-Page is loading
* **Transpiler:** Translates Javascript files in Browser-compatible syntax, e.g. for ES6, Typescript or Cofeescript
* **AMD:** Asynchronous module definition
* **UMD:** Universal module definition
* **ES6:** ECMAScript 6

### Overview

| Modulesystem                      | Module-Bundler   |Module-Loader                  |Module-Transpiler        | Native Interface             |
| ---                               | ---              |---                            |---                      | ---                          |
| Module pattern                    | -                |-                              |-                        | HTML over ```<script>```-Tags|
| [CommonJS] [1]: exports + require | [Browserify] [2] |-                              |-                        | Node                  |
| [AMD] [3]: define + require       | R.js (Requirejs) |[RequireJS] [4] or [Curl] [5]  |-                        | (HTML with loader-dep)       |
| [ES6] [6]: import and export      | -                |-                              | [Babel] [7]/[Rollup] [8]| Node*, HTML**                |
| [UMD] [9]: Design pattern to support AMD and CJS | - | - | -                                                   | Node, HTML |

\* Node supports [nearly all features] [10] since 8.2.1 <br />
\** Currently transpiler are still needed

Three tools are still missing in the table, because they have a special role:
1. [SystemJS][]: A module loader which supports all Module pattern
2. [Webpack][]: Main task is module bundling, but it has also support for module loading and transpiling (with help of plugins)
3. [Parcel][]: A new star at the module horizont? Same tasks as Webpack but without configuration? Currenty not used by the author

Some example source code for using the different module systems can be found at [Github](https://github.com/wiedehoeft/TemplatesSnipptesAndExperiments/tree/testJsModuleSystems)

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