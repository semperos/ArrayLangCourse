# Basic syntax

## Functions and arguments
Functions are called <dfn>verbs</dfn> in J.

J has two-argument, infix verbs. These are called <dfn>dyadic</dfn> verbs.

```J
   3 * 5
```
```
15
```
---
```J
   3 - 5
```
```
_2
```

Some verbs map between elements of their left and right argument arrays. It is easy to add lists of numbers together:
```J
   1 2 3 + 4 5 6
```
```
5 7 9
```

Negative numbers are written with an <dfn>underscore</dfn> `_` to differentiate between negation (`-3`) and literal negative numbers (`_3`).
```J
   1 2 3 - 1 0 _1
```
```
0 2 4
```

There are also one-argument, prefix verbs. These are called <dfn>monadic</dfn> verbs.
```J
   - 5 _3 0 _4 2
```
```
_5 3 0 4 _2
```
---
```J
   |. 1 2 3 4 5
```
```
5 4 3 2 1
```

All J built-ins are either one or two-character ASCII symbols.

Some symbols represent both a monadic and a dyadic verb, but these are often closely related. As we will see later, even user-defined verbs can be monadic, dyadic or even both (<dfn>ambivalent</dfn>).

**:bulb: Try this**: Use these verbs monadically and dyadically:

<div class="center language-J" markdown="span">
<div class="displayBox" markdown="span">
<a class="glyph" title="Conjugate/Plus">`+`</a>
<a class="glyph" title="Negate/Minus">`-`</a>
<a class="glyph" title="Signum/Times">`*`</a>
<a class="glyph" title="Reciprocal/Divide">`%`</a>
<a class="glyph" title="Magnitude/Residue">`|`</a>
<a class="glyph" title="Exponential/Power">`^`</a>
<a class="glyph" title="Natural Log/Logarithm">`^.`</a>
<a class="glyph" title="Ceiling/Greater of/Max">`>.`</a>
<a class="glyph" title="Floor/Lesser of/Min">`<.`</a>
</div>
</div>

## Singleton extension
Dyadic verbs can map between a single value and an array of values.
```J
   3 * 1 10 100
```
```
3 30 300
```
---
```J
   3 = 1 2 3 4 5
```
```
0 0 1 0 0
```

**:bulb: Try this**: Replace the verbs in the previous two expressions with:
<a class="glyph" title="Max">`>.`</a>
<a class="glyph" title="Min">`<.`</a>
<a class="glyph" title="Less than">`<`</a>

While experimenting, you may cause a `LENGTH ERROR`:

```J
   1 2+3 4 5
```
```
|length error
|   1 2    +3 4 5
|[-0] 
```

