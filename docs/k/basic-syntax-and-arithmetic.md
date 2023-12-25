# Basic syntax

## Functions and arguments
K has two-argument, infix functions. These are called <dfn>dyadic</dfn> functions.

```K
 3 * 5
```
```
15
```
---
```K
 3 - 5
```
```
-2
```

Some functions map between elements of their left and right argument arrays. It is easy to add lists of numbers together:
```K
 1 2 3 + 4 5 6
```
```
5 7 9
```

Unlike APL, BQN, and J, K does not have a different symbol for negative numbers and subtraction; both rely on `-`.

```K
 1 2 3 - 1 0 -1
```
```
0 2 4
```

There are also one-argument, prefix functions. These are called <dfn>monadic</dfn> functions.
```K
 - 5 -3 0 -4 2
```
```
-5 3 0 4 -2
```
---
```K
 | 1 2 3 4 5
```
```
5 4 3 2 1
```

Most symbols represent both a monadic and a dyadic function, but these are often closely related. As we will see later, even user-defined functions can be monadic, dyadic, or have up to 8 arguments.

**:bulb: Try this**: Use these functions monadically and dyadically:

<div class="center language-K" markdown="span">
<div class="displayBox" markdown="span">
<a class="glyph" title="flip/add">`+`</a>
<a class="glyph" title="negate/subtract">`-`</a>
<a class="glyph" title="first/multiply">`*`</a>
<a class="glyph" title="sqrt/divide">`%`</a>
<a class="glyph" title="div/mod">`!`</a>
<a class="glyph" title="ascend/less">`<`</a>
<a class="glyph" title="descend/more">`>`</a>
<a class="glyph" title="floor/lowercase">`_`</a>
</div>
</div>

## Singleton extension
Dyadic functions can map between a single value and an array of values.
```K
 3 * 1 10 100
```
```
3 30 300
```
---
```K
 3 = 1 2 3 4 5
```
```
0 0 1 0 0
```

**:bulb: Try this**: Replace the functions in the previous two expressions with:
<a class="glyph" title="more">`>`</a>
<a class="glyph" title="less">`<`</a>
<a class="glyph" title="divide">`%`</a>

While experimenting, you may cause a `'length` error:

```K
 1 2+3 4 5
```
```
'length
    1 2+3 4 5
       ^
```

