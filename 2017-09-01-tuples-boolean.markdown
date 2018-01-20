---
layout: single
title: "Take the Named Tuples Boolean"
date:   2017-08-01
categories: python namedtuple boolean_algebra logic
tags: python namedtuple boolean_algebra logic
---
# Canonical Normal Forms and Boolean Minimization
Quite a while ago I read parts of Randy Hyde's **The Art of Assembly Language Programming (AoA)**. The section on boolean logic and Canonical Normal forms intrigued me for some reason. The whole book is available online and the specific section is [3.4 Canonical Forms](http://www.plantation-productions.com/Webster/www.artofasm.com/Linux/HTML/DigitalDesign2.html). 

More recently I've been messing around with functions that take numbers and output "something"--things like `fizzbuzz(6)` or `isprime(17)`. Canonical Normal Forms came to mind and that also led to Quinn-McCluskey reduction (more on Q-M later). So I wrote something up in Python.

In case you're wondering--the title is meant to be a reference to Camper Van Beethoven 1985 smash hit "Take the Skinheads Bowling". And I used a named tuple for doing quite a bit of the work in [cdnf.py](https://github.com/jmbriody/bookofnumbers/blob/master/bookofnumbers/cdnf.py).

# Canonical Normal Forms explained
So the basic gist of CNFs is that an integer can be used to specify a specific boolean expression in normal form. When viewed as a binary number the integer acts as the "results" in a truth table. So the CNF of 228 is "ABC + A'BC + AB'C + A'BC'". In binary 228 is 11100100--so the number of terms in the CNF corresponds to the number of 1's in the binary representation. The individual terms are derived from the "columns" of the truth table.

So for our example of 228 (where BP is bitposition) . . .

    BP  |  C  |  B  |  A  | 228 | Term
    --------------------------------------
    1   |  0  |  0  |  0  |  0  |
    2   |  0  |  0  |  1  |  0  |
    3   |  0  |  1  |  0  |  1  | A'BC'
    4   |  0  |  1  |  1  |  0  |
    5   |  1  |  0  |  0  |  0  |
    6   |  1  |  0  |  1  |  1  | AB'C
    7   |  1  |  1  |  0  |  1  | A'BC
    8   |  1  |  1  |  1  |  1  | ABC

. . . every row where 228 has a 1 is used to create a term. The term itself is created from the A, B, and C columns. If the letter is a zero it is "primed"--meaning NOT B. If the letter is a one it is un-primed. Each letter in a term is AND'd with the other letters. So A'BC' means (NOT A) AND (B) AND (NOT C). In the final form each term is OR'd with the others (using + for OR). So the final version is . . .

A'BC' OR AB'C OR A'BC OR ABC

Note: I'm using A as the lower order bit, alternating 0101. Others, such as [Logic Circuit Simplification](http://www.32x8.com/) do the reverse where the last letter (in this case C) is the low order bit. I have a "reverse" flag in the code that will use that version of ordering just to make it easier to compare stuff.

