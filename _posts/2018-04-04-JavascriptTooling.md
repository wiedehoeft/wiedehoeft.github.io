---
author: Christian Wiedehöft
title: Overview of Javascript Tools
date: 2018-04-04
layout: default
comments: true
---

This post gives a brief overview of Javascript Tools.

| Category              | Tools                                | Notes                                                         |
| ---                   | ---                                  | ---                                                           |
| Scaffolding           | Yeoman                               |                                                               |
|                       | Lineman                              | seems to be outdated                                          |
|                       | kittn                                |                                                               |
|                       | Brunch                               |                                                               |
| Styleguides           | Idiomatic.js                         |                                                               |
|                       | Pragmatic.js                         |                                                               |
|                       | Google JS Style Guide                |                                                               |
|                       | NPM Coding Style                     |                                                               |
|                       | Node.js Style Guide                  |                                                               |
|                       | jQuery Style Guide                   |                                                               |
|                       | Douglas Crackford's Code Conventions | see JSLINT                                                    |
|                       | airbnb Javascript Style Guide        |                                                               |
|                       | Dojo Style Guide                     |                                                               |
|                       | JavaScript Quality Guide             | see Javascript Application Design by Nicolas G. Bevacqua      |
| Code Quality          | JSLINT                               |                                                               |
|                       | JSHINT                               |                                                               |
|                       | ESLINT                               | https://www.nczonline.net/blog/2013/07/16/introducing-eslint/ |
|                       | JSBeautifier                         |                                                               |
|                       | Google Closure Linter                |                                                               |
|                       | Flow                                 |                                                               |
| Documentation         | JSDoc 3                              |                                                               |
|                       | YUIDoc                               |                                                               |
|                       | JSDuck 5                             |                                                               |
|                       | Swagger                              |                                                               |
|                       | jGrousedoc                           |                                                               |
|                       | Docco                                |                                                               |
| Concatenation, Minification, Obfuscation  | YUI Compressor   |                                                               |
|                       | Google Closure Compiler              |                                                               |
|                       | UglifyJS 2                           |                                                               |
| Package Management    | NPM for Backend                      |
|                       | Bower for Frontend                   |
|                       | Yarn                                 |
|                       | Duo                                  |
| Module loader/bundler | RequireJS                            | see Post [An overview of the Javascript module systems]({{ site.baseurl }}{% link _posts/2017-12-30-JavascriptModuleSystemOverview.md %}) |
|                       | Webpack                              |
|                       | SystemJS                             |
|                       | Browserify                           |
| Testing               | QUnit                                | with DOM Dependencies
|                       | CasperJS                             | with DOM Dependencies
|                       | Protractor                           | with DOM Dependencies
|                       | mocha                                |
|                       | Jasmine                              |
|                       | Karma                                | Test Runner
|                       | Jest                                 | 
|                       | Ava                                  | Test Runner
|                       | Testem                               | Test Runner
|                       | PhantomJS                            | Headless browser for tests against DOM in e.g. CI
|                       | Chai                                 | Assertion library
|                       | Sinon.JS                             | Mocking, Stubbing and Spying
|                       | Blanket.JS                           | Test Coverage
|                       | Jest                                 | React first approach
| Building              | Grunt                                |
|                       | Gulp JS                              |
|                       | Webpack                              |
|                       | Parcel                               |
| Security              | Synk                                 |
|                       | Node Security Project                |
|                       | RetireJS                             |
|                       | Gemnasium                            |
|                       | OSSIndex                             |
| Templating            | Handlebar.js                         |
|                       | Mustache.js                          |
|                       | Twig                                 |
|                       | Jade                                 |
| MVC, MVVM, MV*        | Backbone.js                          | for examples see [http://todomvc.com](http://todomvc.com)
|                       | Knockout.js                          |
|                       | Angular.js/.io                       |
|                       | Vue.js                               |
|                       | React                                |
|                       | Ember.js                             |
|                       | Polymer                              |      
| Backend               | Node                                 |
|                       | Express                              |
| UI                    | Storybook                            | available for Angular, Vue and React 

*You have more tools or new categories and tools? Then leave a comment.*



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
