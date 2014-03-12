---
title: Haskell $
author: LuneTron
---

What is precedence? I learned about precedence when I was a kid learning arithmetic. Multiplication takes precedence over addition. And exponentiation takes precedence over multiplication. And precedence is transitive. 

I think Haskell is broken because common terms like addition and multiplication break Haskell's normal precedence rules. The `(*)` function and the `(+)` function are only intuitive because of the good old order of operations that we all learned in school.

Really, why does order of operations even exist? Is it logical in any way? It saves us from writing lots of parentheses, and ultimately from making mistakes by miscounting opening and closing parens. And it makes the functions more human readable. But is there any inherent about multiplication that makes it take precedence over addition? In other words, could `3 + 4 * 5` implicitly equal `(3 + 4) * 5` instead?

`4 * 5` is defined to be `5 + 5 + 5 + 5`. And 

With each passing day, if something as basic as precedence isn't encoded in the types, or if it's not consistent across all functions, then something is broken. 

The space has the highest precedence in Haskell, meaning that 

how does all this precedence relate back to single parameter functions and lambda calculus? Well, application is left associative, and and abstraction is right associative.


I want all type signatures to carry with them a notion of precedence and fixity.

So really :t does not provide sufficient information. we need :i to tell us more information. Because even if two implementations are "equivalent" functionally, they act differently when applied multiple times in a row. So as single functions applied just to two arguments in a vacuum, they are equivalent (see (++) and my wrapped version), but once we do `a ++ b ++ c` we have to deal with associativity. And when we add other operators in there like `a ++ b :: c` we have to also deal with precedence! Folks, we do not just have types to deal with. And the problem is, the compiler will not tell you if you assumed one association or one precedence, and you actually have another! This seems like a giant hole for bugs to me.

To please those Haskell could have two operators, `*` and `special*` where `special*` is multiplication with the built in precedence.


What's changed: fixity/precedence and type are both attributes of a function, and fixity is just as important as type. But fixity seems a lot less sound than types do. Because if I try to apply a function to arguments of the wrong type, the compiler will tell me. but if my function has the wrong fixity, and I omit parens accidentally, then the compiler will not tell me! 

just by wrapping up a built in infix function in another name, I'm invisibly changing its fixity, which changes the meaning of expressions.

```
myConcat :: [a] -> [a] -> [a]
myConcat = (++)
```

Why did I never think about this in Scala? In Scala there were no homemade infix operators

This seems like cheating, making in infix operator this way: 
```
scala> class Minus(left: Int) { def >>:(right: Int): Int = left - right }
```
We're just using the fact that we can do `new Minus(5).>>:(3)` which is just how you access a method in a class.


```
scala> class Minus1(left: Int) { def minus(right: Int): Int = left - right }
defined class Minus1

scala> class Minus2(left: Int) { def >>:(right: Int): Int = left - right }
defined class Minus2

scala> object Implicits { implicit def tominus(i: Int) = new Minus1(i); implicit def tominus2(i: Int) = new Minus2(i) }
warning: there were 2 feature warning(s); re-run with -feature for details
defined module Implicits

scala> 8 >>: 3 >>: 7
res42: Int = -4

scala> 8 minus 3 minus 7
res43: Int = -2

scala> 7 minus 3 minus 8
res44: Int = -4

scala> 7 >>: 3 >>: 8
res45: Int = -2
```

So, subtraction. It never occurred to me to that subtraction is a left-associative operator. I knew that by convention `2 - 3 - 4` means that you subtract 3 from 2, and then subtract 4 from that result. But actually thinking of it in terms of associativity, now that's really cool. Though, why is Haskell `(+)` also left-associative? (See http://www.haskell.org/onlinereport/decls.html and http://www.haskell.org/tutorial/functions.html) Numeric addition can be performed in any order, the the `(+)` function is defined on the type class `Num`, so why? Same question goes for `(*)`. Shouldn't that not have a left-right association? 

&& and || being right associative is clever, because then they can be smart and opt out early, after just looking at the first element.
