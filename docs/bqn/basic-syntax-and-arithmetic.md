# Basic syntax

## Functions and arguments
BQN has two-argument, infix functions. These are called <dfn>dyadic</dfn> functions.

```BQN
      3 × 5
```
```
15
```
---
```BQN
      3 - 5
```
```
¯2
```

Some functions map between elements of their left and right argument arrays. It is easy to add lists of numbers together:
```BQN
      1‿2‿3 + 4‿5‿6
```
```
⟨ 5 7 9 ⟩
```

Negative numbers are written with a <dfn>high minus</dfn> `¯` to differentiate between negation (`-3`) and literal negative numbers (`¯3`).
```BQN
      1‿2‿3 - 1‿0‿¯1
```
```
0 2 4
```

There are also one-argument, prefix functions. These are called 1-argument or <dfn>monadic</dfn> functions.
```BQN
      - 5‿¯3‿0‿¯4‿2
```
```
⟨ ¯5 3 0 4 ¯2 ⟩
```
---
```BQN
      ⌽ 1‿2‿3‿4‿5
```
```
⟨ 5 4 3 2 1 ⟩
```

Some symbols represent both a 1-argument and a 2-argument function, but these are often closely related. As we will see later, even user-defined functions can be monadic, dyadic or even both (<dfn>ambivalent</dfn>).

**:bulb: Try this**: Use these functions monadically and dyadically:

<div class="center language-BQN" markdown="span">
<div class="displayBox" markdown="span">
<a class="glyph" title="Conjugate/Add">`+`</a>
<a class="glyph" title="Negate/Subtract">`-`</a>
<a class="glyph" title="Sign/Multiply">`×`</a>
<a class="glyph" title="Reciprocal/Divide">`÷`</a>
<a class="glyph" title="Absolute Value/Modulus">`|`</a>
<a class="glyph" title="Exponential/Power">`*`</a>
<a class="glyph" title="Square Root/Root">`√`</a>
<a class="glyph" title="Ceiling/Maximum">`⌈`</a>
<a class="glyph" title="Floor/Minimum">`⌊`</a>
</div>
</div>

## Singleton extension
Dyadic functions can map between a single value and an array of values.
```BQN
      3 × 1‿10‿100
```
```
⟨ 3 30 300 ⟩
```
---
```BQN
      3 = 1‿2‿3‿4‿5
```
```
⟨ 0 0 1 0 0 ⟩
```

**:bulb: Try this**: Replace the functions in the previous two expressions with:
<a class="glyph" title="Maximum">`⌈`</a>
<a class="glyph" title="Minimum">`⌊`</a>
<a class="glyph" title="Less Than">`<`</a>

While experimenting, you may cause an error:

```BQN
      1‿2 + 3‿4‿5
```
```
Mapping: Equal-rank argument shapes don't agree

1‿2 + 3‿4‿5
    ^
```

