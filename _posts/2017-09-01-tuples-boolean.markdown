---
layout: single
title: "CDNF Part I: Canonical Fodder"
date:   2017-08-01
categories: python namedtuple boolean_algebra logic
tags: python namedtuple boolean_algebra logic I
---
# Canonical Normal Forms and Minimization
Quite a while ago I read parts of Randy Hyde's **The Art of Assembly Language Programming (AoA)**. The section on boolean logic and Canonical Normal forms intrigued me for some reason. The whole book is available online and the specific section is [3.4 Canonical Forms](http://www.plantation-productions.com/Webster/www.artofasm.com/Linux/PDFs/DigitalDesign.pdf). 

More recently I've been messing around with functions that take numbers and output "something"--things like `fizzbuzz(6)` or `isprime(17)`. Canonical Normal Forms came to mind and that also led to Quine-McCluskey reduction (more on Q-M later). So I wrote something up in Python.

In case you're wondering--the title is meant to be a reference to Camper Van Beethoven 1985 smash hit "Take the Skinheads Bowling". And I used a named tuple for doing quite a bit of the work in [cdnf.py](https://github.com/jmbriody/bookofnumbers/blob/master/bookofnumbers/cdnf.py).

## Canonical (Disjunctive) Normal Forms explained
So the basic gist of CDNFs is that for any set of inputs (e.g. A, B, and C) all the logical functions of those inputs can be written with "minterms" that include all 3 inputs. For example "A'BC + AB'C" and "A'BC + AB'C + A'BC' + A'B'C'" would both be CDNFs with 3 inputs. Each minterm's input items are ANDed (so ABC is "A AND B AND C"). And the individual minterms are ORd (using "+"). And finally ' is used to represent negation, so B' is "NOT B". The "OR" portion is where the Disjunctive part comes from; for Conjunctive Normal Forms each input is ORd within a minterm and each minterm is ANDd--but I won't be dealing with Conjunctive Normal Forms here.

These normal forms can be ordered based on binary numbers (or converting ints to binary). For CDNF each "1" bit in the binary representation "points" to a minterm. For example the CDNF of 228 is "ABC + ABC' + AB'C + A'BC'". In binary 228 is 11100100--so the number of terms in the CDNF corresponds to the number of 1's in the binary representation. The individual terms are derived from the columns of a truth table.

So for our example of 228 (where BP is bitposition) . . .

    BP  |  A  |  B  |  C  | 228 | Term
    --------------------------------------
    0   |  0  |  0  |  0  |  0  |
    1   |  0  |  0  |  1  |  0  |
    2   |  0  |  1  |  0  |  1  | A'BC' (010)
    3   |  0  |  1  |  1  |  0  |
    4   |  1  |  0  |  0  |  0  |
    5   |  1  |  0  |  1  |  1  | AB'C  (101)
    6   |  1  |  1  |  0  |  1  | ABC'  (110)
    7   |  1  |  1  |  1  |  1  | ABC   (111)

. . . every row where 228 has a 1 is used to create a term. The term itself is created from the A, B, and C columns. If the letter is a zero it is "primed"--meaning NOT B. If the letter is a one it is un-primed. 

For the canonical() function it is important to note that the Bit Position (BP) corresponds to the binary version of the BP. So BP 5 == 101 which is a minterm of AB'C. BP 0 = 000 -- A'B'C'. 

