---
layout: post
title: "Ruby Jumpstart"
date: 2013-04-06 00:16
comments: true
categories: 
---
 This posts is an introduction to Ruby and aggregates information I have learned from [Codeacademy](http://www.codecademy.com/) and
 [Programming the World Wide Web](http://www.amazon.com/Programming-World-Wide-Web-7th/dp/01326658160) and [Agile Developement with Rails](http://www.amazon.com/Agile-Web-Development-Rails-Programmers/dp/097669400X)
 
 Please visit [here](http://www.ruby-lang.org/en/documentation/) for a full list of documentation and sources on the [ruby language](http://www.ruby-lang.org/en/).
 Another interesting resource is the [Ruby's User Guide](http://www.rubyist.net/~slagell/ruby/index.html), originally written by Yukhiro Matsumoto.
- - -


## Table of Contents

1 - [Origins and Uses of Ruby](#origin)  
2 - [Naming Conventions](#naming)  
3 - [Scalars](#scalars)  
4 - [Input and Output](#io)  
5 - [Control Statements](#control_statements)  
6 - [Code Blocks and Iterators](#blocks)  
7 - [Procs and Lambdas](#procs_and_lambdas)
8 - [Control Statements](#control_statements)  
9 - [Fundamentals of Arrays](#arrays)  
10 - [Hashes](#hash)  
11 - [Methods](#methods)  
12 - [Sorting](#sorting)    
13 - [Pattern Matching](#pattern)   
14 - [Classes](#classes)  
15 - [Modules](#modules)  

  
  
### <a id="origin"></a> Origins and Uses of Ruby
  Ruby was created in Japan by [Yuki "matz" Matsumoto](http://www.rubyist.net/~matz/) and was publicly released in 1995. Ruby blended parts of his favorite languages (Perl, Smalltalk, Eiffel, Ada, and Lisp) to form a new language that balanced functional programming with imperative programming.[1] Ruby has achieved mass acceptance especially with the quick growth of Rails, the web application framework created by [37signals](http://37signals.com/)

  One interesting aspect about Ruby is that **everything in Ruby is an object** meaning that there are instance variables with methods. Recall that in other languages such as C, primitive data types (like integers and characters) are not objects.

  1.[http://www.ruby-lang.org/en/about/](http://www.ruby-lang.org/en/about/)

{% blockquote %}
  Everything in Ruby is an object.
{% endblockquote %}



### <a id="naming"></a> Naming Conventions
Local variables, method parameters, and method names should all start with a lower case letter or underscore: box, water\_bottle, g6.
Instance variables should begin with an "at"@ sign, such as @id, @telephone\_number
The convention is to use underscores to separate words in a multiword method or variable name water\_bottle as opposed to waterBottle.    

Class names, module names, and constants muststart with an uppercase letter. By convention they use capitalization, rather than underscores, to distinguish the start of words within the name. Class names look like Object, PurcharseOrder, and LineItem. This naming convention is also known as **upper camel case**, since the shape of the lettering looks like the humps of camel.  

Rails uses *symbols* to identify things. In particular, symbols are used when naming method parameters and lookingthings up in [hashes](#hash)
Symbols are prefixed with colons : like :action or :id

{% codeblock Naming Examples lang:ruby %}

#local variables method parameters
watch
ti89
xbox360
@zip\_code

#Class Names, module names, constants
Shape
MusicInstrument

#symbols
:id
#symbol Example
redirect\_to :action => "edit", :id => params[:id]  

# Replace \_ with underscore. Need \ to escape underscore in markdown.
{% endcodeblock %}

{% blockquote %}
By convention class names, modules names and constants use capitalization to distinguish the start of words within the name. This is known as upper camelcase
because of its shape.
{% endblockquote %}

### <a id="scalars"></a> Scalars 

Ruby has three categories of datatypes - **scalars,arrays and hashes**. There are two categories of scalar types numerics and character strings. Recall that everything in Ruby is an object.

#### Numeric Literals
  All numeric data types in Ruby are descendants of the [Numeric](http://ruby-doc.org/core-2.0/Numeric.html) class. The immediate child classes of Numeric are [Float](http://www.ruby-doc.org/core-2.0/Float.html) and [Integer](http://ruby-doc.org/core-2.0/Integer.html). The Integer class has two child classes [Fixnum](http://www.ruby-doc.org/core-2.0/Fixnum.html) and [Bignum](http://ruby-doc.org/core-2.0/Bignum.html).  
  An integer literal that fits into the range of a machine word, which is often 32 bits, is a Fixnum object (minus 1 bit). If the integer exceeds the size of the the Fixnum object then it is coereced into a Bignum object. There is no length limitation(other than the computer's memory size) on integer literals.  
  
  Underscore characters can appear embedded in integer literals, which helps readability.

  {% codeblock Underscores in numeric literals lang:ruby%}

  1\_000 * 3
   #=> 3000 

  # Replace \_ with underscore. Need \ to escape underscore in markdown.
  {% endcodeblock %}

  A numeric literal that has either an **embedded decimal point** or a **following exponent** is a [Float](http://www.ruby-doc.org/core-2.0/Float.html) object and uses the machines double precision floating point architecture. A decimal point must be both preceded and followed by at least one digit so .35 is not valid.You must use 0.35. 
   {% blockquote %}
   A decimal point must be both preceded and followed by at least one digit.  
   {% endblockquote %}

#### String Literals
  All string literals are [String](http://ruby-doc.org/core-2.0/String.html). There are two categories of string literals, single quoted and double quoted. Single quoted literals cannot include characters(other than single quote characters) specified with escape characters. You can specify a single quote delimiter by preceding the delimiter with a **%q**. If the new delimiter is a parantheses, brace, bracket, or a point bracket, the corresponding closing element must be used.
 {% codeblock Single quoted strings lang:ruby %}  
 
  'It\'s good'
  #=> It\'s good

  'Roses are red, \n violets are blue'
  #=> Roses are red, \n violets are blue

  %q$I don't know$
  #=> 'I don't know'

  %q{You don't know?}
  #=> 'You don't know?'

 {% endcodeblock %}

  Double quoted strings differ from single quoted strings in two ways:
  1. They can include special characters in escape sequences
  2. The values of variables names can be interpolated into a string. With **string interpolation** a block of code between  #{ } is evaluated and then converted into a string.

  {% blockquote %}

  With string interpolation a block of code between  #{ } is evaluated and then converted into a string.

  {% endblockquote %}

  Finally a different delimiter can be used by preceding the delimiter with a **%Q**.
 
 {% codeblock Double quoted strings lang:ruby %}


 "That's cool"
 #=> That's cool

 %Q@That's cool@
 #=> That's cool

 "That's \t cool" #escape sequence for tab
 #=> That's     cool

 puts "3 + 3 = #{3 + 3}"
 #=> 3 + 3 = 6
 
 six = 6
 puts "3 + 3 = #{six}"
 #=> 3 + 3 = 6

 {% endcodeblock %}

#### Numeric Operators

Most of the operatoirs in Ruby are similar to those in other common languages like + for addition, - for subtraction, \* for multiplication, / for division.

Note however that Ruby does not support increment(++) and decrement(--) operators.
 Note that ** \*\* ** is the exponentiation operator.


 {% codeblock Exponentation in Ruby lang:ruby%}

 2 ** 3

 #=> 8
 {% endcodeblock %}

 Operators can also serves as methods. \* can be used for repetition and + can be used for string concatenation.


 {% codeblock String Repetition in Ruby lang:ruby%}

 "Hello " * 3

 #=> Hello Hello Hello

 {% endcodeblock %}

{% blockquote  %}
Ruby does not support increment and decrement operators.
{% endblockquote %}

#### String Methods

The +  symbol and <<  symbol are used to **concatenate** strings.

{% codeblock String Concatenation lang:ruby %}
 "Hello" + "World"
 #=> "HelloWorld" 
 "Hello" << "World"
 #=> "HelloWorld"
{% endcodeblock %}

There are many [String](http://ruby-doc.org/core-2.0/String.html) methods in Ruby.

Ruby method names can end with an exclamation mark(a bang or mutator method) or a question mark(a predicate method). A bang method changes the object in place. A predicate method indicates that a method is boolean: returns true or false.

  {% blockquote %}
   A bang method is a method name ending with a ! and changes the the object in place. 
  {% endblockquote %}

  {% codeblock Bang Method lang:ruby %}
  str = "String"
  #=> "String" 
  str.upcase
  #=> "STRING" 
  str # str is not modified
  => "String" 
  str.upcase!
  #=> "STRING" 
  str # str has been modified in place
  #=> "STRING
  {% endcodeblock %}

A few String methods are summarized below.
  
  **capitalize** Convert first letter to uppercase and the rest to lowercase  
  **chop** Removes the last character  
  **chomp** removes a new line fro mthe right end, if there is one   
  **upcase** converts all of the letters in the object to uppercase  
  **downcase** converts all of the letters  
  **strip** Removes the spaces on both ends  
  **lstrip** Removes the spaces on the left end  
  **rstrip** Removes the spaces on the right end  
  **reverse** Reverse the characters of the string  
  **swapcase** Converts all uppercase letters to lowercase and all lowercase letters to uppercase  

 Strings can also be accessed by an index where 0 is the first position. If a negative index is used, the index starts at the back where the last character is -1.
 Strings can also be index like [index,length].

 {% codeblock String Indexing Examples lang:ruby %}

 "12345"[0] 
 #=> 1

 "12345"[-5] 
 #=> 1
 
 "12345"[-5,3]
 #=> "123" 

 {% endcodeblock %}
  





### <a id="io"></a> Input and Output

Sometimes you may want to work with Ruby on the command line. 
One way is to run **irb** on the command line to start the Interactive Ruby Shell and then experimenting with Ruby in the console.
You can execute a script by running  **ruby** *scriptname* 
The **-c** flag can be used to check syntax. For a full set of flags run **man ruby**
You can also execute a script by  putting **#!/usr/bin/env ruby** at the top of your script and running your script like so **./scriptname**
If you use this method make sure that your script is executable by running **chmod +x**. Generally ruby scripts end with the extension **.rb**

#### Output

**print** is used to output a string.
**puts** behaves like print but adds a newline to the end of output.

{% codeblock Output Exaple lang:ruby %}
 print "Hello"
 #Hello => nil 
 puts "Hello"
 #Hello
 #=> nil 
{% endcodeblock %}
{% blockquote %}
puts behaves like print but adds a newline to the end of output
{% endblockquote %}

####Input
**gets** is used to retrieve a line of input from the keyboard.
The parsed input is a string, and you can use string methods to filter the string.
Use methods such as **to_i** or **to_f** to convert the input into an integer or float respectively.

{% codeblock Input Example lang:ruby %}
num = gets.chomp.to\_i
#removes newline character from input and converts input to an integer
#=> 9123 
# Replace \_ with underscore. Need \ to escape underscore in markdown.

{% endcodeblock %}

### <a id="control_statements"></a> Control Statements 
Control Statements are used to control execution flow of a program.

#### Control Expressions
Control Expressions are logical expressions used to for control statments. A relational operator has two operands and an operator
Relational Operator

**==** equals  
**!=** not equals  
**<** less than  
**>** greater than  
**<=** less than or equal to  
**>=** greater than or equal to  
**<=>** Comparator: for a <=> b returns 1 if a > b , -1 if a < b, and  0 if a == b This operator is also very help for sorting.  
**eql?** returns true if parameter and reciever object have same type and value.   
**equal?** returns true if parameter is the same as the reciever object. Effectly checks for same references.  

{% codeblock Relational Operators lang:ruby %}

"thunderstorm".eql?("thunderstorm")
 #=> true 

 "thunderstorm".equal?("thunderstorm")
 #=> false 
 # both thunderstorms refer to different objects

 str = "thunderstorm"
 #=> "thunderstorm" 
 str2 = str
 #=> "thunderstorm" 
 str.equal?(str)
 #=>true

{% endcodeblock %}


##### Operator Precedence and associativity 

**Operator precendence** specifies the order in which operations should be carried out.
The following operators are listed in precedence from greatest to least (in descending order).
Operators on the same line have the same precedence. **Associativity** tells you how to evaluate operations
if two or more operators have the same precendence either start evaluating from the left or right. If operators
are nonassociative that means that these operators cannot associative with themselves.
{% codeblock Associativity Example lang:ruby%}
  3 + 7 - 4
 #=> 6
 #Left Associativity
 #(3 + 7) -4
{% endcodeblock%}

{% codeblock Nonassociative Example lang:ruby %}
 1 < 2 < 3
 # < is a nonassociative operator
 #NoMethodError: undefined method `<' for true:TrueClass

{% endcodeblock %}

\*\* **Right**
{% codeblock  Operator Precedence lang:ruby %}

 2 ** 2 ** 3

 #=> 256
 # 2 ** ( 2 ** 3) => 2 ** 8
{% endcodeblock %}

!, unary + and - **Right**  
Here is a pretty interesting article on how to define your own [unary operator](http://www.rubyinside.com/rubys-unary-operators-and-how-to-redefine-their-functionality-5610.html)
{% codeblock Unary Operators lang:ruby%}
--------+-+ 3
#=> -3
-+3
#=> -3
{% endcodeblock %}

\*, /, % **Left**  
\+, - **Left**   
\& **Left**  
\> , \<, \>= , \<= **Nonassociative**    
==, \!=, \<=> **Nonassociative**  
&& **Left**    
|| **Left**    
=, +=, -=, \*=, \*\*=, /=, %=, &=, &&=, ||= **Right**    
not **Right**  
or, and **Left**  

{% blockquote %}
and and or are control-flow modifiers like if and unless
{% endblockquote %}
More about [using and vs &&](http://devblog.avdi.org/2010/08/02/using-and-and-or-in-ruby/)

#### Selection statements

Selection statements are used for conditional branching of execution.

{% codeblock Selection Statements lang:ruby %}

    if num == 1
      puts "one"
    elsif num == 2
      puts "two"
    elsif num == 3
      puts "three"
    else
      puts "I don't recognize this number"
    end

    case num
    when 1 then puts "one"
    when  2
      puts "two"
    when 3
      puts "three"
    else
       puts "I don't recognize this number"
    end


{% endcodeblock %}

{% blockquote %}
   The case construct is the equivalent of a switch statement in other programming languages.
{% endblockquote %}

The [then](http://stackoverflow.com/questions/948135/how-to-write-a-switch-statement-in-ruby) keyword following the when clauses can be replaced with a newline.

#### Loop Statements

Loop of statements repeatedly executed until a terminiting condition.

{% codeblock Loops in Ruby lang:ruby%}

 i = 10
 loop do  
   i -= 1  
   next if i % 2 == 1 #continue to next iteration if odd
   print "#{i}" #string interpolation
    break if i <= 0 #terminating condition that breaks out of loop
  end

  #=> 86420

  for num in 1...10 #up to but not including (exclusive)
      print num
  end

  #=> 123456789

  for num in 1..10 #up to including (inclusive)
      print num
  end

  #=> 12345678910

{% endcodeblock %}

 {% blockquote %}
    ... and .. are range operators for exclusive and inclusive ranges. 
 {% endblockquote %}

 {% blockquote %}

 next is the equivalent of a continue statement in other programming languages. The program continues to the next iteration of the loop. 

 {% endblockquote %}

Although if statements are fairly common in Ruby Applications, looping constructs are rarely used. Block and iterators often take their place.

{% blockquote %}
  In Ruby, looping constructs are rarely used. Instead, block and iterators take their place.
{% endblockquote %}
### <a id="blocks"></a> Code Blocks and Iterators
**Code Blocks** are groups of code between **do ... end** or **curly braces {}**. By convention **do...end** is used for multiline blocks where as {} is used for single line blocks.
[do/end vs curly braces](http://stackoverflow.com/questions/5587264/do-end-vs-curly-braces-for-blocks-in-ruby)

**Iterators** are methods used to invoke blocks repeatedly.
Note that blocks can accept parameters that are delimited like so **|parameter|**

{% codeblock times Iterator lang:ruby%}
10.times{|x| puts x}

# 0
# 1 
# 2
# 3
# 4
# 5
# 6
# 7
# 8
# 9

{% endcodeblock %}

{% codeblock each Iterator lang:ruby %}

fruits = ["apple","rasberry","lemon","cranberry"]

# each element is fruit is passed as an argument to the block
# fruit is variable name of  the parameter in the block
fruits.each {|fruit| puts "#{fruit} pie"}
# apple pie
# rasberry pie
# lemon pie
# cranberry pie

# returns the fruits array
# => ["apple", "rasberry", "lemon", "cranberry"] 

{% endcodeblock %}


{% codeblock Iterating over Hashes lang:ruby %}

ratings = {
  memento: 3,
  primer: 3.5,
  matrix: 3,
  skyfall: 4,
  uhf: 1,
}

good = ratings.select {|k,ratings| ratings > 3}
#=> {:primer=>3.5, :skyfall=>4} 
#=> is equivalent to { primer: 3.5, skyfall: 4}

ratings = {
  memento: 3,
  primer: 3.5,
  skyfall: 4,
  uhf: 1,
}


ratings.each{|key,value| puts "#{key.capitalize} Ratings: #{value}"}

# Memento Ratings: 3
# Primer Ratings: 3.5
# Skyfall Ratings: 4
# Uhf Ratings: 1

{% endcodeblock %}

**collect** is an iterator that applies the block on each element of the array.
It creates a new array containing the values returned by the block.
If you used the bang method it modifies the array in place.
{% codeblock collect Iterator lang:ruby %}

    fruits = ["apple","rasberry","lemon","cranberry"]
#=> ["apple", "rasberry", "lemon", "cranberry"] 
   fruits.collect {|fruit| "#{fruit} pie"}
#=> ["apple pie", "rasberry pie", "lemon pie", "cranberry pie"] 
#note that a new array was created and fruits has stayed the same
   fruits
#=> ["apple", "rasberry", "lemon", "cranberry"] 
   fruits.collect! {|fruit| "#{fruit} pie"}
#=> ["apple pie", "rasberry pie", "lemon pie", "cranberry pie"] 
#note that because we used the bang method fruits has now changed
    fruits
#=> ["apple pie", "rasberry pie", "lemon pie", "cranberry pie"] 
    fruits.collect {|fruit| "#{fruit} pie"}
#=> ["apple pie pie", "rasberry pie pie", "lemon pie pie", "cranberry pie pie"] 

{% endcodeblock %}

Methods can transfer control to a block and back again. This is can be done using the **yield** method, which invokes the block with an optional parameter.

{% codeblock Yield lang:ruby%}

def yield_num(num)
  puts "inside of method yield_num"
  puts "Yield!"

  yield num
  #passing your own number directly to the block
  yield 20
end

yield_num(5) { |num| puts "Your lucky number is #{num}"}

#=> inside of method yield_num
#=> Yield!
#=> Your lucky number is 5
#=> Your lucky number is 20
{% endcodeblock %}
{% blockquote %}
Methods can transfer control to a block and back again. This is known as the yield method, which invokes the block with an optional parameter.
{% endblockquote %}
### <a id ="procs_and_lambdas"></a> Procs and Lambdas
#### TODO
#### Proc

**Procs** are full-fledged objects, so they have all the powers and abilities of objects. (Blocks do not.)  

Unlike blocks, **procs can be called over and over without rewriting them**. This prevents you from having to retype the contents of your block every time you need to execute a particular bit of code.


http://www.robertsosinski.com/2008/12/21/understanding-ruby-blocks-procs-and-lambdas/
http://stackoverflow.com/questions/1435743/why-does-explicit-return-make-a-difference-in-a-proc
http://ablogaboutcode.com/2012/01/04/the-ampersand-operator-in-ruby/


#### lambda
First, a lambda checks the number of arguments passed to it, while a proc does not. This means that a lambda will throw an error if you pass it the wrong number of arguments, whereas a proc will ignore unexpected arguments and assign nil to any that are missing.

Second, when a lambda returns, it passes control back to the calling method; when a proc returns, it does so immediately, without going back to the calling method.

### <a id="arrays"></a> Fundamentals of Arrays
An **array** is a series of elements. If you have arrays of arrays this is known as a **multi-dimensional array**. Array elements are indexed by integers with the first element being 0. 

Ruby arrays differs from more traditional langauges such as C,C++, and Java because the lenght of an array is dynamic. They can shrinkand grown during program execution. If you've worked with C++, this is the same behaviior as a vector.

Generally arrays must store homogenous elements (elements of the same type). However Ruby arrays store heterogenous elements or a mix of elements.

{% codeblock Arrays lang:ruby %}

Array.new(5)
#=>[nil, nil, nil, nil, nil] 

Arr.new(5,"fivetimes")
#=> ["fivetimes", "fivetimes", "fivetimes", "fivetimes", "fivetimes"] 

[1,2]
#=> [1, 2] 

[1,2][0] # get the first element from array
#=> 1 


Array.new(5,[1,2])
# => [[1, 2], [1, 2], [1, 2], [1, 2], [1, 2]] 

{% endcodeblock%}

#### for-in
One method to loop over an array is to use the for-in statement.
{% codeblock for-in lang:ruby%}
arr = ["one","two","three","four"]

for x in arr
  puts x
end

#=>one
#=>two
#=>three
#=>four

multi = Array.new(5,[1,2])
# => [[1, 2], [1, 2], [1, 2], [1, 2], [1, 2]] 
for arr in multi # get nested array
  print "[ "
  for val in arr # get values from nested array
    print "#{val} "  
  end
  puts "]"
end

#=> [ 1 2 ]
#=> [ 1 2 ]
#=> [ 1 2 ]
#=> [ 1 2 ]
#=> [ 1 2 ]
{% endcodeblock %}

[Arrays](http://ruby-doc.org/core-2.0/Array.html) have an assortment of methods. Some common methods
are demoed below.

{% codeblock Array methods lang:ruby %}

[1,2,3,4] + [13] # concatenate the arrays
#=> [1, 2, 3, 4, 13] 

[1,2,3,4] << [13] # append [13] to array
#=> [1, 2, 3, 4, [13]] 

[1,2,3,4] << 13   # append 13 to array
#=> [1, 2, 3, 4, 13] 

arr = [1,2,3,4]
arr.push(13) # adds 13 to right side of array
#=> [1, 2, 3, 4, 13] 

arr.pop # removes first element from right side
#=> 13

arr
#=> [1,2,3,4] 
# 13 is gone!

arr.unshift(13) # adds 13 to left side of array
#=> [13, 1, 2, 3, 4]

arr.shift #remove first element from left side
#=> 13 
arr
#=> [1, 2, 3, 4] 

arr.shift(2) #remove 2 more elements from left side
#=> [1, 2] 
arr
#=> [3, 4]

arr = [1,2,3,4]
arr.reverse
#=> [4, 3, 2, 1] 
arr
#=> [1, 2, 3, 4] #note that the original array has not changed 
arr.reverse!
#=> [4, 3, 2, 1] 
 arr
#=> [4, 3, 2, 1] #note that the original array changed because we called a ! method 

{% endcodeblock%}

Arrays can also be treated as [sets](http://mathworld.wolfram.com/Set.html)
The & operator is used for set intersection, -, for set difference, and |, for set union

{% codeblock Sets lang:ruby%}

[1,2,3,4] & [2,45,6,7,8,9] # set intersect
#=> [2] 

[1,2,3,4] - [2,45,6,7,8,9] # set difference
#=> [1, 3, 4] 

[1,2,3,4] | [2,45,6,7,8,9] # set union
#=> [1, 2, 3, 4, 45, 6, 7, 8, 9] 


{% endcodeblock %}

### <a id="hash"></a> Hashes 
Arrays are indexed by integers.
There is another key, value collection called a **hash** where an object is reference by another object known as a **symbol**.
If you're coming from a background in JavaScript, PHP, you maybe used to acessing hashes using strings. 

{% codeblock Hashes lang:ruby %}
# this is the old syntax for a ruby symbols
# symbols are declared as :symbol
oldnumhash = { 
    :four => 4,
    :five =>  5,  
    :six => 6
}

oldnumhash.each{|k,v| puts "#{k} maps to #{v}"}
#=> four maps to 4
#=> five maps to 5
#=> six maps to 6

# Ruby 1.9 introduced an even simpler syntax
# Notice that this the colon (:) comes right after the key
numhash = { 
    one: 1,
    two: 2,
    three: 3
}

puts numhash[:one]
#=> 1
puts numhash[:two]
#=> 2
puts numhash[:three]
#=> 3
{% endcodeblock %}

Here is a good description of why symbols are used as hash keys in Ruby
{% blockquote %}

"Using symbols not only saves time when doing comparisons, but also saves memory, because they are only stored once.

Symbols in Ruby are basically "immutable strings" . That means that they can not be changed, and it implies that the same symbol when referenced many times throughout your source code, is always stored as the same entity, e.g. has the same object id.

Strings on the other hand are mutable, they can be changed anytime. This implies that Ruby needs to store each string you mention throughout your source code in it's separate entity, e.g. if you have a string "name" multiple times mentioned in your source code, Ruby needs to store these all in separate String objects, because they might change later on (that's the nature of a Ruby string).

If you use a string as a Hash key, Ruby needs to evaluate the string and look at it's contents (and compute a hash function on that) and compare the result against the (hashed) values of the keys which are already stored in the Hash.

If you use a symbol as a Hash key, it's implicit that it's immutable, so Ruby can basically just do a comparison of the (hash function of the) object-id against the (hashed) object-ids of keys which are already stored in the Hash. (much faster)

Notes:

If you do string comparisons, Ruby can compare symbols just by their object ids, without having to evaluate them. That's much faster than comparing strings, which need to be evaluated.

If you access a hash, Ruby always applies a hash-function to compute a "hash-key" from whatever key you use. You can imagine something like an MD5-hash. And then Ruby compares those "hashed keys" against each other." 
{% endblockquote %}

**Source:**  [tilo's](http://stackoverflow.com/users/677684/tilo) answer from [http://stackoverflow.com/questions/8189416/why-use-symbols-as-hash-keys-in-ruby](http://stackoverflow.com/questions/8189416/why-use-symbols-as-hash-keys-in-ruby)


Each object is identified by an integer which can be retrieved using the method [.object_id](http://ruby-doc.org/core-2.0/Object.html#method-i-object_id).
As mentioned previously **only one copy of a symbol exists at a given time**, meaning that there exists only one object id for that symbol.

{% codeblock Object id lang:ruby %}

# note that these string objects are different instances 
  puts "string".object_id
  puts "string".object_id

# these ids are different
#=>79341770
#=>79341710


# note that only one copy of the symbol :symbol exists at a time
puts :symbol.object_id
puts :symbol.object_id

# these ids are the same
#=>192008
#=>192008
{% endcodeblock %}



### <a id="methods"></a> Methods
#### TODO
### <a id="sorting"></a> Sorting
#### TODO
### <a id="pattern"></a> Pattern Matching
#### TODO
### <a id="classes"></a> Classes
#### TODO
### <a id="modules"></a> Modules
#### TODO
