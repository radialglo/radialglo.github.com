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
In his post he discusses drawbacks for using Functional Inheritance and also addresses Douglas Crockford's concerns for Pseudoclassical Inheritance.

Here is an example of Functional Inheritance from Douglas Crockford's book:

```javascript Functional Inheritance Example

var mammal = function(spec) {

    var that = {};

    that.get_name = function() {

        return spec.name;

    };

    that.says = function() {
        return spec.saying || '';
    };

    return that;
}

var cat = function(spec) {

    spec.saying = spec.saying || 'meow';
    var that = mammal(spec);
    that.purr = function(n) {

        var i, s = '';

        for (i = 0; i < n; i++) {

            if (s) {
                s += '-';
            }
            s += 'r';
        }

        return s;
    };
    that.get_name = function() {
        return that.says() + ' ' + spec.name + ' ' + that.says();
    };
    return that;

}

var myMammal = mammal({name: 'Herb'});
var myCat = cat({name: 'Fiona'});
    myCat.get_name(); // "meow Fiona meow"
    myCat.purr(5); // "r-r-r-r-r"

```

and the same "classes" modeled using Pseudoclassical Inheritance:


```javascript Pseudoclassical Inheritance Example

var Mammal = function(name) {
    this.name = name;
};

Mammal.prototype.get_name = function() {
    return this.name;
};

Mammal.prototype.says = function() {
    return this.saying || '';
};

var Cat = function(name) {
    Mammal.call(this, name);
};

Cat.prototype = Object.create(Mammal.prototype);
Cat.prototype.constructor = Cat;

Cat.prototype.saying = 'meow';

Cat.prototype.purr = function(n) {

    var i, s = '';

    for (i = 0; i < n; i++) {

        if (s) {
            s += '-';
        }
        s += 'r';
    }

    return s;
};
Cat.prototype.get_name = function() {
    return this.says() + ' ' + this.name + ' ' + this.says();
};

var myMammal = new Mammal('Herb');
var myCat = new Cat('Fiona');
    myCat.get_name(); // "meow Fiona meow"
    myCat.purr(5); // "r-r-r-r-r"

```


### Main Drawbacks