Note: Art of Assembly uses "A" as the low order bit--so it is the rightmost letter going 010101.  Others, such as [Logic Circuit Simplification](http://www.32x8.com/) do the reverse where the last letter (in this case C) is the low order bit. For my canonical() A is the high order bit as in the table above--for 3 inputs it is 00001111 and C is 01010101. The second argument to canonical() can be used to switch this so A would be 01010101 and C 00001111. 

## The Canonical() code
The code itself is actually pretty minimal. There may be a bit of hinky razza-ma-taz going on but it is probably more concise than the explanation above.

{% gist 28dc5d1e3e8c6016e969185836baf0cf %}

The basic definition is `canonical(x, highorder_a=True, includef=False)` . . .

* x -- is our int to convert
* highorder_a -- lets us switch between A being the HO and LO bit
* includef -- if True simply adds "F(x) =" in front of the result

Walking through the code using `canonical(228)` gives us . . .

**Lines 5 and 6** Convert the int to binary and reverse the order--essentially for a binary number the right-most bit is our BP 0, next is BP 1, etc. So we need to reverse it. For 228--Binary is 11100100, reversed is '00100111'.

**Lines 8 to 10** Next we determine how many inputs/letters we need. This part may take a bit of thinking, but essentially the length of the binary version of our input will determine the number of inputs/letters we will need. So for 228 the binary representation is 8 digits. So its length can be represented by a 3 digit binary. This corresponds to the number of columns (A, B, C) needed. We stick with a minimum of 2 letters mostly so the 1st 15 integers provide standard results.

**Line 12** Here we are just making a list of the binary represenations
of the Bit Positions where a 1 appears. So for 228 we have BPs (using a zero indexed array) at 2, 5, 6, 7. (That is the `i in re.finditer('1', binary)` of the list comprehension.) And this is why we reversed the bits on line 6. `format(i.start(),  '0' + str(letters) + 'b' )` converts 2, 5, 6, and 7 to a binary format with a zero fill based on the number of letters. For 228 with BPs of 2, 5, 6, 7 we end up with ['010', '101', '110', '111']--which are the binary versions of our minterms.

**Line 13** We now take our list from line 12 and feed each item into the _minterm_() function. 

**\_minterm\_()**
**Line 21** Simply creating a list of all the letters. Very much overkill as A-Za-z would allow more inputs/permutations than the universe can contain.

**Line 23 and 24** If we want A as the low order bit we reverse the digits for each of our minterms.

**Line 26 to 29** Finally, we simply loop over the digits in our term. For each digit we add the next letter--then if the digit is a zero we add ' after the letter. So 111 becomes ABC and 110 becomes ABC'.

**Line 14** Back in canonical we convert the new list of minterms to a string with " + " between each item.

# From Canonical to a Minimized version
So what's the big deal about a canonical expression? Essentially it represents all of the "terms" from a truth table. From the canonical version it is possible to minimize the expression to make things simpler. This means both electronics and code and other applications don't have to deal with typically large numbers of circuits or if/else statements.

And now for an example . . .

## Let's go Bowling!
So, Alan likes to bowl. But he never bowls alone. He has three friends that he bowls with: Bob, Clara, and Declan. For the examples I'll use 4 distinct situations . . .

**Situation 1** The base case of Alan never bowling alone.

**Situation 2** Same as 1 but Bob owes Clara money so Bob won't bowl if Clara is bowling

**Situation 3** Same as 2 (Bob still owes Clara) but Bob will bowl with Clara if Declan is also bowling because Declan is a really funny guy.

**Situation 4** Bob has payed Clara--but Alan will only bowl if there is an even number (2 or 4) bowlers because the bowling alley charges a higher rate for 3 bowlers.

All 4 of these situations are summarized in the table below. Columns A, B, C, and D specify whether someone is available/willing to bowl. The columns 1, 2, 3, and 4 are the 4 situations a 1 means they go bowling--zero means they do not go bowling.

    ABCD  1 2 3 4  Row #
    ----  -------  ------
    0000  0 0 0 0   0
    0001  0 0 0 0   1
    0010  0 0 0 0   2
    0011  0 0 0 0   3
    0100  0 0 0 0   4
    0101  0 0 0 0   5
    0110  0 0 0 0   6
    0111  0 0 0 0   7
    1000  0 0 0 0   8
    1001  1 1 1 1   9 x
    1010  1 1 1 1  10 x
    1011  1 1 1 0  11 y
    1100  1 1 1 1  12 x
    1101  1 1 1 0  13 y
    1110  1 0 0 0  14 z
    1111  1 0 1 1  15 y2

* Rows 0-7: Alan can't bowl so everything is zero
* Row 8: Alan can bowl but none of his friends can (zeros). A=1, B,C,D=0
* Rows 9, 10, 12 (x): Alan can bowl and exactly 1 friend can so all are true
* Row 11 and 13 (y): S 1, 2, and 3 are true because either Clara or Bob can't bowl. S4 is false because it is only 3 bowlers.
* Row 14 (z): S 2 and 3 are false because Bob and Clara won't bowl together. And S4 is False again because of 3 bowlers
* Row 16 (y2): Only false for S2 where Bob absolutely will not bowl with Clara.

## Canonical to minimized forms for Bowling
**Situation 1 -- 65024**
There are 7 "True" instances so we'll have 7 minterms. Starting with row 15 the minterm would be ABCD, next would be ABCD', etc. We could manually translate these terms--but we can also just use `int('1111111000000000', 2)` to get 65024 and feed it to canonical(). To get . . .

ABCD + ABCD' + ABC'D + ABC'D' + AB'CD + AB'CD' + AB'C'D

. . . which represents all cases where Alan will bowl. Using some premises of logical operations/reductions this can be reduced to . . .

AB + AC + AD

This probably seems obvious from the description of Situation 1. If we know Alan will only bowl if at least one of his friends is we aren't going to come up with all the permutations of the canonical form--we'd simply say Alan will bowl if Bob does (Clara and/or Declan can bowl as well); if Bob isn't bowling but Clara is Alan will bowl (Declan may or may not); if neither Bob or Clara are bowling but Declan is Alan will bowl.

**Situation 2 -- 15872**
Next up we have the situation where Bob and Clara will not bowl together `int('0011111000000000', 2)` or 15872. The canonical form is . . .

ABC'D + ABC'D' + AB'CD + AB'CD' + AB'C'D

Note the 1st 4 terms. In the 1st 2 Clara isn't bowling (C') so Bob can. Terms 3 and 4 Bob isn't bowling (B') so Clara can. Fifth term Bob and Clara are probably together having angry sex while Alan and Declan are bowling [more on Bob and Clara later]. The minimized form is:

