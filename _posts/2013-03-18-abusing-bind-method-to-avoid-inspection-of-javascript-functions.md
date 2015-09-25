---
title: "Lab: Abusing `.bind` Method to Avoid Inspection of JavaScript Functions"
abstract: "An interesting feature(?) of JavaScript: Proxying functions over native side using `.bind` method."
---

Hello!

You all know we can see a function's implementation with Web Inspector:

    :::javascript
    function STCustom() {
        // Hello, I'm the implementation!
    }

The inspector says what is the implementation:

![image](http://cl.ly/image/1t3d2T2V3U1a/Screen%20Shot%202013-03-18%20at%202.13.36%20AM.png)

I realised that if I bind `STCustom` function anything else, the implementation will not be visible.

Let's wrap it and bind to undefined.

    :::javascript
    var STCustom = function () {
        // Hello, I'm the implementation!
    }.bind();

Now, let's see it's implementation:

![image](http://cl.ly/image/212d1S0M3n2J/Screen%20Shot%202013-03-18%20at%202.22.13%20AM.png)

It's not visible. But it's not useful for now. Let's abuse it more. :)

    var STCustom = (function () {
      function STCustom() {
        // Hello I'm the implementation!
      }
      STCustom.prototype.setColumnSize = function(number) {
        // do some calculations
      };
      STCustom.prototype.setRowSize = function(number) {
        // do more calculations
      };
      return STCustom;
    })();

It's a simple JavaScript class with methods. Before try to hide them let's check the `STCustom` variable.

![image](http://cl.ly/image/1t3d2T2V3U1a/Screen%20Shot%202013-03-18%20at%202.13.36%20AM.png)

OK, It's still visible. Let's `.bind` it to nothing again:

    var STCustom = (function () {
      function STCustom() {
        // Hello I'm the implementation!
      }
      STCustom.prototype.setColumnSize = function(number) {
        // do some calculations
      };
      STCustom.prototype.setRowSize = function(number) {
        // do more calculations
      };
      return STCustom.bind();
    })();

![image](http://cl.ly/image/212d1S0M3n2J/Screen%20Shot%202013-03-18%20at%202.22.13%20AM.png)

Now, it's hidden again. What about the methods?

![image](http://cl.ly/image/3X3n461N0Y2V/Screen%20Shot%202013-03-18%20at%202.35.03%20AM.png)

These are visible. So we should hide them too, abusing "bind":

    var STCustom = (function () {
      function STCustom() {
        // Hello I'm the implementation!
         for (var method in this)
          if (this.hasOwnProperty(method) && this[method].bind)
            this[method] = this[method].bind(this);
      }
      STCustom.prototype.setColumnSize = function(number) {
        // do some calculations
      };
      STCustom.prototype.setRowSize = function(number) {
        // do more calculations
      };
      return STCustom.bind();
    })();

We bound all the methods to the object itself. So it couldn't be inspected.

## Deep-Freezed Classes

We also freezed that class, you cannot extend it anymore:

![image](http://cl.ly/image/0R0i2A2l0Z3n/Screen%20Shot%202013-03-18%20at%202.44.34%20AM.png)

As you see, we are not able to access `prototype` of the class.

## Why is that?

It's because `.bind` method's implementation. It returns a bounded native function, so you see the native one all the time, not your implementation. It's like proxying the function over browser.

It's not for production use, just a small trick that I realised.

Thanks! :)

*Sorry for my English mistakes.*
