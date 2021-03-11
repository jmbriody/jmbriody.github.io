---
layout: single
title: "Closures or: How I learned to stop classing and love the function"
date:   2017-08-01
categories: python Closures
tags: python Closures
---
## Python Closures
I'm not going to say closures are simple--if you come from an object oriented background or are starting off with no background and learning a functional language they can be a bit of a mind-bender. Once you get past the world of syntax (the "ifs", "whiles", and "fors") you essentially enter the world of "how do I put all that together?". For languages with functions as "first-class objects" (FCOs) you'll probably encounter closures or closure like constructs.

You could probably get away with writing a ton of code in python without ever using a closure--but they can be a great tool to make your code simpler, more useful, and easier to extend and maintain. I don't think you'd survive reading or writing Java Script without closures. I'm going to, however, focus on python.

$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$

## Wikipedia != ELI5
Wikipedia starts their article on closures with . . .
> In programming languages, closures (also lexical closures or function closures) are techniques for implementing lexically scoped name binding in languages with first-class functions. Operationally, a closure is a record storing a function[a] together with an environment:[1] a mapping associating each free variable of the function (variables that are used locally, but defined in an enclosing scope) with the value or reference to which the name was bound when the closure was created.[b] A closure—unlike a plain function—allows the function to access those captured variables through the closure's copies of their values or references, even when the function is invoked outside their scope.

. . . which is pretty dense. Dense in the sense it is accurate, complete, and correct; not in the "idiot" sense. It is, however, the R-rated version; I'll try to stick to the PG or PG-13 version.

## To the Code
Before parsing that and going into a bit of detail on the "Why?"/"When?" of closures I'm going to get to the code. I'll be using FizzBuzz for the first example--in many other places I've seen simple "adders" used for example closures and as far as examples go they seem limited.

So the basic fizzbuzz I'll use is:

{% highlight python %}
def fizzbuzz(x):
    return "Fizz" * (x % 3 == 0) + "Buzz" * (x % 5 == 0) or x

print([fizzbuzz(x) for x in range(0,20)])
{% endhighlight %}

If you're wondering how that works the two expressions `(x % 3 == 0)` and `(x % 5 == 0)` return either **True** or **False**. **True** evaluates to 1 and **False** to zero. So when x is 6 you end up with `"Fizz" * 1 + "Buzz" * 0 or x` then `"Fizz" or x`. In Python logic the following (plus some others) evaluate to False: 

* 0 (zero)
* None
* False
* any of "", \(\), \{\}, or \[\]. 
 
