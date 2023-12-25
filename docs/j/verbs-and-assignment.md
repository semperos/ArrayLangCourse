# Verb, Direct Definition, and Assignment

## Verbs and Direct Definition
In J, normal functions—those that take noun arguments and produce noun results—are called <dfn>verbs</dfn>.

A <dfn>direct definition</dfn> is a way of writing verbs in J. It starts and ends with double curly braces `{{}}`, has a right argument `y` and an optional left argument `x`.

```J
   3{{x}}5     NB. x is the (optional) left argument
3
   {{y}}'apl'  NB. y is the right argument
apl
   {{x}}5      NB. Calling a dyadic verb monadically results in an error
|domain error
|       {{x}}5
|[-0] 
   3{{y}}      NB. Calling a verb without a right argument results in an error
|syntax error
|       3{{y}}
|[-0] 
```

From here, when verbs are first introduced, `v y` denotes a monadic verb `v` and `x F y` denotes a dyadic verb.

## Assignment
Primitives are the words defined by the system, but you can create your own names using the primitive `=.` called <dfn>copula</dfn>.

```J
   one=.1
   three=.3
   equals=.=
   plus=.+
   four=.4
   four equals one plus three   NB. 1 means true, 0 means false
```
```
1
```

We can use a name in the same line in which it is defined. In production code it is best to avoid this unless an expression is very short.

Read the following as "squared numbers divided by the sum of squares":
```APL
   squared%+/squared=.*: _1 0 1 2
```
```
0.1666666667 0 0.1666666667 0.6666666667`
```

Unlike APL and BQN, in J you are free to redefine names with new values of any type.

## Multiline functions and the editor
You can do quite a lot in a single line of J. However, it is not long before you want to keep sequences of multiple statements available for re-use. Of course we can write verbs which consist of multiple statements.

You can separate each statement by a newline. Lines are evaluated top to bottom.

The <dfn>left</dfn> verb `[` allows us to write multiple J statements in a single line, but unlike APL and BQN's diamond separator, evaluation occurs right-to-left.

The following two definitions of the `mean` verb are equivalent.

```APL
 mean =. {{
   sum =. +/ y
   count =. # y
   sum % count
 }}

 mean =. {{ sum % count [ count =. # y [ sum =. +/ y }}
```

In the J Playground and the JQT tool, you will need to use an editor to write multi-line definitions like this, rather than the interactive prompt.

## Problem set 2
The following problems can be solved with single-line dfns.

1. Eggs

	A recipe serving 4 people uses 3 eggs. Write the function `eggs` which computes the number of eggs which need cracking to serve `y` people. Using a fraction of an egg requires that a whole egg be cracked.

	```J
	   eggs 4
	```
	```
	3
	```
	---
	```J
	   eggs 100
	```
	```
	75
	```
	---
	```J
	   Eggs ⍳12
	```
	```
	1 2 3 3 4 5 6 6 7 8 9 9
	```

	???Example "Answer"
		```J
		eggs =. {{>.y*3%4}}
		```

1. Write a function `to` which returns integers from `x` to `y` inclusive.

	```J
	   3 to 3
	3
	   3 to 4
	3 4
	   1 to 7
	1 2 3 4 5 6 7
	   _3 to 5
	_3 _2 _1 0 1 2 3 4 5
	```

	**BONUS:** What if `x>y`?  
	```J
	   3 to 5
	3 4 5
	   5 to 3
	5 4 3
	   5 to _2
	5 4 3 2 1 0 _1 _2
	```

	???Example "Answer"
		In the simple case, make sure to generate enough numbers and use `⍺` as an offset:  
		```J
		to =. {{x+i.1+y-x}}
		```
		In general we take into account whether the difference is positive or negative:  
		```J
		to =. {{x+(*d)*i.1+|[d=.y-x}}
		```

1. The formula to convert temperature from Celsius ($T_C$) to Fahrenheit ($T_F$) in traditional mathematical notation is as follows:

	$$T_F = {32 + {{9}\over{5}}\times {T_C}}$$  

	Write the function `CtoF` to convert temperatures from Celcius to Farenheit.  
	```J
	   CtoF 11.3 23 0 16 _10 38
	52.34 73.4 32 60.8 14 100.4
	```

	???Example "Answer"
		```J
		CtoF =. {{32+y*9%5}}
		```

1. Prime Time

	A prime number is a positive whole number greater than $1$ which can be divided only by itself and $1$ with no remainder.

	Write a verb which returns `1` if its argument is prime and `0` otherwise.

		       isPrime 21
	    0
		       isPrime 17
	    1

	???Example "Answer"
		There are several ways to code this, but the basic method is to count the number of divisors.
		```J
		isPrime =. {{2=+/d=<.d=.y%1+i.y}}
		isPrime =. {{2=+/0=(1+i.y)|y}}
		```