Functions such as `+ * >` apply between elements of two arrays of the same shape, or between one element and many if one of the arguments is a single value. However, if the arrays are of two different shapes, it is not clear how the function should be applied. Of course, you may want to [apply a function between all combinations of elements of the left and right argument](./array-logic-data-driven-conditionals.md#the-outer-product), but that will be addressed soon enough.

## Order of execution
Expressions are executed from right to left.

```K
 10*!2+5
```
```
0 10 20 30 40 50 60
```

??? Info "Show me step-by-step"
	To start, there is a literal number 5:
	```K
	 5
	5
	```
	
	Next, there is a plus `+` with a number 2 to its immediate left, so it is evaluated as two plus five:
	```K
	 2+5
	7
	```
	
	Then the symbol <dfn>enum</dfn> `!`. To its left is another function, multiply `*`, not a value. So the function is called <em>monadically</em>. The monadic form of `!` is the <dfn>index generator</dfn>, which generates an integer array of length defined by its right argument.
	```
	 !2+5
	0 1 2 3 4 5 6
	```
	
	Lastly, another dyadic function, we multiply our list by ten:
	```
	 10*!2+5
	0 10 20 30 40 50 60
	```

The expression above is "ten *times* the indices from 0 to *two plus five*, or in short: "ten times enum two plus five". We can make it clearer using (superfluous) **parentheses** `()`.
```K
 10*(!(2+5))
```
```
0 10 20 30 40 50 60
```

Of course, we can change the order of execution using different parentheses.

```K
 (10*!2)+5
```
```
5 15
```

??? Info "Show me step-by-step"
	Beginning from the right, there is a literal number 5:
	```K
	 (10*!2)+5
	        5
	```
	
	Then there is a plus symbol `+`. Before we can decide if it is being called monadically or dyadically, we must look to the left.
	
	```K
	      )+5
	```
	
	A right parenthesis. We must evaluate the contents of the parentheses to see if it is a function or a value.
	
	```K
	 (10*!2)
	```
	
	This expression evaluates to the list `0 10`. Since it is a value, it is used as the left argument to our plus function.
	
	```K
	 (10*!2)+5
	 (0  10)+5
	5 15
	```

Infix (dyadic) functions have a **short** *left* scope and **long** *right* scope. This means that they take the result of everything to their right hand side as their right argument. 

If there is one, the left argument is the value to the <em>immediate</em> left.

However, juxtaposed values form lists <em>before</em> any functions are applied. This is called <dfn>stranding</dfn> and lets us write very natural expressions, such as:

```K
 1 2 3 + 4 5 6
5 7 9
```

but this can lead to some surprises if we are not aware:

```K
 2 + 2 2 + 2
```
```
6 6
```

??? Info "Show me step-by-step"
	First, there is a literal number 2
	```K
	        2
	2
	```
	
	Then there is a symbol `+`. What, if any, is the value to its immediate left?
	```K
	     2 2 + 2
	```
	
	It is a 2-element vector `2 2`. The plus function maps between these elements and the single number on the right:
	```K
	     2 2 + 2
	4 4
	```
	
	Finally there is another addition. The overall evaluation looks like the following:
	```K
	 2 + 2 2 + 2
	 2 + 4 4
	 6 6
	```

## Comments
Anything after a forward slash `/` is ignored.

```K
 / nothing happens on this line
 2 * 3 / 4 5
```
```
6
```
---
```K
 "A"   / K uses double quotes for single char
```
```
"A"
```

## The reduction operator
Adding a list of numbers *could* become very tedious...
```K
 1+2+3+4+5+6+7+8+9+10+11+12+13+14+15
```
```
120
```

The **fold** [operator](./Operators.md) `F/` inserts the function `F` to its left between parts of the right argument array.
```K
 +/1 2 3 4 5 6 7 8 9 10 11 12 13 14 15
```
```
120
```

Fold is also known as *reduce* because it reduces the number of dimensions of its argument. In the example above, we have a **vector** (1 dimensional, list) argument and return a **scalar** (0 dimensional, single value) result.

## The index generator
The index generator `!i` generates integers up to the integer right argument `i`

```K
 !10
```
```
0 1 2 3 4 5 6 7 8 9
```

**Note:** APL's `⍳` starts at `1`, while K's `!` starts at 0.

So we can do an arithmetic sum as follows

|**Traditional Mathematical Notation (TMN)** | **APL** |
|---|---|
| $\sum_{n=1}^N n$ | `+/!N`

## What do these errors mean?

In the APL and BQN versions of this lesson, a negative number passed as the right argument to the index generator function produces an error. How can an item in an array have a negative index?

In K, `!i` simply extends the domain to negative numbers and continues to return a monotonically increasing array with `i` as the starting point:

```K
 !-4
```
```
-4 -3 -2 -1
```

Here's another behavior you might encounter:

```K
 1+
```
```
1+
```

What is this? We can use <dfn>type</dfn> `@` to interrogate the type:

```K
 @(1+)
```
```
`p
```

The symbol `\`p` represents a <dfn>projection</dfn>, which is a partially applied function. We'll talk more about that later.

Here's an actual error you might encounter:

```K
 a
```
```
'value
 a
 ^
```

A `'value` means that there is nothing associated with the name provided. We have not seen any named functions or variables yet; nothing has been assigned to the name `a`, so trying to use it in an expression is meaningless.

## Problem Set 1
1.   
	The average daily temperatures, in degrees Celcius, for 7 days are stored in a variable `adt`. Note that unlike in APL, BQN, and J for which we named this variable `t_allweek`, K does not permit `_` in symbols because it's a built-in function.
	
	```K
	 adt:11.7 8.6 9.7 14.2 6.7 11.8 9.2
	```
	
	Use K to compute the follwing:
	
	1. The highest daily temperature
	1. The lowest daily temperature
	1. The range of (difference between the largest and the smallest) temperatures
	1. Each temperature rounded to the nearest whole number

	??? Example "Answers"
		<ol type="a">
		<li>
		```K
		 |/adt
		14.2
		```
		</li>
		<li>
		```K
		 &/adt
		6.7
		```
		</li>
		<li>
		```K
		 (|/adt)-&/adt
		7.499999999999999
		```
		
		> You may have found the correct answer using the following expression:
		```K
		 |/adt-&/adt
		7.499999999999999
		```
		
		> but this is less efficient because it does more subtractions than it needs to. Recall the right-to-left evaluation:
		```K
		 |/ adt - &/ adt
		 |/ adt - 6.7
		 |/ 11.7 8.6 9.7 14.2 6.7 11.8 9.2 - 6.7
		 |/ 5 1.9 3 7.5 0 5.1 2.5
		 7.5
		```
		
		> if we use parentheses `()` to force K to compute the maximum of the list before doing subtraction, we only do a single subtraction instead of 7:
		```K
		 ( |/adt ) - &/ adt
		 ( |/adt ) - 6.7
		 ( 14.2  ) - 6.7
		 7.499999999999999
		```
		
		</li>
		<li>
		To round to the nearest whole number, add 0.5 and round down:
		```K
		 _0.5+adt
		12 9 10 14 7 12 9
		```
		</li>
		</ol>

1. A Mathematical Notation

	Use K to evaluate the following

	1. $\prod_{n=1}^{12} n$ (multiply together the first twelve integers)

	1. $\sum_{n=1}^{17}n^2$ (add together the first seventeen squared integers)

	1. $\sum_{n=1}^{100}2n$ (add together the first one hundred positive even integers)

	1. $\sum_{n=1}^{100}2n-1$ (add together the first one hundred odd integers)

	1. In TMN, the following expression is equal to `0`, why does the following return `70` in K?
		```K
		 84 - 12 - 1 - 13 - 28 - 9 - 6 - 15
		```
		```
		70
		```

*[TMN]: Traditional Mathematical Notation

	??? Example "Answers"
		<ol type="a">
		<li>
		```K
		 */1+!12
		479001600
		```
		</li>
		<li>
		Let's define a `pow` function that raises its left argument to the power of the right argument, and the a `sq` function that squares its argument:
		```K
		 pow:{*/y#x}
         sq:pow[;2]
		```
		</li>
		<li>
		Now we can define our solution in terms of `sq`:
		```K
		 +/sq'1+!17
        1785
		```

		The `'` is the each adverb, which applies the given function to each element in the argument list. We'll cover adverbs later.

		</li>
		<li>
		```K
		 +/2*1+!100
        10100
		```
		</li>
		<li>
		We can either subtract 1 from the even numbers:
		```K
		 +/(2*1+!100)-1
		10000
		```

		or we can add negative 1:
		```K
		 +/-1+2*1+!100
        10000
		```
		</li>
		<li>
		Remember the right-to-left rule: functions take everything to their right, and the first thing to their left. We can add unnecessary parentheses to show how APL evaluates our expression.
		```K
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
		```K
		 pow:{*/y#x}
		 sq:pow[;2]
		 +/sq'1+!4
		```
		```
		30
		```
		---
		```K
		/sq'1+!467
		```
		```
		34058310
		```
		</li>
		<li>
		Each $n$th layer has $(2n-1)^2$ cubes. There are $713,849,500$ cubes in a stack with $812$ layers.
		```K
		 /sq'-1+2*1+!4
		```
		```
		84
		```
		---
		```K
		 +/sq'-1+2*1+!812
		```
		```
		713849500
		```
		</li>
		<li>
		Each $n$th layer has $n^3$ cubes. There are $5,503,716$ cubes in a stack with $68$ layers.
		```K
		 cubed:pow[;3]
		 +/cubed'1+!3
		```
		```
		36
		```
		---
		```K
		 +/cubed'1+!68
		```
		```
		5503716
		```
		</li>
		</ol>

1. Rewrite the following expressions so that they do not use parentheses.
	1. `(%a)*b`
	1. `(%a)%b`
	1. `(a+b)-5`
	1. `(a+b)+5`

	???Example "Answers"
		<ol type="a">
		<li>Multiplication is commutative, which means that the order of arguments does not matter, so we can write `b*%a`. Even more simply, it is `b%a` because multiplication by a reciprocal is the same as division.</li>
		<li>${{{1}\over{a}}\div{b}} = {{1}\over{a\times{b}}}$ so we can write `%a*b`</li>
		<li>Use a literal negative five:`¯5+a+b`</li>
		<li>No parentheses needed: `a+b+5`</li>
		</ol>