I would recommend reading Michael Bolins [Inheritance Patterns in JavaScript](http://bolinfest.com/javascript/inheritance.php)
for an in depth explanation of these drawbacks. Note that some of his points are made in respect to Google Closure.

1. Instance of types take up more memory
    - Every time ```mammal()``` is called, two new functions are created (one per method of the type). Each time, the functions are basically the same, but they are bound to different values. These functions are expensive because each is a closure that maintains a reference for every named variable in the enclosing function in which the closure was defined. This may accidentally prevent objects from being garbage collected, causing a memory leak.
    - This is not a concern when ```Mammal``` is called because of how it takes advantage of prototype-based inheritance. Each method is defined once on ```Mammal.prototype``` and is therefore available to every instance of ```Mammal```. This limits the number of function objects that are created and does not run the risk of leaking memory

2. Types cannot be tested using instanceof
    - From [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof), the ```instanceof``` operator tests
     whether an object has in its prototype chain the ```prototype``` property of a constructor. 
     > ```object instanceof constructor```
    - Since prototypes are not used for implementing inheritance for ```cat()```, there is no appropriate constructor to use for instanceof
3. Encourages adding properties to Function.prototype and Object.prototype
    - In his book, Crockford augment's the prototype to assist in creating superclass methods

```javascript Prototype Augmentation from JavaScript: The Good Parts
// From Chapter 4.
Function.prototype.method = function(name, func) {
  this.prototype[name] = func;
  return this;
};

// From Chapter 5.
Object.method('superior', function(name) {
  var that = this, method = that[name];
  return function() {
    return method.apply(that, arguments);
  };
});
```
 - Adding methods to the prototype is dangerous, especially because the ```for in``` construct includes properties added to the prototype.

4. Makes it impossible to update all instances of a type
    - You need to individually update each instance.
    - In Pseudoclassical Inheritance, updating all instance of a type is as simple as updating properties or methods on the prototype.


### Closure-related/Other Drawbacks

1. Methods cannot be inlined
    - Methods in the functional pattern are often bound to variables that cannot be referenced externally, so there is no way for Closure Compiler to rewrite method calls in a manner that eliminates the method dispatch
2. Superclass methods cannot be renamed(or will be renamed incorrectly)
    - Method renaming from JavaScript minifiers can use issues
3. Results in an extra level of indentation
    - Coding preference
4. Naming newly created objects is awkward


## Naive Implementation of Pseudoclassical Inheritance

Here is an example of implementing Pseudoclassical Inheritance from [JavaScript Garden](http://bonsaiden.github.io/JavaScript-Garden/#object.prototype).
The main idea is to **create a dummy instance of the parent** ```Foo``` via ```new Foo``` and also call the parent constructor.

```javascript Naive Pseudoclassical Inheritance
function Foo() {
    this.value = 42;
}
Foo.prototype = {
    method: function() {}
};

function Bar() {
    Foo.call(this); // call parent constructor
}

// Set Bar's prototype to a new instance of Foo
Bar.prototype = new Foo();
Bar.prototype.foo = 'Hello World';

// Make sure to list Bar as the actual constructor
Bar.prototype.constructor = Bar;

var test = new Bar(); // create a new bar instance
```

While this way of implementing Pseudoclassical Inheritance is not wrong per se, there is a little awkwardness to how the child's prototype (```Bar.prototype```) is being set.
Imagine the case, where the parent constructor takes in a couple parameters. It would be strange to invoke the ```new Parent()``` with dummy parameters because the instantiated object is a dummy instance. Additionally, the superclass’s constructor may have side effects (i.e. anything instance properties set in the superclass constructor get added to the prototype)or do something that is computationally intensive. Therefore, since the object that gets instantiated for the prototype is usually just a throwaway instance, you don’t want to create it unnecessarily.

## General Implementation of Pseudoclassical Inheritance

To improve on the naive implementation of Pseudoclassical inheritance, we can **create a dummy constructor**, **set it's prototype to the prototype of the parent**,
and **create a dummy instance that is used as the prototype of the child**. Same as before, we also need to reset the prototype.constructor of the child to child(because the prototype needs to reference the appropraite constructor) and call the parent constructor in the child.

> Use a dummy constructor when creating the object used for the prototype because superclass’s constructor may have side effects or do something that is computationally intensive

We can improve the the previous Foo Bar example like so:

```javascript PseudoClassical Inheritance
function Foo() {
    this.value = 42;
}
Foo.prototype = {
    method: function() {}
};

function Bar() {
    Foo.call(this); // call parent constructor
}

// Set Bar's prototype to a new dummy instance that has use Foo.prototype for its prototype
Bar.prototype = Object.create(Foo.prototype); // new Foo(); 

// Make sure to list Bar as the actual constructor
Bar.prototype.constructor = Bar;

var test = new Bar(); // create a new bar instance

/* Test's prototype chain
  test -> Bar.prototype -> Foo.protoype -> Object -> null
*/

```

Pay close attention to the use of the native [Object.create](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create) method
which creates a new object with the specified prototype object and properties.

> The ```Object.create()``` method creates a new object with the specified prototype object and properties.

Here is a simple [Object.create polyfill](http://javascript.crockford.com/prototypal.html) from Crockford's website.
```javascript Object.create

if (typeof Object.create !== 'function') {
    Object.create = function (o) {
        function F() {}
        F.prototype = o;
        return new F();
    };
}

```
And an optimized polyfill from [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create)
```javascript Object.create polyfill
if (typeof Object.create != 'function') {
  Object.create = (function() {
    var Object = function() {};
    return function (prototype) {
      if (arguments.length > 1) {
        throw Error('Second argument not supported');
      }
      if (typeof prototype != 'object') {
        throw TypeError('Argument must be an object');
      }
      Object.prototype = prototype;
      var result = new Object();
      Object.prototype = null;
      return result;
    };
  })();
}

```

## Pseudoclassical Inheritance in the wild

Based on our understanding of why and how to implement Pseudoclassical Inheritance, now we'll take 
a look at how this pattern is being used for large JavaScript Libraries.

### Google Closure
Closure is a set of individual JavaScript tools that are also designed to help developers build complex web applications, and is used by Gmail, Google Maps, and Google Docs.
Inheritance in Google Closure is implementance via Pseudoclassical Inheritance. Google closure also adds a ```base``` method to the child constructor to expose
a covenient way to invoke parent methods. 
<!--
https://developers.google.com/closure/faq#gwt
-->
```javascript Inheritance via Google Closure
/**
 * Inherit the prototype methods from one constructor into another.
 *
 * Usage:
 * <pre>
 * function ParentClass(a, b) { }
 * ParentClass.prototype.foo = function(a) { };
 *
 * function ChildClass(a, b, c) {
 *   ChildClass.base(this, 'constructor', a, b);
 * }
 * goog.inherits(ChildClass, ParentClass);
 *
 * var child = new ChildClass('a', 'b', 'see');
 * child.foo(); // This works.
 * </pre>
 *
 * @param {Function} childCtor Child class.
 * @param {Function} parentCtor Parent class.
 */
goog.inherits = function(childCtor, parentCtor) {
  /** @constructor */
  function tempCtor() {};
  tempCtor.prototype = parentCtor.prototype;
  childCtor.superClass_ = parentCtor.prototype;
  childCtor.prototype = new tempCtor();
  /** @override */
  childCtor.prototype.constructor = childCtor;

  /**
   * Calls superclass constructor/method.
   *
   * This function is only available if you use goog.inherits to
   * express inheritance relationships between classes.
   *
   * NOTE: This is a replacement for goog.base and for superClass_
   * property defined in childCtor.
   *
   * @param {!Object} me Should always be "this".
   * @param {string} methodName The method name to call. Calling
   *     superclass constructor can be done with the special string
   *     'constructor'.
   * @param {...*} var_args The arguments to pass to superclass
   *     method/constructor.
   * @return {*} The return value of the superclass method/constructor.
   */
  childCtor.base = function(me, methodName, var_args) {
    var args = Array.prototype.slice.call(arguments, 2);
    return parentCtor.prototype[methodName].apply(me, args);
  };
};
```
<!--
https://github.com/google/closure-library/blob/master/closure/goog/base.js#L2087-L2137
-->

### CoffeeScript

In addition to using Pseudoclassical inheritance for the underlying implementation of CoffeeScript,
[Jeremy Askenas](https://twitter.com/jashkenas) also discusses inconviences to libraries that try to
provide a cleaner syntax for classical inheritance.

> JavaScript's prototypal inheritance has always been a bit of a brain-bender, with a whole family tree of libraries that provide a cleaner syntax for classical inheritance on top of JavaScript's prototypes: [Base2](https://code.google.com/p/base2/), [Prototype.js](http://prototypejs.org/), [JS.Class](http://prototypejs.org/), etc. The libraries provide syntactic sugar, but the built-in inheritance would be completely usable if it weren't for a couple of small exceptions: it's awkward to call super (the prototype object's implementation of the current function), and it's awkward to correctly set the prototype chain.

The crux of Jeremy's implementation of Pseudoclassical inheritance is the  ```__extends``` function.
An interesting part of the implementation is the for loop which **copies static methods** from the parent
onto the child.
```javascript
 for (var key in parent) {
    if (__hasProp.call(parent, key)) child[key] = parent[key];
}
```

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
[Jasmine](http://jasmine.github.io/2.0/introduction.html) is a behavior-driven development framework for testing JavaScript code. In Jasmine,
inherit is attached to the utility object util.
{% codeblock lang:js %}
  util.inherit = function(childClass, parentClass) {
    var Subclass = function() {
    };
    Subclass.prototype = parentClass.prototype;
    childClass.prototype = new Subclass();
  };

{% endcodeblock %}


### YUI3

YUI is a free, open source JavaScript and CSS library from Yahoo for building richly interactive web applications.

The method in YUI for inheritance is ```Y.extend``` used like so ```Y.extend(Programmer, Person);``` where a Programmer inherits from Person.
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

<!--
https://github.com/yui/yui3/blob/master/src/yui/js/yui-object.js#L20-L47
-->
 ``` Y.Object ``` used within ```Y.extend``` is essentially a wrapper
 for ```Object.create``` with a fallback. The fallback wraps the dummy constructor around an Immediately Invoked Function Expression(IIFE),
 so that the dummy constuctor can be reused.

``` javascript Y.Object
/**
 * Returns a new object that uses _obj_ as its prototype. This method wraps the
 * native ES5 `Object.create()` method if available, but doesn't currently
 * pass through `Object.create()`'s second argument (properties) in order to
 * ensure compatibility with older browsers.
 *
 * @method ()
 * @param {Object} obj Prototype object.
 * @return {Object} New object using _obj_ as its prototype.
 * @static
 */
O = Y.Object = Lang._isNative(Object.create) ? function (obj) {
    // We currently wrap the native Object.create instead of simply aliasing it
    // to ensure consistency with our fallback shim, which currently doesn't
    // support Object.create()'s second argument (properties). Once we have a
    // safe fallback for the properties arg, we can stop wrapping
    // Object.create().
    return Object.create(obj);
} : (function () {
    // Reusable constructor function for the Object.create() shim.
    function F() {}

    // The actual shim.
    return function (obj) {
        F.prototype = obj;
        return new F();
    };
}()),
```

## Conclusion

I hope this article sheds light on the advantages of using Pseudoclassical Inheritance over Functional Inheritance and clarifies
how to implement Pseudoclassical Inheritance in an optimal manner.




