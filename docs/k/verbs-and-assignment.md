# Verbs and Assignment

## Verbs

In K, normal functions—those that take noun arguments and produce noun results—are called <dfn>verbs</dfn>.

A custom verb definition starts and ends with curly braces `{}`. It can receive either 1, 2, or 3 arguments, which are assigned the variables `x`, `y`, and `z` respectively.

Built-in verbs can be used either in infix position or prefix; custom verbs can only be used in prefix position.

```K
 {x}3       / x is the first argument
3
 {x}[3]     / you can optionally use [] for one arg, required for 2+
3
 {y}[3;4]   / y is the second argument
4
 {z}[3;4;5] / z is the third argument
5
 {x+y}[3]     / providing too few args returns a partially-applied verb
{x+y}[3;]
 plus3:{x+y}[3] / See it in action
 plus3 4
7
```

See K's built-in documentation (enter `\\` at the REPL) to learn how verbs are specified. Most built-in verbs have different functionality depending on both the number and type of arguments provided.

## Assignment
Primitives are the words defined by the system, but you can create your own names using the primitive `:` called <dfn>right</dfn>.

```K
 one:1
 three:3
 equals:=
 plus:+
 four:4
 equals[four;plus[three;one]]  # 1 means true, 0 means false
```
```
1
```

We can use a name in the same line in which it is defined. In production code it is best to avoid this unless an expression is very short.

Read the following as "squared numbers divided by the sum of squares":

```K
 squared%+/squared:{x*x}(-1 0 1 2)
```
```
0.16666666666666666 0.0 0.16666666666666666 0.6666666666666666
```

Unlike APL and BQN, in K you are free to redefine names with new values of any type.

## Multiline functions

You can do quite a lot in a single line of K. However, it is not long before you want to keep sequences of multiple statements available for re-use. Of course we can write functions which consist of multiple statements.

Multiline definitions must be written in a file and loaded via `\l`. You can separate each statement by a newline. Lines are evaluated top to bottom. **Note:** Your K source files _must_ end with a newline, otherwise you will get an `'eoleof` error.

The <dfn>statement separator</dfn> `;` allows us to write multiple K statements in a single line. Some people think that it is more readable to spread multiple statements across multiple lines of a function. However, it is worth being aware that K separators `⋄` are equivalent to newline characters in terms of execution. The following two definitions of the `mean` function are equivalent.


```K
mean:{
 sum:+/y
 count:#y
 sum % count
}

mean:{sum:+/x;count:#x;sum % count}
```

Separate statements are executed from left to right and top to bottom.

## Problem set 2

The following problems can be solved with single-line dfns.

1.  Eggs

    A recipe serving 4 people uses 3 eggs. Write the function `eggs` which computes the number of eggs which need cracking to serve `y` people. Using a fraction of an egg requires that a whole egg be cracked.

    ```K
     eggs 4
    ```
    ```
    3
    ```
    ---
    ```K
     eggs 100
    ```
    ```
    75
    ```
    ---
    ```K
     eggs 1+!12
    ```
    ```
    1 2 3 3 4 5 6 6 7 8 9 9
    ```

    ???Example "Answer"
        ```K
         / Play with -_- step by step to see how it implements ceiling
         eggs:{-_-x*3%4}
        ```
	

1.  Write a function `to` which returns integers from `x` to `y` inclusive.

    ```K
     to[3;3]
    ,3
     to[3;4]
    3 4
     to[1;7]
    1 2 3 4 5 6 7
     to[-3;5]
    -3 -2 -1 0 1 2 3 4 5
    ```

    **BONUS:** What if `⍺>⍵`?

    ```K
     to[3;5]
    3 4 5
     to[5;3]
    5 4 3
     to[5;-2]
    5 4 3 2 1 0 -1 -2
    ```

    ???Example "Answer"
        In the simple case, make sure to generate enough numbers and use `x` as an offset:
        ```K
         to:{x+!1+y-x}
        ```

    In general we take into account whether the difference is positive or negative. In K, there are no builtins for absolute value or signum, so we define these as well:
    
    ```K
     signum:{(x>0)-x<0}
     abs:{$[x<0;x*-1;x]}
     to:{d:y-x;x+(signum[d])*!1+abs[d]}
    ```

1.  The formula to convert temperature from Celsius ($T_C$) to Fahrenheit ($T_F$) in traditional mathematical notation is as follows:

    $$T_F = {32 + {{9}\over{5}}\times {T_C}}$$

    Write the function `CtoF` to convert temperatures from Celcius to Farenheit.

    ```K
     CtoF 11.3 23 0 16 -10 38
    52.34 73.4 32 60.8 14 100.4
    ```

    ???Example "Answer"
        ```K
         CtoF:{32+x*9%5}
        ```

1.  Prime Time

    A prime number is a positive whole number greater than $1$ which can be divided only by itself and $1$ with no remainder.

    Write a verb which returns `1` if its argument is prime and `0` otherwise.

    ```K
     isPrime 21
    0
     isPrime 17
    1
    ```

    ???Example "Answer"
        There are several ways to code this, but the basic method is to count the number of divisors.
        ```K
         isPrime:{2=+/d=_d:x%1+!x}
         isPrime:{2=+/0=(1+!x)!'x}
        ```
