---
layout: post
title: "Additional Things I learned from jQuery Source"
date: 2014-10-27 17:03
comments: true
categories: 
---

## Introduction

Several months ago I decided to build a client side library called [Guac](https://github.com/radialglo/guac).
The library supports a similar and trimmed down API of that of [jQuery](http://jquery.com/).
My library is by no means production ready, but building it was a good exercise for me to also explore
the internals of [jQuery](https://github.com/jquery/jquery).

This posts will discuss some  of the internal workings of jQuery and explain how these
ideas relate to the higher level API of jQuery.
I will be walking you through parts of the [jQuery codebase](https://github.com/jquery/jquery).

I would also recommend taking a look at [Paul Irish's](http://www.paulirish.com/) related posts:
[10 Things I learned From jQuery Source](http://www.paulirish.com/2010/10-things-i-learned-from-the-jquery-source/)
and [11 More Things I learned From jQuery source](http://www.paulirish.com/2011/11-more-things-i-learned-from-the-jquery-source/).
I try not to overlap with ideas he has already explained unless they are crucial to what I am discussing below.


## How jQuery is built

### TODO

## jQuery Alias

 You're probably familiar ``` $ ``` variable which is often used for [selecting DOM Elements](http://learn.jquery.com/using-jquery-core/selecting-elements/)
 like so ``` $("#awesomeID") ```

 In fact, the ``` $ ``` is  actual just an alias for ``` jQuery ```, so you can also use ``` jQuery("#awesomeID") ```
 instead of ``` $("#awesomeID") ```.

The related [line](https://github.com/jquery/jquery/blob/master/src/exports/global.js#L28) within the codebase is
{% codeblock jQuery aliasing lang:js %}
window.jQuery = window.$ = jQuery;
{% endcodeblock %}

**Source Code:** [src/exports/global.js](https://github.com/jquery/jquery/blob/master/src/exports/global.js)

Variables at the global scope are automatically attached to the ```window``` object. Similarly if you want to create a global from
a variable from in an inner scope, add it to the window object.

Since the jQuery codebase is encapsulated in an [IIFE](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)
to avoid polluting the global namespace, ```jQuery``` and ```$``` are attached to the window object to export
it as a global.

{% codeblock Export jQuery and $ to Global Scope lang:js %}
(function() { // Immediately Invoked Function Expression (IIFE) to encapsulate code base
    // ...
    window.jQuery = window.$ = jQuery; // create alias and export global variables
    // ...
})();
{% endcodeblock %}

Please also note that other libraries such as [prototype](http://prototypejs.org/) do also use
```$``` as a variable, so if you are using  ```$``` in jQuery and other libraries refer to
jQuery's documentation on [avoid conflicts with other libraries](http://learn.jquery.com/using-jquery-core/avoid-conflicts-other-libraries/).

## Prototype Alias


If you've ever tried creating you're own [jQuery plugin](http://learn.jquery.com/plugins/basic-plugin-creation/)
you'll notice that you add the plugin to ```$.fn```.

As an example for [jQuery's plugin tutorial](http://learn.jquery.com/plugins/basic-plugin-creation/),
creating a plugin called ``` greenify ``` that makes retrieved text green would work like so:

{% codeblock jQuery example plugin: greenify lang:js %}
    $.fn.greenify = function() {
        this.css( "color", "green" );
    };
    $( "a" ).greenify(); // Makes all the links green.
{% endcodeblock %}

In fact creating a plugin actually attaches greenify to jQuery's ```prototype```, because ```$.fn``` is an alias for prototype.
Recall that an object inherit methods and properties from their [prototype](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Inheritance_and_the_prototype_chain).
Since, you're plugin is attached to the prototype, you can invoke the plugin after creating you're jQuery instance.

The [related line](https://github.com/jquery/jquery/blob/master/src/core.js#L39) that creates this aliasing is as follows:

{% codeblock jQuery.prototype aliasing lang:js %}

jQuery.fn = jQuery.prototype = {
    // ...
}

{% endcodeblock %}
**Source Code:** [src/core.js](https://github.com/jquery/jquery/blob/master/src/core.js#L39)

Just to verify that jQuery.fn is indeed an alias for jQuery.prototype, we can check that
their memory addresses are equal.
{% codeblock verify jQuery.fn is an alias for jQuery.prototype lang:js %}

jQuery.fn === jQuery.prototype // true

{% endcodeblock %}

Using ``` fn ``` as an alias for ``` prototype ``` is a very convenient shorthand.
I believe ``` fn ``` (function) also makes sense semantically because when you
are creating a plugin, you are essentially adding another method to the jQuery Object.


## Method Chaining

Many of jQuery's method's return ```this``` (referring to the jQuery object)

For example **addClass**
{% codeblock addClass returns this lang:js%}
    addClass: function( value ) {
        // ...
        return this;
    }
{% endcodeblock %}

[**Source Code**](https://github.com/jquery/jquery/blob/master/src/attributes/classes.js#L10-L52)

Since ```this``` is a reference to the jQuery object, when a method returns ``` this ```, you
can immediately invoke another method like so:

{% codeblock Method Chaining Example lang:js %}

$(".box").addClass("red").css("opacity", 0.7);

{% endcodeblock %}

## jQuery Constructor

Every time you invoke ``` jQuery ``` or ```$```, you are invoking a constructor.
Often times you initialize an object by invoking the constructor with the ``` new ``` keyword.
jQuery hides this by calling ``` new jQuery.fn.init(selector, context) ``` internally, when ``` jQuery ``` is invoked.

{% codeblock jQuery Constructor lang:js %}
// Define a local copy of jQuery
jQuery = function( selector, context ) {
    // The jQuery object is actually just the init constructor 'enhanced'
    // Need init if jQuery is called (just allow error to be thrown if not included)
    return new jQuery.fn.init( selector, context );
}
{% endcodeblock %}

**Source Code:** [src/core.js](https://github.com/jquery/jquery/blob/master/src/core.js#L19-L24)

Since **jQuery.fn.init** is the internal constructor that is used to initialize the jQuery object,
the prototype of init
{% codeblock Set fn.init.prototype to jQuery.fn lang:js%}

// Give the init function the jQuery prototype for later instantiation
init.prototype = jQuery.fn;

// verify that they are indeed equal
jQuery.fn.init.prototype === jQuery.fn && jQuery.fn === jQuery.prototype


{% endcodeblock %}

**Source Code:** [src/core/init.js](https://github.com/jquery/jquery/blob/master/src/core/init.js#L118-L119)


### Function Overloading

Many programming languages support a feature known as [**function overloading**](http://msdn.microsoft.com/en-us/library/5dhe1hce.aspx)    
which allows for more than one function of the same name in the same scope as long as they differ by certain criteria.
According to this [function overloading table](http://msdn.microsoft.com/en-us/library/5dhe1hce.aspx) from Microsoft,
the parts of a function declaration that are used to differentiate between functions within the same scope
include **number of arguments**, **type of arguments**, **presence or absence of ellipsis**  and **const or volatile**.

Although this feature is not explicitly supported in JavaScript, John Resig's describe a method for [simulating overloading by number of arguments](http://ejohn.org/blog/javascript-method-overloading/).

jQuery also supports function overloading by checking the type of arguments and the [jQuery() documentation](http://api.jquery.com/jQuery/)
discusses such usage in more detail.

Notice the **// HANDLE ** comments within the source code
that account for all the different cases.

{% codeblock jQuery.fn.init lang:js %}
init = jQuery.fn.init = function( selector, context ) {
        var match, elem;

        // HANDLE: $(""), $(null), $(undefined), $(false)
        if ( !selector ) {
            return this;
        }

        // Handle HTML strings
        if ( typeof selector === "string" ) {
            if ( selector[0] === "<" &&
                selector[ selector.length - 1 ] === ">" &&
                selector.length >= 3 ) {

                // Assume that strings that start and end with <> are HTML and skip the regex check
                match = [ null, selector, null ];

            } else {
                match = rquickExpr.exec( selector );
            }

            // Match html or make sure no context is specified for #id
            if ( match && (match[1] || !context) ) {

                // HANDLE: $(html) -> $(array)
                if ( match[1] ) {
                    context = context instanceof jQuery ? context[0] : context;

                    // Option to run scripts is true for back-compat
                    // Intentionally let the error be thrown if parseHTML is not present
                    jQuery.merge( this, jQuery.parseHTML(
                        match[1],
                        context && context.nodeType ? context.ownerDocument || context : document,
                        true
                    ) );

                    // HANDLE: $(html, props)
                    if ( rsingleTag.test( match[1] ) && jQuery.isPlainObject( context ) ) {
                        for ( match in context ) {
                            // Properties of context are called as methods if possible
                            if ( jQuery.isFunction( this[ match ] ) ) {
                                this[ match ]( context[ match ] );

                            // ...and otherwise set as attributes
                            } else {
                                this.attr( match, context[ match ] );
                            }
                        }
                    }

                    return this;

                // HANDLE: $(#id)
                } else {
                    elem = document.getElementById( match[2] );

                    // Support: Blackberry 4.6
                    // gEBID returns nodes no longer in the document (#6963)
                    if ( elem && elem.parentNode ) {
                        // Inject the element directly into the jQuery object
                        this.length = 1;
                        this[0] = elem;
                    }

                    this.context = document;
                    this.selector = selector;
                    return this;
                }

            // HANDLE: $(expr, $(...))
            } else if ( !context || context.jquery ) {
                return ( context || rootjQuery ).find( selector );

            // HANDLE: $(expr, context)
            // (which is just equivalent to: $(context).find(expr)
            } else {
                return this.constructor( context ).find( selector );
            }

        // HANDLE: $(DOMElement)
        } else if ( selector.nodeType ) {
            this.context = this[0] = selector;
            this.length = 1;
            return this;

        // HANDLE: $(function)
        // Shortcut for document ready
        } else if ( jQuery.isFunction( selector ) ) {
            return rootjQuery.ready !== undefined ?
                rootjQuery.ready( selector ) :
                // Execute immediately if ready is not present
                selector( jQuery );
        }

        if ( selector.selector !== undefined ) {
            this.selector = selector.selector;
            this.context = selector.context;
        }

        return jQuery.makeArray( selector, this );
    };
{% endcodeblock %}
**Source Code:** [src/core/init.js](https://github.com/jquery/jquery/blob/master/src/core/init.js#L16-L116)

### jQuery's Array-like properties

An interesting feature of the jQuery is that **instances
have Array like properties** as each instance
has a **length property** and stores its elements in **numeric
indices** for its elements.

jQuery's Array-like behavior meshes well with method chaining,
because for method's that return ```this``` or the instance,
you now have the option to not only call another instance method
but also to access selected elements.


{% codeblock Method Chaining and jQuery's Array-like properties lang:js%}

// Assume we have html divs with a box class

// we can call a method after the constructor returns
$(".box").addClass("red");

// we can access an element
$(".box")[0] // get the first box

// we can chain methods to gether
$(".box").addClass("red").css("opacity", 0.7);

// add class to all boxes and get the first elemeent
$(".box").addClass("foo")[0];

{% endcodeblock %}

jQuery has an instance method called [get](http://api.jquery.com/get/)
that retrieves the elements matched from jQuery,
using the array like properties we discussed above.

Note that this is different static [jQuery.get](http://api.jquery.com/jquery.get/)
that is used to laod data from the server with a HTTP GET request.

{% codeblock jQuery.fn.get  lang:js %}

// Get the Nth element in the matched element set OR
// Get the whole matched element set as a clean array
get: function( num ) {
    return num != null ?

        // Return just the one element from the set
        ( num < 0 ? this[ num + this.length ] : this[ num ] ) :

        // Return all the elements in a clean array
        slice.call( this );
},

{% endcodeblock %}

**Source Code:** [src/core.js](https://github.com/jquery/jquery/blob/master/src/core.js#L55-L65)


### Constuctor Walkthroughs


#### TODO

{% codeblock jQuery.fn.find lang:js %}
jQuery.fn.extend({
    find: function( selector ) {
        var i,
            len = this.length,
            ret = [],
            self = this;

        if ( typeof selector !== "string" ) {
            return this.pushStack( jQuery( selector ).filter(function() {
                for ( i = 0; i < len; i++ ) {
                    if ( jQuery.contains( self[ i ], this ) ) {
                        return true;
                    }
                }
            }) );
        }

        for ( i = 0; i < len; i++ ) {
            jQuery.find( selector, self[ i ], ret );
        }

        // Needed because $( selector, context ) becomes $( context ).find( selector )
        ret = this.pushStack( len > 1 ? jQuery.unique( ret ) : ret );
        ret.selector = this.selector ? this.selector + " " + selector : selector;
        return ret;
    }
});
{% endcodeblock %}
{% codeblock jQuery.pushStack lang:js %}
    // Take an array of elements and push it onto the stack
    // (returning the new matched element set)
    pushStack: function( elems ) {

        // Build a new jQuery matched element set
        var ret = jQuery.merge( this.constructor(), elems );

        // Add the old object onto the stack (as a reference)
        ret.prevObject = this;
        ret.context = this.context;

        // Return the newly-formed element set
        return ret;
    },
{% endcodeblock %}
{% codeblock jQuery.merge lang:js %}
merge: function( first, second ) {
        var len = +second.length,
            j = 0,
            i = first.length;

        for ( ; j < len; j++ ) {
            first[ i++ ] = second[ j ];
        }

        first.length = i;

        return first;
}
{% endcodeblock %}
jQuery merge



## Static Methods

jQuery has [static methods](http://en.wikipedia.org/wiki/Method_%28computer_programming%29#Static_methods)
which do not require an instance of jQuery to operate.
Many of these [methods](https://github.com/jquery/jquery/blob/master/src/core.js#L191-L460) are utility methods 
such as **isArray** or **each**.


{% codeblock Static Method Example: $.each lang:js %}
$.each([1,2,3], function(i, el){
   console.log(el);
});
// 1
// 2
// 3
{% endcodeblock %}

An interesting thing to note is that there are **static methods** 
and **instance methods** in the codebase that have the same name.

Many of these instance methods also invoke the static methods
within their definition.

{% codeblock jQuery.fn.each instance method lang:js %}
// ...
each: function( callback, args ) {
    return jQuery.each( this, callback, args ); // use static each
},
// ...
{% endcodeblock %}
**Source:** [jQuery.fn.each](https://github.com/jquery/jquery/blob/master/src/core.js#L85-L87) [jQuery.each](https://github.com/jquery/jquery/blob/master/src/core.js#L278-L326)
{% codeblock jQuery.fn.find instance method lang:js %}
find: function( selector ) {
    var i,
        len = this.length,
        ret = [],
        self = this;

    // ...
    for ( i = 0; i < len; i++ ) {
        jQuery.find( selector, self[ i ], ret ); // use static jQuery.find
    }

    // ...
}
{% endcodeblock %}
**Source:** [jQuery.find](https://github.com/jquery/jquery/blob/master/src/selector-sizzle.js#L6)  [jQuery.fn.find](https://github.com/jquery/jquery/blob/master/src/traversing/findFilter.js#L55-L78)

## Implicit Iteration

### TODO


