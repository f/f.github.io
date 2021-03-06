---
layout: post
title: "Exporting the Common Module for Node.js and Native Browser, in a Short Way"
abstract: "Today, I saw a beautiful and different way to define common module for Node.js and the browser. In the leFunc.js’s source, there is a beautiful way to handle it."
---

Today, I saw a beautiful and different way to define common module for Node.js and the browser. In the [leFunc.js](https://github.com/jrf0110/leFunc/blob/master/lib/leFunc.js)’s
source, there is a beautiful way to handle it.

{% highlight js %}
var module  = module || {}
  , window  = window || module.exports || exports
  , exports = module.exports || exports || window
;
{% endhighlight %}

_Update on 12 November 2012: leFunc replaced that method with a simpler one._

{% highlight js %}
if (typeof module !== "undefined")
  module.exports.leFunc = leFunc;
else if (typeof define !== "undefined")
  define('leFunc', function(){ return leFunc });
else window.leFunc = leFunc;
{% endhighlight %}

Maybe, we can make it more modular:

{% highlight js %}
function exporter(name, module) {
  if (typeof module !== "undefined")
    module.exports[name] = module;
  else if (typeof define !== "undefined")
    define(name, function(){ return module });
  else window[name] = module;
}
{% endhighlight %}

Have a good day! :)
