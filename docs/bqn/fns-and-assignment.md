# Functions and Assignment

??? Info "Before diving in"
	At this point, you should work through [Tutorial: BQN expressions](https://mlochbaum.github.io/BQN/tutorial/expression.html) to get a general, BQN-focused introduction.

	As part of that, reviewing [Expression syntax](https://mlochbaum.github.io/BQN/doc/expression.html) and [Types](https://mlochbaum.github.io/BQN/doc/types.html) would also serve you well, paying attention to sections about Roles in BQN.

## Functions
A <dfn>function</dfn> is an operation that takes one or two subject arguments. The left argument is called `𝕨` and the right argument `𝕩`.

You can define your own explicit functions using block syntax:

```BQN
      3{𝕨}5      # 𝕨 is the (optional) left argument
```
```
3
```
---
```BQN
      {𝕩}"apl"   # 𝕩 is the right argument
```
```
"apl"
```
---
```BQN
      {𝕨}5       # Calling a dyadic function monadically results in an error
```
```
Error: Left argument required

{𝕨}5
^^^
```
---
```BQN
      3{𝕩}       # Calling a function without a right argument results in an error
```
```
Error: Second-level parts of a train must be functions

3{𝕩}
^
```

From here, when functions are first introduced, `Fn 𝕩` denotes a monadic function `Fn` and `𝕨 Fn 𝕩` denotes a dyadic function.

## Assignment
Names are assigned with the left arrow `name ← expression`. We say "name gets [function or array]".

```BQN
      one←1
      three←3
      Equals←=
      Plus←+
      four←4
      four Equals one Plus three  # 1 means true, 0 means false
```
```
1
```

Note that the <dfn>role</dfn> of both sides of an assignment must match. If you try to assign `Equals` as `equals`, you get the following error:

```BQN
equals←=
```
```
Error: Role of the two sides in assignment must match

equals←=
^^^^^^
```

_Consult the resources in **Before diving in** above if the term "role" or this error doesn't make sense._

We can use a name in the same line in which it is defined. In production code it is best to avoid this unless an expression is very short.

Read the following as "squared numbers divided by the sum of squares":
```APL
      squared÷+´squared←¯1‿0‿1‿2⋆2
```
```
⟨ 0.16666666666666666 0 0.16666666666666666 0.6666666666666666 ⟩
```

## Mutating Variables
You may come across the following error:

```BQN
      Count ← {+´𝕩}
      count ← {+´𝕩} 1‿0‿0‿1‿0‿1‿0
```
```
Error: Redefinition

      count ← {+´𝕩} 1‿0‿0‿1‿0‿1‿0
      ^^^^^
```

BQN does not permit reassigning a variable within the same scope.  You can, however, mutate an already-defined variable using `↩`.

```BQN
      a ← 4
```
```
4
```
---
```BQN
      a √↩     # a is now the square root of its previous value
```
```
2
```
---
```BQN
      a ⋆↩ 3   # a is now its previous value cubed
```
```
8
```


These expressions expand as found in [BQN's online documentation](https://mlochbaum.github.io/BQN/doc/expression.html#assignment):

<table>
<thead>
<tr>
<th>Syntax</th>
<th>Meaning</th>
</tr>
</thead>
<tbody>
<tr>
<td><code><span class="Value">a</span> <span class="Function">F</span><span class="Gets">↩</span></code></td>
<td><code><span class="Value">a</span> <span class="Gets">↩</span> <span class="Function">F</span> <span class="Value">a</span></code></td>
</tr>
<tr>
<td><code><span class="Value">a</span> <span class="Function">F</span><span class="Gets">↩</span> <span class="Value">b</span></code></td>
<td><code><span class="Value">a</span> <span class="Gets">↩</span> <span class="Value">a</span> <span class="Function">F</span> <span class="Value">b</span></code></td>
</tr>
</tbody>
</table>

## Multiline functions
You can do quite a lot in a single line of BQN. However, it is not long before you want to keep sequences of multiple statements available for re-use. Of course we can write functions which consist of multiple statements.

The <dfn>statement separator</dfn>, `⋄` (diamond), allows us to write multiple APL statements in a single line. Some people think that it is more readable to spread multiple statements across multiple lines of a function. However, it is worth being aware that BQN diamonds `⋄` are equivalent to newline characters in terms of execution. The following two definitions of the `Mean` function are equivalent.

```BQN
 Mean ← {
	sum ← +´𝕩
	count ← ≢𝕩
	sum ÷ count
 }

 Mean ← { sum ← +´𝕩 ⋄ count ← ≢𝕩 ⋄ sum ÷ count }
```

Separate statements are executed from left to right and top to bottom.

## Problem set 2
The following problems can be solved with single-line functions.

1. Eggs

	A recipe serving 4 people uses 3 eggs. Write the function `Eggs` which computes the number of eggs which need cracking to serve `⍵` people. Using a fraction of an egg requires that a whole egg be cracked.

	```BQN
	      Eggs 4
	```
	```
	3
	```
	---
	```BQN
	      Eggs 100
	```
	```
	75
	```
	---
	```BQN
	      Eggs 1+↕12
	```
	```
	1 2 3 3 4 5 6 6 7 8 9 9
	```

	???Example "Answer"
		```BQN
		Eggs ← {⌈𝕩×3÷4}
		```

1. Write a function `To` which returns integers from `𝕨` to `𝕩` inclusive.

	```BQN
	      3 To 3
	3
	      3 To 4
	⟨ 3 4 ⟩
	      1 To 7
	⟨ 1 2 3 4 5 6 7 ⟩
	      ¯3 To 5
	⟨ ¯3 ¯2 ¯1 0 1 2 3 4 5 ⟩
	```

	**BONUS:** What if `𝕨>𝕩`?  
	```BQN
	      3 To 5
	⟨ 3 4 5 ⟩
	      5 To 3
	⟨ 5 4 3 ⟩
	      5 To ¯2
	⟨ 5 4 3 2 1 0 ¯1 ¯2 ⟩
	```

	???Example "Answer"
		In the simple case, make sure to generate enough numbers and use `𝕨` as an offset:  
		```APL
		To ← {𝕨+↕1+𝕩-𝕨}
		```
		In general we take into account whether the difference is positive or negative:  
		```APL
		To ← {𝕨+(×d)×↕1+|d←𝕩-𝕨}
		```

1. The formula to convert temperature from Celsius ($T_C$) to Fahrenheit ($T_F$) in traditional mathematical notation is as follows:

	$$T_F = {32 + {{9}\over{5}}\times {T_C}}$$  

	Write the function `CtoF` to convert temperatures from Celcius to Farenheit.  
	```BQN
	      CtoF 11.3‿23‿0‿16‿¯10‿38
	⟨ 52.34 73.4 32 60.8 14 100.4 ⟩
	```

	???Example "Answer"
		```BQN
		CtoF ← {32+⍵×9÷5}
		```

1. Prime Time

	A prime number is a positive whole number greater than $1$ which can be divided only by itself and $1$ with no remainder.

	Write a function which returns `1` if its argument is prime and `0` otherwise.

		          IsPrime 21
	    0
		          IsPrime 17
	    1

	???Example "Answer"
		There are several ways to code this, but the basic method is to count the number of divisors.
		```BQN
		IsPrime ← {2=+´d=⌊d←𝕩÷1+↕𝕩}
		IsPrime ← {2=+´0=(1+↕𝕩)|𝕩}
		```