ABC' + AB'C + AC'D

**Situation 3 -- 48640**
Bob and Clara will only bowl together if Declan is bowling. . . .

ABCD + ABC'D + ABC'D' + AB'CD + AB'CD' + AB'C'D

This is where things may start to be counter-intuitive. 2nd Term has Declan bowling so both Bob and Clara "can" bowl--but Clara isn't. This simply means Clara wasn't going to bowl regardless and it needs to be represented. The minimized version really clears this up . . .

ABC' + AB'C + AD

The final term "AD" essentially allows Bob and Clara to bowl together at that point. The first 2 terms would allow Bob or Clara to bowl with Alan alone or Alan and Declan. Note the first 2 terms here are the same as Situation 2, but the final term removes the C' restriction.

**Situation 4 -- 38400**
This one is a bit funky. We need 2 or 4 bowlers. (Bob and Clara have settled up.) The canonical form is:

ABCD + ABC'D' + AB'CD' + AB'C'D

and the minmized form is:

ABCD + ABC'D' + AB'CD' + AB'C'D

So this one can not be minimized at all. Essentially either all 4 play (ABCD) or two of them absolutely can not play.

### So What's Up with Bob and Clara
In a strange twist of the universe all of these inputs and variables we so non-chalantly throw around actually lead very full lives. Bob and Clara seem to constantly argue and get on each others nerves--but they have been carrying on a rather secret relationship. Declan is almost constantly commenting that they should "get a room"; but Alan has had a thing for Clara for quite a while. Bob and Clara essentially came up with a plan that if they can get Alan and Declan to bowl together more often they'd be tied up at the bowling alley and Bob and Clara could safely have their sordid rendevous. 

Clara has been trying to get her friend Erica to take up bowling in the hopes of distracting Alan. But that all gets a little too complex for what is meant to be a relatively simple example. But seriously--why am I explaining this to you. Can't you just let Bob and Clara live their lives? Really, the next time you just non-chalantly throw around a B' or D' just remember Bob may be out having a great time so he is not available, but he could also be sitting at home playing REM's "Everybody Hurts" on a constant loop while watching the Powerpuff Girls on netflix just waiting for his phone to ring.

Oops. Sorry, where was I. Oh . . . Heading into Quine-McCluskey. Which I'll take up in Part II since this is getting a bit long.