[See: Notes about Booleans](http://www.thomas-cokelaer.info/tutorials/python/boolean.html) In `"Fizz" or x` "Fizz" is taken as true so "x" is ignored. 
For something like x = 7 you end up with `"" or x`--which is `False or x` resulting in "x". 

That, however, is not a closure--it is simply the function we'll be returning from our closure. The actual closure wraps around the function like . . .

{% gist ec273e2c8a4e8f9d3ef47a5272e0ad01 fbclosure.py %}

With that code you can then run stuff like:

{% highlight python %}

fb35 = fbclosure()
fb27 = fbclosure(2, 7)
print([fbclosure()(x) for x in range(0,20)])
print([fb35(x) for x in range(0,20)])
print([fb27(x) for x in range(0,20)])

print(fb35(224))
print(fb27(224))
{% endhighlight %}

In this case the closure itself is created on **lines 1 and 5**: `def fbclosure` and `return fizzbuzz`. I've also added the fizz and buzz parameters defaulting to 3 and 5 to make our fizzbuzz a little more versatile. Going back to the Wikipedia definition--the fizz and buzz variables are "lexically scoped names". Our "function" is fizzbuzz, our environment includes 'fizz' and 'buzz'--they are the "captured" items.

At this point the Wikipedia definition is starting to take form. Our closure is returning a function, fizzbuzz--although we rename it with lines like `fb35 = fbclosure()` or `fb27 = fbclosure(2, 7)`. Our renamed functions store the information/environment of the enclosing closure ("lexically scope"). For fb35() that is `fizz = 3` and `buzz = 5`; for fb27() we have `fizz = 2` and `buzz = 7`. 

As far as using it--you can call the closure directly as in the first print statement (`fbclosure()(x)`), but that probably isn't typical. The real magic and power of functional languages is assigning the enclosed function to a variable which is actually a function. 

## So, why Closures?
For the most part closures provide two things . . .

1. Act as mini-classes/objects 
2. Conveniently hide data (i.e. provides a "private" scope)

On Item 1--a closure provides some sort of functionality and each instance of a closure maintains its own state. From wikipedia this is the "lecixally scoped binding". 

Item 2 builds on Item 1. Java Script and Python are pretty liberal with their scoping. In either language you can trample on or redefine a lot of things--either on purpose or by mistake. The more object oriented/class based languages tend to be defensive (private-ish) by default--functional languages are quite a bit more liberal.

That said, though, while the defensive programming aspect can be important the real power of closures is in Item 1. In languages with functions as first class objects encapsulating and reusing important functions saves a lot of work, lines of code, and provides consistency. For a smaller application you avoid repeating code with functions. When an application grows you will typically start collecting code into modules or classes. 

Closures provide something of a middle ground. Rather than having to continually call a function with a set of parameters or instantiate a class--common bits of code can be centralized so they are easily re-used throughout an application or by multiple developers.

## Another Example: Multiple Functions
This may be quite a bit less common, but a closure in python **can** return multiple functions. Fizzbuzz isn't a particularly good example for this, but something like simple geometry does fit the bill. Iconoclast that I am I'm going to use triangles for this example rather than the more typical points and circles that seem to be the rage.

{% gist ec273e2c8a4e8f9d3ef47a5272e0ad01 triangle_b.py %}

In this case `def triangle(a, b, c)` is our enclosure. Then it gets a little funky. At the bottome **Lines 18-24** we define an empty function `T()` then assign two of our functions to it `T.area = area` and `T.height = height`. We then complete our closure by simply returning `T`.

If you come from an object oriented background this is very much like simply creating a minimalistic "object". `t345 = triangle(3, 4, 5)` will create an object with two functions, area() and height(). Our third function, semiperimeter(), remains hidden, essentially a private function used only internally.

**NOTE:** This code will only work in Python 3--Python 2 does not have the "nonlocal" feature.

Okay, back to our functions. For the most part the 3 functions are pretty straightforward and standard. `semiperimeter()` is only used internally by `area()`. Both `area()` and `height()` are exposed to end-users with our closure. Should probably note that `height()` calls `area()` which also calls `semiperimeter()`. So, anyway, you can run code like . . .

{% highlight python %}
tri = triangle(2,4,5)
tri.height()  # ((2, 3.799671038392666), (4, 1.899835519196333), (5, 1.5198684153570663))
tri.area()    # 3.799671038392666
{% endhighlight %}

But then there is that **nonlocal** stuff in `area()`. According to area's signature/definition (`def area(A=a, B=b, C=c):`) we can call it and replace any combination of our closures sides to get an area using the passed in values. So `tri.area(3)`, replaceing "a = 2" with "a = 3" will return 6.0. The first thing to note is that if nothing is passed in (`tri.area()`) it will simply use the a, b, and c values from the closure. So what does **nonlocal** do? If you comment out the nonlocal related lines (**7 and 8**) then run . .. 

```
tri = triangle(2,4,5)
tri.area(3)
```

. . . it seems to run fine. But tri.area(3) returns "3.2113081446662823" when the area of a 3, 4, 5 triangle should be 6.0!!! What?!? The problem here is that `area()` relies on `semiperimeter()`. Without **nonlocal** our area fucntion is using a = 3, b = 4, and c = 5, but semiperimeter is still using a = 2 from the original closure. So **line 7** tells area that we need to (possibly) change the enclosure's copies of a, b, and c; **line 8** then updates those variables temporarily so that our call to semiperimeter will use a = 2. So, with the nonlocal lines in place, 'tri.area(3)' will correctly return 6.0.

The `height()` function however is never affected by the nonlocal changes. A call like `tri.area(3)` is only a temporary change, once it returns its results the original enclosure keeps "a = 2".

You may be saying "What's the difference between `nonlocal` and `global`?". Good question. In this case our a, b, and c are not global variables--they are enclosed within our closure. `nonlocal` looks for variables in the immediate enclosing scope, but **will not** go as far as global scope. (Essentially if the immediate enclosing scope is global, nonlocal will not go there.)

If you're totally new to Python then A) you probably shouldn't be reading my mad and meandering ravings, but more importantly B) you should probably really get accustomed to going to PEPs for the inside-skinny on Python language feature. A PEP (Python Enhancement Proposal) is essentially a concise clear design document for a Python language feature or change. They are generally very well written and extremely clear on 1) what the problem/issue is, 2) how the issue relates to the over all Python language universe, 3) a break down of what the proposed solutions to the issue are and what the advantages/disadvantages of a solution entail, and finally 4) what the proposed solution is/entails. Anyway, for nonlocal [PEP 3104](https://www.python.org/dev/peps/pep-3104/) is a great read not only for nonlocal but also as a pretty accessible primer on scoping issues for Python and how they compare/relate to other languages.

Now, back to triangle()--`height()` could be altered in a similar way as `area()` so you could "fudge" side lengths to find some required height.

In hindsight this may have been a crud example and I should've stuck with circles and points.

## Where to go from here
Please do not use closures as a hammer and everything is a nail. (The triangle example is a good example.) But some ideas to play around with . . .
1. Implement a data structure like a linked list with your nodes as closures
2. Do some geometry crud with points as closures
3. Populate a list with different versions of the same enclosure and do something with it. 
4. Implement a deck of cards as a closure with shuffle() resetting and randomizing the deck and getcard() returning the next card. Potentially allow your closure to use multiple decks (e.g. a 6 deck shoe).
5. Go outside and hit some golf balls or whatever you do to take your mind off of things. Quite often the best way to learn is to not learn. (Seriously, you'll do your best thinking when you aren't thinking.)

Like any other aspect of learning the idioms and intricacies of a specific language becoming familiar with them, playing with them, and being able to read/write them is the actual "hard" part of learning a language. You will come across closures in other people's code and learning when to use them or go a different direction is an important step in writing good code. Python, by design, lets you do quite a bit with a few lines of code. It is very often referred to as a "multi-paradigm" language--you can accomplish the same goals writing objected-oriented classes or in a more purely functional (or even imperative) manner. And that is the beauty of Python--once you've added closures, how to make your objects iterable, or how to put a set of functions into a module you'll begin to see how all of these tools fit into a work belt and start to design software rather than write lines of code. 

After that soapbox-ish last paragraph I'm going to admit that I'm still much more of an "end result" programmer than a "design" programmer. I'll write lines of code and functions to solve a problem. I'll look back at my creation and say "This is goo...d?--this should probably be an object in a class, this should probably be a separate set of functions in a module, can I drop this function with a simple list comprehension?, this is not my beautiful house . . ." 

## Links and Inspirations
I in no way endorse, collude with, or vouch for the moral integrity of any of the writers for the links below. But I found at least parts of these pages interesting and useful. Googling is 80% of the battle. Somewhat JS Module heavy but closures seem to be almost endemic in JS and python/python closures are probably a gateway drug to JS closures.

1. [Python Fundamentals](https://www.protechtraining.com/bookshelf/python_fundamentals_tutorial/functional_programming?ncr=1)
2. [Python Closure--Multiple Functions](https://stackoverflow.com/questions/19287485/using-a-closure-with-multiple-functions-without-making-variable-global-in-python)
3. [Python--Programiz](https://www.programiz.com/python-programming/closure)
4. [Wikipedia](https://en.wikipedia.org/wiki/Closure_(computer_programming))
5. [Wikipedia--simple](https://simple.wikipedia.org/wiki/Closure_(computer_science))
6. [JS Module Pattern](https://toddmotto.com/mastering-the-module-pattern/)
7. [JS Module Pattern II](https://yuiblog.com/blog/2007/06/12/module-pattern/)
8. [JS Module Pattern III](http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html)
9. [JS What is a Closure](https://www.joezimjs.com/javascript/javascript-closures-and-the-module-pattern/)

 