Functions such as `+ × ⌈` apply between elements of two arrays of the same shape, or between one element and many if one of the arguments is a single value. However, if the arrays are of two different shapes, it is not clear how the function should be applied. Of course, you may want to [apply a function between all combinations of elements of the left and right argument](./array-logic-data-driven-conditionals.md#the-outer-product), but that will be addressed soon enough.

## Order of execution
Expressions are executed from right to left.

```BQN
      10×↕2+5
```
```
⟨ 0 10 20 30 40 50 60 ⟩
```

??? Info "Show me step-by-step"
	To start, there is a literal number 5:
	```BQN
	            5
	5
	```
	
	Next, there is a plus `+` with a number 2 to its immediate left, so it is evaluated as two plus five:
	```BQN
	          2+5
	7
	```
	
	Then the symbol <dfn>Range</dfn> `↕`. To its left is another function, multiply `×`, not a value. So the function is called <em>monadically</em>. The monadic form of `↕` is the <dfn>index generator</dfn>, which generates an integer array of length defined by its right argument, where each element is also its own index:
	```
	         ↕2+5
	⟨ 0 1 2 3 4 5 6 ⟩
	```
	
	Lastly, another dyadic function, we multiply our list by ten:
	```
	         10×↕2+5
    ⟨ 0 10 20 30 40 50 60 ⟩
	```

The expression above is "ten *times* the indices from 0 to *two plus five*, or in short: "ten times range two plus five". We can make it clearer using (superfluous) **parentheses** `()`.
```BQN
      10×(↕(2+5))
```
```
⟨ 0 10 20 30 40 50 60 ⟩
```

Of course, we can change the order of execution using different parentheses.

```BQN
      (10×↕2)+5
```
```
⟨ 5 15 ⟩
```

??? Info "Show me step-by-step"
	Beginning from the right, there is a literal number 5:
	```BQN
	      (10×↕2)+5
	              5
	```
	
	Then there is a plus symbol `+`. Before we can decide if it is being called monadically or dyadically, we must look to the left.
	
	```BQN
	            )+5
	```
	
	A right parenthesis. We must evaluate the contents of the parentheses to see if it is a function or a value.
	
	```BQN
	      (10×↕2)
	```
	
	This expression evaluates to the list `⟨ 0 10 ⟩`. Since it is a value, it is used as the left argument to our add function.
	
	```BQN
	      (10×↕2)+5
	      ⟨0, 10⟩+5
	⟨ 5 15 ⟩
	```

Infix (dyadic) functions have a **short** *left* scope and **long** *right* scope. This means that they take the result of everything to their right hand side as their right argument. 

If there is one, the left argument is the value to the <em>immediate</em> left.

BQN, unlike APL, does not perform stranding, as you may have surmised from the use of the `‿` character, which is called <dfn>strand</dfn>.

This prevents the confusion called out in the APL lesson:

```BQN
      2 + 2 2 + 2
```

Gives the following error:

```
Double subjects (missing ‿?)

      2 + 2 2 + 2
            ^
```

Using the explicit stranding, we get the same result as APL would for the above expression:

```BQN
      2 + 2‿2 + 2
⟨ 6 6 ⟩
```

??? Info "Show me step-by-step"
	First, there is a literal number 2
	```BQN
	                2
	2
	```
	
	Then there is a symbol `+`. What, if any, is the value to its immediate left?
	```BQN
	          2‿2 + 2
	```
	
	It is a 2-element vector `2‿2`. The add function maps between these elements and the single number on the right:
	```BQN
	          2‿2 + 2
	⟨ 4 4 ⟩
	```
	
	Finally there is another addition. The overall evaluation looks like the following:
	```BQN
	      2 + 2‿2 + 2
	      2 + 4‿4
	⟨ 6 6 ⟩
	```

## Comments
Anything after a hash symbol `#` is ignored.

```BQN
      # nothing happens on this line
      2 × 3 # 4 5
```
```
6
```
---
```BQN
	  'A'   # hash is hard to mistake for another letter
```
```
A
```

## The reduction operator
Adding a list of numbers *could* become very tedious...
```BQN
      1+2+3+4+5+6+7+8+9+10+11+12+13+14+15
```
```
120
```

We can phrase this list of integers `1` through `15` more succinctly:

```BQN
      1+↕15
⟨ 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 ⟩
```

The [**fold** operator](https://mlochbaum.github.io/BQN/doc/fold.html./Operators.md) `𝔽´` inserts the function `𝔽` to its left between parts of the right argument array.
```BQN
      +´1+↕15
```
```
120
```

A fold is sometimes called *reduce* because it reduces the number of dimensions of its argument. In the example above, we have a **vector** (1 dimensional, list) argument and return a **scalar** (0 dimensional, single value) result.

## Range: The index generator
The Range function `↕𝕩` generates integers from `0` up to the integer right argument `𝕩`
```BQN
      ↕10
```
```
⟨ 0 1 2 3 4 5 6 7 8 9 ⟩
```

**Note:** APL's `⍳` starts at `1`, while BQN's `↕` starts at 0.

So we can do an arithmetic sum as follows:

|**Traditional Mathematical Notation (TMN)** | **BQN** |
|---|---|
| $\sum_{n=1}^N n$ | `+´↕N`

## What do these errors mean?
While experimenting, you are very likely to come across these:

```BQN
      ↕¯4
```
```
↕𝕩: 𝕩 must consist of natural numbers

↕¯4
^
```

The error indicates that this function expects natural numbers, which do not include negative integers. Negative numbers are <em>outside the domain</em> of the index generator function. How might you [generate integers from 1 to negative four](./dfns-and-assignment.md#problem-set-2)?

```BQN
      1+
```
```
Second-level parts of a train must be functions

1+
^
```

This error represents a syntactic error. Here, BQN thinks you're trying to string together multiple functions (a _train_ whose cars are functions). We'll study trains and partial function application in later lessons.

```BQN
      a
```
```
Undefined identifier

a
^
```

We have not seen any named functions or variables yet; nothing has been assigned to the name `a`, so trying to use it in an expression is meaningless.

## Problem Set 1
1.   
	The average daily temperatures, in degrees Celcius, for 7 days are stored in a variable `t_allweek`.
	
	```BQN
	t_allweek ← ⟨ 11.7 , 8.6 , 9.7 , 14.2 , 6.7 , 11.8 , 9.2 ⟩
	```
	
	Use BQN to compute the follwing:
	
	1. The highest daily temperature
	1. The lowest daily temperature
	1. The range of (difference between the largest and the smallest) temperatures
	1. Each temperature rounded to the nearest whole number

	??? Example "Answers"
		<ol type="a">
		<li>
		```BQN
		      ⌈´t_allweek
		14.2
		```
		</li>
		<li>
		```BQN
		      ⌊´t_allweek
		6.7
		```
		</li>
		<li>
		```BQN
		      (⌈´t_allweek)-⌊´t_allweek
		7.499999999999999
		```
		
		> You may have found the correct answer using the following expression:
		```BQN
		      ⌈´t_allweek-⌊´t_allweek
		7.5
		```
		
		> but this is less efficient because it does more subtractions than it needs to. Recall the right-to-left evaluation:
		```BQN
		      ⌈´      t_allweek                 - ⌊´ t_allweek
		      ⌈´      t_allweek                 - 6.7
		      ⌈´ 11.7‿8.6‿9.7‿14.2‿6.7‿11.8‿9.2 - 6.7
		      ⌈´ 5‿1.9‿3‿7.5‿0‿5.1‿2.5
		      7.5
		```
		
		> if we use parentheses `()` to force BQN to compute the maximum of the list before doing subtraction, we only do a single subtraction instead of 7:
		```BQN
		      ( ⌈´t_allweek ) - ⌊´ t_allweek
		      ( ⌈´t_allweek ) - 6.7
		      (     14.2    ) - 6.7
		      7.5
		```
		
		</li>
		<li>
		To round to the nearest whole number, either add 0.5 and round down:
		```BQN
		      ⌊0.5+t_allweek
		⟨ 12 9 10 14 7 12 9 ⟩
		```
		
		or subtract 0.5 and round up:
		```BQN
		      ⌈t_allweek-0.5
		⟨ 12 9 10 14 7 12 9 ⟩
		```
		</li>
		</ol>

1. A Mathematical Notation

	Use BQN to evaluate the following

	1. $\prod_{n=1}^{12} n$ (multiply together the first twelve integers)

	1. $\sum_{n=1}^{17}n^2$ (add together the first seventeen squared integers)

	1. $\sum_{n=1}^{100}2n$ (add together the first one hundred positive even integers)

	1. $\sum_{n=1}^{100}2n-1$ (add together the first one hundred odd integers)

	1. In TMN, the following expression is equal to `0`, why does the following return `70` in BQN?
		```BQN
		      84 - 12 - 1 - 13 - 28 - 9 - 6 - 15
		```
		```
		70
		```

*[TMN]: Traditional Mathematical Notation

	??? Example "Answers"
		<ol type="a">
		<li>
		```BQN
		      ×´1+↕12
        479001600
		```
		</li>
		<li>
		```BQN
		      +´(1+↕17)⋆2
        1785
		```
		Without parentheses we get the sum of the first 289 integers, instead of the first 17 integers squared.

		We can use the Explain button in the BQN online REPL to see the difference:

		```BQN
		+´(1+↕17)⋆2	
		|     |   |
		|    ↕17  |
		|  1+┘    |
		|  └-----⋆2
		+´       |
		|--------y
		|
		1785
		```

		Without parentheses:

		```BQN
		+´1+↕17⋆2	
		| |   | |
		| |  17⋆2
		| |    |
		| | ↕--┘
		| | |
		| 1+┘
		|  |
		+´ |
		 |-┘
		 |
		41905
		```

		|TMN|BQN|
		|---|---|
		|$\sum_n^{17^2} n$|`+´↕17⋆2`
		|$\sum_n^{17} n^2$|`+´(↕17)⋆2`|

		</li>
		<li>
		```BQN
		      +´2×1+↕100
        10100
		```
		</li>
		<li>
		We can either subtract 1 from the even numbers:
		```BQN
		      +´(2×1+↕100)-1
		10000
		```

		or we can add negative 1:
		```BQN
		      +´¯1+2×1+↕100
        10000
		```
		The high minus denotes a literal negative, whereas the hyphen indicates subtraction.
		</li>
		<li>
		Remember the right-to-left rule: functions take everything to their right, and the first thing to their left. We can add unnecessary parentheses to show how BQN evaluates our expression.
		```BQN
		      (84 - (12 - (1 - (13 - (28 - (9 - (6 - 15)))))))
	    70
		```
		</li>
		</ol>

1. Pyramid Schemes
	1. Sugar cubes are stacked in an arrangement as shown by **Figure 1**.

		<img src="../../img/SquaredCubes.png" width="200px" alt="Stacked sugar cubes"/>
			<figcaption><strong>Figure 1.</strong> Stacked sugar cubes</figcaption>

		This stack has `4` **layers** and a total of `30` cubes. How many cubes are there in a similar stack with `467` **layers**?

	1. Now consider the stack in **Figure 2**.

		<img src="../../img/OddSquaredCubes.png" width="280px" alt="Differently stacked sugar cubes"/>
			<figcaption><strong>Figure 2.</strong> Differently stacked sugar cubes</figcaption>

		The arrangement in **Figure 2** has `4` **layers** and `84` cubes. How many cubes are there in a similar stack with `812` **layers**?

	1. Now look at **Figure 3**.

		<img src="../../img/CubedCubes.png" width="280px" alt="This is just a waste of sugar cubes by now..."/>
			<figcaption><strong>Figure 3. </strong>This is just a waste of sugar cubes by now...</figcaption>

		The stack in **Figure 3** has `3` **"layers"** and `36` cubes in total. How many cubes are there in a similar stack with `68` **"layers"**?

	???Example "Answers"
		<ol type="a">
		<li>
		Each $n$th layer has $n^2$ cubes. There are $34,058,310$ cubes in a stack with $467$ layers.
		```BQN
			+´(1+↕4)⋆2
		```
		```
		30
		```
		---
		```BQN
			+´(1+↕467)⋆2
		```
		```
		34058310
		```
		</li>
		<li>
		Each $n$th layer has $(2n-1)^2$ cubes. There are $713,849,500$ cubes in a stack with $812$ layers.
		```BQN
			+´(¯1+2×1+↕4)⋆2
		```
		```
		84
		```
		---
		```BQN
			+´(¯1+2×1+↕812)⋆2
		```
		```
		713849500
		```
		</li>
		<li>
		Each $n$th layer has $n^3$ cubes. There are $5,503,716$ cubes in a stack with $68$ layers.
		```BQN
			+´(1+↕3)⋆3
		```
		```
		36
		```
		---
		```BQN
		    +´(1+↕68)⋆3
		```
		```
		5503716
		```
		</li>
		</ol>

1. Rewrite the following expressions so that they do not use parentheses.
	1. `(÷a)×b`
	1. `(÷a)÷b`
	1. `(a+b)-5`
	1. `(a+b)+5`

	???Example "Answers"
		<ol type="a">
		<li>Multiplication is commutative, which means that the order of arguments does not matter, so we can write `b×÷a`. Even more simply, it is `b÷a` because multiplication by a reciprocal is the same as division.</li>
		<li>${{{1}\over{a}}\div{b}} = {{1}\over{a\times{b}}}$ so we can write `÷a×b`</li>
		<li>Use a literal negative five:`¯5+a+b`</li>
		<li>No parentheses needed: `a+b+5`</li>
		</ol>
