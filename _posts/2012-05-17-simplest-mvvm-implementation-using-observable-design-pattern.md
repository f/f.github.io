---
layout: post
title: Simplest MVVM Implementation Using Observable Design Pattern
abstract: The best-known MVVM libraries are Angular.js and Knockout.js. This example looks a little Knockout.js. But much more simpler.
---

<style>
  iframe {
    width: 550px;
    height: 300px;
  }
</style>

Today, I’ll write how to implement a simple MVVM implementation using [observables](../javascript-observable-design-pattern/).

The best-known MVVM libraries are Angular.js and Knockout.js. This example looks a little Knockout.js. But mooooore simpler; just to show you
how it works, probably you won't use it on your projects.

Lets check how to do it!

It has only one function to observe: value, so we can define a variable like saying `var test = MVVM.value(‘test’);` and it get one parameter for default value.
At the end of declerations, it must be started saying `MVVM.apply()`, as you will see, it just fires the `change:model` event to rebind all views.

_P.S: I used native JavaScript to manage DOM. You can change it into jQuery._

## The Implementation

Lets create an HTML with `data-bind` attributes. If we call `data-bind="innerHTML: test"`, it'll run like `element.innerHTML = this.test`

{% highlight html %}
<div data-bind="innerHTML: fatih, style: {color: color}, onclick: function() {fatih('hi!')}"></div>
<div data-bind="innerText: fatih, style: {display: function() {return fatih == 'hi!'?'none':'block'}()}"></div>
{% endhighlight %}

This is the HTML structure what we need.

And that is our Observer:

{% highlight js %}
var Observable = function() {
    var events = {};
    this.on = function(name, func) {
        if (!events[name]) events[name] = [];
        events[name].push(func);
    };
    this.trigger = function(name, data) {
        if (events[name] && events[name].length > 0) {
            for (var i = 0, l = events[name].length; i < l; i++) {
                events[name][i].call(this, data);
            }
        }
    };
    this.off = function(name, func) {
        var l = arguments.length;
        if (l == 0) {
            events = {};
        } else if (l == 1 && events[name]) {
            delete events[name];
        } else if (l == 2 && events[name] && events[name].length > 0) {
            for (var i = 0, _l = events[name].length; i < _l; i++) {
                if (events[name][i] == func) {
                    delete events[name][i];
                    return;
                }
            }
        }
    }
};
{% endhighlight %}

Let's create the framework!

{% highlight js %}
var MVVM = {
    value: function(def) {
        var self = this;
        var _value;
        var setter = function(value) {
            _value = value || def;
            self.trigger('change:model', {
                value: value
            });
        };
        setter.toString = function() {
            return _value || def;
        };
        return setter;
    }
};
{% endhighlight %}

And let's make our framework observable:

{% highlight js %}
Observable.call(MVVM);
MVVM.on('change:model', function() {
    Array.prototype.slice.call(document.querySelectorAll('[data-bind]')).forEach(function(el) {
        var boundData = eval('({' + el.dataset.bind + '})');
        for (var prop in boundData) {
            var value = boundData[prop];
            el[prop] = typeof value == 'object' ?
            function(prop, val) {
                for (var v in val) {
                    prop[v] = val[v];
                }
            }(el[prop], value) : value;
            MVVM.trigger('change:view');
        }
    });
});
MVVM.apply = function() {
    MVVM.trigger('change:model');
};
{% endhighlight %}

Let's begin observing:

{% highlight js %}
var color = MVVM.value('red');
var fatih = MVVM.value('fath');

MVVM.apply();
{% endhighlight %}

And here is the running version:

<iframe frameborder="0" src="http://jsfiddle.net/fkadev/GemEK/embedded/"></iframe>
