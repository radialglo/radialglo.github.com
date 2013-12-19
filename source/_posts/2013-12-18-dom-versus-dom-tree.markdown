---
layout: post
title: "DOM versus DOM Tree"
date: 2013-12-18 20:23
comments: true
categories: 
---
### Context
A fellow classmate asked the following question recently on <a href="http://piazza.com" target="_blank">Piazza</a>
{% blockquote %}
Is the value of a attribute in a element become a node in HTML DOM?
e.g. &lt;a href=&quot;good&quot;&gt;&lt;/a&gt; is "good" a text node or something else?
{% endblockquote %}

Unfortunately, I was stumped on the question and decided it was time to finally begin reading the DOM specifications also known as [**DOM TR(Technical Reports)**](http://www.w3.org/DOM/DOMTR) to get a deeper technical understanding of the DOM.

The current specification is **DOM4**. The most up to date version of this spec is available as the  [**DOM Living Standard**](http://dom.spec.whatwg.org/) 

One of the things I noticed is that many developers use the term DOM in place of where **DOM Structure Model** or **DOM tree**  should actually be used.
This distinction between these two terms is  crucial in answering the question.

### Response

It's important to distinguish between 
the **DOM** and the **DOM tree**. 

**The DOM is a programming API for documents.**

According to the spec, 
*The Document Object Model (DOM) is an application programming interface (API) for valid HTML and well-formed XML documents.*[http://www.w3.org/TR/2004/REC-DOM-Level-3-Core-20040407/introduction.html](http://www.w3.org/TR/2004/REC-DOM-Level-3-Core-20040407/introduction.html)

**The DOM tree refers to the structure model of the document(not the DOM).** 

According to the spec, 
*In this specification, we use the term structure model to describe the tree-like representation of a document. 
We also use the term "tree" when referring to the arrangement of those information items which can be reached 
by using "tree-walking" methods; (this does not include attributes).* 
[http://www.w3.org/TR/2004/REC-DOM-Level-3-Core-20040407/introduction.html](http://www.w3.org/TR/2004/REC-DOM-Level-3-Core-20040407/introduction.html)


**In other words, attribute is considered a node in the DOM(API for documents), 
but not a node in the DOM tree(structure of documents).**

More specifically from the spec,*
Attr objects inherit the Node interface, but since they are not actually child nodes of the element they describe, the DOM does not consider them part of the document tree. Thus, the Node attributes parentNode, previousSibling, and nextSibling have a null value for Attr objects.* 
[http://www.w3.org/TR/2004/REC-DOM-Level-3-Core-20040407/core.html#ID-637646024](http://www.w3.org/TR/2004/REC-DOM-Level-3-Core-20040407/core.html#ID-637646024)

I did a search for HTML DOM, and none of the technical specifications refer to the any specification as HTML DOM, except
for W3Schools.Be cautious with the wording of W3Schools [http://www.w3schools.com/js/js_htmldom.asp](http://www.w3schools.com/js/js_htmldom.asp). 
W3Schools is not affiliated with W3C and can occasionally be incorrect with some of these nuances. 
There is a site created by some of the industries leading front end engineers discussing this: 
[http://www.w3fools.com/](http://www.w3fools.com/)

**To answer the question is a text node created for an attribute?, the answer is yes.** 
From the spec,
*On setting(the node value), this creates a Text node with the unparsed contents of the string. 
I.e. any characters that an XML processor would recognize as markup are instead 
treated as literal text. See also the method setAttribute on the Element interface.*
[http://www.w3.org/TR/2004/REC-DOM-Level-3-Core-20040407/core.html#ID-637646024](http://www.w3.org/TR/2004/REC-DOM-Level-3-Core-20040407/core.html#ID-637646024)


Finally some JavaScript to illustrate this!
{% codeblock DOM versus DOM Tree lang:html %}

<!DOCTYPE html>
<html>
<body>

<div id="top_bar">
    <div class="top_bar_left clearFix">
      <div class="top_bar_logo"></div>
     </div> 
</div>

<script type="text/javascript">

  // https://developer.mozilla.org/en-US/docs/Web/API/Node.nodeType
  var NODE_TYPE = {
    ELEMENT: 1,
    ATTR: 2,
    TEXT: 3
    // ...
  };

  console.log ("INSPECTING DOM TREE ...");
      // top navigation bar in piazza
  var top_bar = document.getElementById("top_bar"),
      // get it's children
      // convert array-like NodeList to Array
      children = Array.prototype.slice.call(top_bar.childNodes),
      attr_children_count = 0;
   
    // iterate all the children of top_bar
    children.forEach(function(child) {
      if (child.nodeType === NODE_TYPE.ATTR) {
        attr_children_count += 1;
      }
      console.log(child);
    });

  // no attribute nodes in DOM tree
  if (attr_children_count === 0) {
    console.log("Attributes are not considered children in the DOM tree");
  }


  console.log ("INSPECTING ATTR NODE ...");
  // now let's inspect the attribute node id
  var id_node = top_bar.getAttributeNode("id");
  // get it's children
  // convert array-like NodeList to Array
  id_node_children = Array.prototype.slice.call(id_node.childNodes);
  id_node_children.forEach(function(child) {
      // confirm that id node has child text node
      console.log(child.nodeValue); // "top_bar"
      console.log(child.nodeType); // 3 => indicates text node
  });

</script>

</body>
</html>

{% endcodeblock %}


### Note on DOM4
In DOM4, an Attribute object, no longer extends a node object so it is not considered a node in the DOM(API for documents) for the DOM4 Spec.
You may also notice that the nodeType ATTRIBUTE\_NODE is considered deprecated. 
[https://developer.mozilla.org/en-US/docs/Web/API/Node.nodeType](https://developer.mozilla.org/en-US/docs/Web/API/Node.nodeType)