Verbs such as `+ * >.` apply between elements of two arrays of the same shape, or between one element and many if one of the arguments is a single value. However, if the arrays are of two different shapes, it is not clear how the verb should be applied. Of course, you may want to [apply a verb between all combinations of elements of the left and right argument](./array-logic-data-driven-conditionals.md#the-outer-product), but that will be addressed soon enough.

## Order of execution
Expressions are executed from right to left.

```J
   10*i.2+5
```
```
0 10 20 30 40 50 60
```

??? Info "Show me step-by-step"
	To start, there is a literal number 5:
	```J
	   5
	5
	```
	
	Next, there is a plus `+` with a number 2 to its immediate left, so it is evaluated as two plus five:
	```J
	   2+5
	7
	```
	
	Then the symbol <dfn>idot</dfn> `i.`. To its left is another verb, times `*`, not a value. So the verb is called <em>monadically</em>. The monadic form of `i.` is the <dfn>index generator</dfn>, which generates an integer array of length defined by its right argument.
	```
	   i.2+5
	0 1 2 3 4 5 6
	```
	
	Lastly, another dyadic verb, we multiply our list by ten:
	```
	   10*i.2+5
	0 10 20 30 40 50 60
	```

The expression above is "ten *times* the indices from 0 to *two plus five*, or in short: "ten times idot two plus five". We can make it clearer using (superfluous) **parentheses** `()`.
```J
   10*(i.(2+5))
```
```
0 10 20 30 40 50 60
```

Of course, we can change the order of execution using different parentheses.

```J
   (10*i.2)+5
```
```
5 15
```

??? Info "Show me step-by-step"
	Beginning from the right, there is a literal number 5:
	```J
	   (10*i.2)+5
	            5
	```
	
	Then there is a plus symbol `+`. Before we can decide if it is being called monadically or dyadically, we must look to the left.
	
	```J
	      )+5
	```
	
	A right parenthesis. We must evaluate the contents of the parentheses to see if it is a verb or a value.
	
	```J
	   (10*i.2)
	```
	
	This expression evaluates to the list `0 10`. Since it is a value, it is used as the left argument to our plus verb.
	
	```J
	   (10*i.2)+5
	   (0   10)+5
	5 15
	```

Infix (dyadic) verbs have a **short** *left* scope and **long** *right* scope. This means that they take the result of everything to their right hand side as their right argument. 

If there is one, the left argument is the value to the <em>immediate</em> left.

However, juxtaposed values form lists <em>before</em> any verbs are applied. This is called <dfn>stranding</dfn> and lets us write very natural expressions, such as:

```J
   1 2 3 + 4 5 6
5 7 9
```

but this can lead to some surprises if we are not aware:

```J
   2 + 2 2 + 2
```
```
6 6
```

??? Info "Show me step-by-step"
	First, there is a literal number 2
	```J
	          2
	2
	```
	
	Then there is a symbol `+`. What, if any, is the value to its immediate left?
	```J
	       2 2 + 2
	```
	
	It is a 2-element vector `2 2`. The plus verb maps between these elements and the single number on the right:
	```J
	       2 2 + 2
	4 4
	```
	
	Finally there is another addition. The overall evaluation looks like the following:
	```J
	   2 + 2 2 + 2
	   2 + 4 4
	   6 6
	```

## Comments
Anything after a _nota bene_ symbol `NB.` is ignored.

```J
   NB. nothing happens on this line
   2 * 3 NB. 4 5
```
```
6
```
---
```J
   'A'   NB. It means "note well"
```
```
A
```

## The reduction operator
Adding a list of numbers *could* become very tedious...
```J
   1+2+3+4+5+6+7+8+9+10+11+12+13+14+15
```
```
120
```

The **insert** [adverb](./Operators.md) `u/` inserts the verb `u` to its left between parts of the right argument array.
```J
   +/1 2 3 4 5 6 7 8 9 10 11 12 13 14 15
```
```
120
```

It is called *insert* because it has the effect of inserting the verb to its left between each element of its right argument. As a reduction operation, it also reduces the number of dimensions of its argument. In the example above, we have a **vector** (1 dimensional, list) argument and return a **scalar** (0 dimensional, single value) result.

## The index generator
The index generator `i. y` generates integers up to the integer right argument `y`
```J
   i.10
```
```
0 1 2 3 4 5 6 7 8 9
```

**Note:** APL's `⍳` starts at `1`, while J's `i.` starts at 0.

So we can do an arithmetic sum as follows

|**Traditional Mathematical Notation (TMN)** | **J** |
|---|---|
| $\sum_{n=1}^N n$ | `+/i.N`

## What do these errors mean?

In the APL and BQN versions of this lesson, a negative number passed as the right argument to the index generator function produces an error. How can an item in an array have a negative index?

In J, `i. y` treats a negative `y` as a signal to reverse the order of the indices produced:

```J
   i._4
```
```
3 2 1 0
```

Here's another error you might encounter:

```J
   1+
```
|syntax error
|       1+
|[-0] 
```

A `syntax error` means that the expression which you tried to execute does not make sense. In the case above, it is because verbs always either take a single argument to their right or two arguments, one to the right and one to the left. Verbs never take a single argument to their left.

```J
   a
|value error: a
|[-0]
```

A `value error` means that there is nothing associated with the name provided. We have not seen any named verbs or variables yet; nothing has been assigned to the name `a`, so trying to use it in an expression is meaningless.

## Problem Set 1
1.   
	The average daily temperatures, in degrees Celcius, for 7 days are stored in a variable `t_allweek`.
	
	```J
	t_allweek =: 11.7 8.6 9.7 14.2 6.7 11.8 9.2
	```
	
	Use J to compute the follwing:
	
	1. The highest daily temperature
	1. The lowest daily temperature
	1. The range of (difference between the largest and the smallest) temperatures
	1. Each temperature rounded to the nearest whole number

	??? Example "Answers"
		<ol type="a">
		<li>
		```J
		   >./t_allweek
		14.2
		```
		</li>
		<li>
		```J
		   <./t_allweek
		6.7
		```
		</li>
		<li>
		```J
		   (>./t_allweek)-<./t_allweek
		7.5
		```
		
		> You may have found the correct answer using the following expression:
		```J
		   >./t_allweek-<./t_allweek
		7.5
		```
		
		> but this is less efficient because it does more subtractions than it needs to. Recall the right-to-left evaluation:
		```J
		   >./   t_allweek           - <./ t_allweek
		   >./   t_allweek           - 6.7
		   >./ 11.7 8.6 9.7 14.2 6.7 11.8 9.2 - 6.7
		   >./ 5 1.9 3 7.5 0 5.1 2.5
		   7.5
		```
		
		> if we use parentheses `()` to force J to compute the maximum of the list before doing subtraction, we only do a single subtraction instead of 7:
		```J
		   ( >./t_allweek ) - <./ t_allweek
		   ( >./t_allweek ) - 6.7
		   (     14.2    ) - 6.7
		   7.5
		```
		
		</li>
		<li>
		To round to the nearest whole number, either add 0.5 and round down:
		```J
		   <.0.5+t_allweek
		12 9 10 14 7 12 9
		```
		
		or subtract 0.5 and round up:
		```J
		   >.t_allweek-0.5
		12 9 10 14 7 12 9
		```
		</li>
		</ol>

1. A Mathematical Notation

	Use J to evaluate the following

	1. $\prod_{n=1}^{12} n$ (multiply together the first twelve integers)

	1. $\sum_{n=1}^{17}n^2$ (add together the first seventeen squared integers)

	1. $\sum_{n=1}^{100}2n$ (add together the first one hundred positive even integers)

	1. $\sum_{n=1}^{100}2n-1$ (add together the first one hundred odd integers)

	1. In TMN, the following expression is equal to `0`, why does the following return `70` in J?
		```J
		   84 - 12 - 1 - 13 - 28 - 9 - 6 - 15  
		```
		```
		70
		```

*[TMN]: Traditional Mathematical Notation

	??? Example "Answers"
		<ol type="a">
		<li>
		```J
		   */1+i.12
        479001600
		```
		</li>
		<li>
		```J
		   +/(1+i.17)^2  NB. Using Power
		   +/*:1+i.17    NB. Using Square
     1785
		```
		The first version using Power `^` requires parentheses, since without parentheses we get the sum of the first 289 integers (17 squared), instead of the first 17 integers squared.

		|TMN|J|
		|---|---|
		|$\sum_n^{17^2} n$|`+/1+i.17^2`
		|$\sum_n^{17} n^2$|`+/1+(i.17)^2`|

		The second formulation using the Square `*:` verb directly avoids this.

		</li>
		<li>
		```J
		   +/2*1+i.100
     10100
		```
		</li>
		<li>
		We can either subtract 1 from the even numbers:
		```J
		   +/(2*1+i.100)-1
		10000
		```

		or we can add negative 1:
		```J
		   +/_1+2*1+i.100
     10000
		```
		The underscore denotes a literal negative, whereas the hyphen indicates subtraction.
		</li>
		<li>
		Remember the right-to-left rule: verbs take everything to their right, and the first thing to their left. We can add unnecessary parentheses to show how J evaluates our expression.
		```J
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
		```J
			+/(1+i.4)^2  NB. Using Power
			+/*:1+i.4    NB. Using Square
		```
		```
		30
		```
		---
		```J
			+/(1+i.467)^2  NB. Using Power, floating point result
		```
		```
		3.40583e7
		```
		---
		```J
			+/*:1+i.467    NB. Using Square, integer result
		```
		```
		34058310
		```
		</li>
		<li>
		Each $n$th layer has $(2n-1)^2$ cubes. There are $713,849,500$ cubes in a stack with $812$ layers.
		```J
			+/(_1+2*1+i.4)^2  NB. Using Power
			+/*:_1+2*1+i.4    NB. Using Power
		```
		```
		84
		```
		---
		```J
			+/(_1+2*1+i.812)^2  NB. Using Power, floating point result
		```
		```
		7.1385e8
		```
		---
		```J
			+/*:_1+2*1+i.812    NB. Using Square, integer result
		```
		```
		713849500
		```
		</li>
		<li>
		Each $n$th layer has $n^3$ cubes. There are $5,503,716$ cubes in a stack with $68$ layers.
		```J
			+/(1+i.3)^3
		```
		```
		36
		```
		---
		```J
			+/(1+i.68)^3  NB. Power returns a floating point result
		```
		```
		5.50372e6
		```
		---
		```J
			>.+/(1+i.68)^3  NB. Round up or down to coerce to integer
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
