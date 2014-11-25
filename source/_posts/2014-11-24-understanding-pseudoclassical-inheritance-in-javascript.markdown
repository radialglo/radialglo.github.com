---
layout: post
title: "Understanding Pseudoclassical Inheritance in JavaScript"
date: 2014-11-24 22:49
comments: true
categories: 
---

## Introduction

In [JavaScript: The Good Parts](http://shop.oreilly.com/product/9780596517748.do), [Douglas Crockford](http://www.crockford.com/) disapproves of **Pseudoclassical Inheritance**
in favor of **Functional Inheritance**. However, a quick survey of JavaScript resources such as [Mozilla Developer Network(MDN)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Introduction_to_Object-Oriented_JavaScript) and JavaScript Tools/Libraries such as  [Google Closure](https://github.com/google/closure-library), [CoffeeScript](http://coffeescript.org/), [YUI3](http://www.yuiblog.com/blog/2010/01/06/inheritance-patterns-in-yui-3/), and [Jasmine](https://github.com/jasmine/jasmine) indicate that Pseudoclassical pattern seems to be quite a popular inheritance pattern.

## Drawbacks of Functional Inheritance

[Michael Bolin](http://bolinfest.com/) who was a core contributor to Google Closure presents
a strong case for Pseudoclassical Inheritance in his blog post [Inheritance Patterns in JavaScript](http://bolinfest.com/javascript/inheritance.php).
In his post he presents drawbacks for using Functional Inheritance and also addresses Douglas Crockford's concerns for Pseudoclassical Inheritance.

### Main Drawbacks

1. Instance of types take up more memory
2. Types cannot be tested using instanceof
3. Encourages adding properties to Function.prototype and Object.prototype
4. Makes it impossible to update all instances of a type
5. Naming newly created objects is awkward

### Closure-related/Other Drawbacks

1. Methods cannot be inlined
2. Superclass methods cannot be renamed(or will be renamed incorrectly)
3. Results in an extra level of indentation

## Naive Implementation of Pseudoclassical Inheritance

## General Implementation of Pseudoclassical Inheritance

## Pseudoclassical Inheritance in the wild

### Google Closure
Closure is a set of individual JavaScript tools that are also designed to help developers build complex web applications, and is used by Gmail, Google Maps, and Google Docs. 
<!--
https://developers.google.com/closure/faq#gwt
-->

### CoffeeScript
> JavaScript's prototypal inheritance has always been a bit of a brain-bender, with a whole family tree of libraries that provide a cleaner syntax for classical inheritance on top of JavaScript's prototypes: [Base2](https://code.google.com/p/base2/), [Prototype.js](http://prototypejs.org/), [JS.Class](http://prototypejs.org/), etc. The libraries provide syntactic sugar, but the built-in inheritance would be completely usable if it weren't for a couple of small exceptions: it's awkward to call super (the prototype object's implementation of the current function), and it's awkward to correctly set the prototype chain.

<!--
http://coffeescript.org/#classes
-->
{% codeblock Inheritance implemented in compiled-down CoffeeScript lang:js %}

var Animal, Horse, Snake, sam, tom,
    __hasProp = {}.hasOwnProperty,
    __extends = function(child, parent) {
        for (var key in parent) {
            if (__hasProp.call(parent, key)) child[key] = parent[key];
        }

        function ctor() {
            this.constructor = child;
        }
        ctor.prototype = parent.prototype;
        child.prototype = new ctor();
        child.__super__ = parent.prototype;
        return child;
    };

Animal = (function() {
  function Animal(name) {
    this.name = name;
  }

  Animal.prototype.move = function(meters) {
    return alert(this.name + (" moved " + meters + "m."));
  };

  return Animal;

})();

Snake = (function(_super) {
  __extends(Snake, _super);

  function Snake() {
    return Snake.__super__.constructor.apply(this, arguments);
  }

  Snake.prototype.move = function() {
    alert("Slithering...");
    return Snake.__super__.move.call(this, 5);
  };

  return Snake;

})(Animal);


{% endcodeblock %}

### Jasmine
<!--
https://github.com/jasmine/jasmine/blob/master/src/core/util.js
https://github.com/jasmine/jasmine/blob/master/src/core/util.js#L3-L10
-->
{% codeblock lang:js %}
  util.inherit = function(childClass, parentClass) {
    var Subclass = function() {
    };
    Subclass.prototype = parentClass.prototype;
    childClass.prototype = new Subclass();
  };

{% endcodeblock %}



### YUI3
<!--
http://www.yuiblog.com/blog/2010/01/06/inheritance-patterns-in-yui-3/
https://github.com/yui/yui3/blob/master/src/oop/js/oop.js#L181-L220
-->

{% codeblock lang:js%}
/**
 * Utility to set up the prototype, constructor and superclass properties to
 * support an inheritance strategy that can chain constructors and methods.
 * Static members will not be inherited.
 *
 * @method extend
 * @param {function} r   the object to modify.
 * @param {function} s the object to inherit.
 * @param {object} px prototype properties to add/override.
 * @param {object} sx static properties to add/override.
 * @return {object} the extended object.
 */
Y.extend = function(r, s, px, sx) {
    if (!s || !r) {
        Y.error('extend failed, verify dependencies');
    }

    var sp = s.prototype, rp = Y.Object(sp);
    r.prototype = rp;

    rp.constructor = r;
    r.superclass = sp;

    // assign constructor property
    if (s != Object && sp.constructor == OP.constructor) {
        sp.constructor = s;
    }

    // add prototype overrides
    if (px) {
        Y.mix(rp, px, true);
    }

    // add object overrides
    if (sx) {
        Y.mix(r, sx, true);
    }

    return r;
};
{% endcodeblock %}