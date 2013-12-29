---
layout: post
title: "Attribute Nodes of HTML Elements"
date: 2013-12-29 04:35
comments: true
categories: 
---

### Context

The following question was asked recently on <a href="http://piazza.com" target="_blank">Piazza</a>

{% blockquote %}
Do JavaScript event attributes (in addition to other HTML attributes, like "id", "style", etc), onclick for example, create non-child attribute nodes of the associated html element?
{% endblockquote %}

For example:
 
{% codeblock Diagram of Question %}

<body id="test" onclick="doSomething();">
    This is <b>a</b> test.
</body>
 
Would this create 7 nodes?
              (onclick)  <--  (body) --> (id)
                             /   |   \
                   ("this is")  (b)  ("test")
                                 |
                               ("a")
{% endcodeblock %}
### Response

#### Short answer:
**Yes**
#### Long Answer:

** Yes, but be aware... **
There are **different ways to add event handlers to an element**, 
and not all methods introduce an attribute node. 
The two ways of **adding event handlers by creating an attribute node** are

 1. **directly in HTML**  (what you did)
 2. using JavaScript such as the method **setAttribute** .

However there are still different ways to add event handlers that don't introduce attribute nodes.

As as side note, there are 5 versions of the the DOM specification (DOM Level 0 -3)
and the current version DOM4 also known as the DOM Living Standard (http://dom.spec.whatwg.org/)
An interesting to note is that the **DOM Level 2** introduced the **Events specification** [http://www.w3.org/TR/DOM-Level-2-Events/events.html](http://www.w3.org/TR/DOM-Level-2-Events/events.html)



*In particular under the section 1.3.2. Interaction with HTML 4.0 event listeners,*
{% blockquote %}
"In HTML 4.0, <b>event listeners were specified as attributes of an element. As such, registration of a second event listener of the same type would replace the first listener.</b>The <b>DOM Event Model allows registration of multiple event listeners</b> on a single EventTarget. To achieve this, event listeners are no longer stored as attribute values. 

In order to achieve compatibility with HTML 4.0, implementors may view the setting of attributes which represent event handlers as the creation and registration of an EventListener on the EventTarget. The value of useCapture defaults to false. This EventListener behaves in the same manner as any other EventListeners which may be registered on the EventTarget. If the attribute representing the event listener is changed, this may be viewed as the removal of the previously registered EventListener and the registration of a new one. No technique is provided to allow HTML 4.0 event listeners access to the context information defined for each event.
"
{% endblockquote %}

We didn't really talk about (in class) adding event listeners and the difference between doing it in HTML versus JavaScript,
but it's **generally bad practice** to introduce event handlers in the HTML, because we want to **separate the concerns** of our structure(HTML) and our behavior(JavaScript).
Otherwise there is too much coupling .(This is also why CSS stylesheets are often used instead of inline CSS). There are also other reasons.
This concept is known as **unobtrusive JavaScript**, and you can read more about this here:[http://www.w3.org/wiki/The_principles_of_unobtrusive_JavaScript](http://www.w3.org/wiki/The_principles_of_unobtrusive_JavaScript).
Finally let's illustrate this through some HTML and JavaScript! Copy paste the code below into an .html file, and then open it in a browser. Click on the body and view console to see what exactly happens.

{% codeblock DOM versus DOM Tree lang:html %}
<!DOCTYPE html>

<html>

<head>

  <script type="text/javascript">

  

    // dom levels https://developer.mozilla.org/en-US/docs/DOM_Levels

    // DOM Living Standard: http://dom.spec.whatwg.org/

    // http://www.w3.org/wiki/The_principles_of_unobtrusive_JavaScript



    var list = null;

    function log(txt) {

      console.log(list);

      var li = document.createElement("li");

      li.appendChild(document.createTextNode(txt));

      list.appendChild(li);

    }


    window.onload = function() {

      var doc = document,
          body = doc.body;

      // get the list node, when the page loads
      list = doc.getElementById("list");

      body.click();

      console.log(body.attributes["onclick"]); // it exists 
      console.log(body.getAttribute("onclick")); // it exists 


      // remove inline onclick attribute node
      body.removeAttribute("onclick");
      

      /*

        .attributes is a collection of all attribute nodes registered to the specified node.

        getAttribute is used to get the corresponding attribute node

      */



      
      // sets onclick attribute but doesn't introduce an attribute node
      body.onclick = function() {

        log("I am not an attribute node. Event added via .onclick");

      }
      body.click();
      

      console.log(body.getAttribute("onclick")); // null


      // now we can add another event via DOM Level 2 Specification
      // http://www.quirksmode.org/js/events_advanced.html

      body.addEventListener("click",function() {

        log("I am not an attribute node. Event added via addEventListener.");

      });


      body.click();
      console.log(body.getAttribute("onclick")); // null

    }

  </script>

</head>

<body onclick="log('I am an attribute node');">

  <ol id="list">

  </ol>

</body>

</html>

{% endcodeblock %}
