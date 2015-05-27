---
title: Category Theory: Categories
author: LuneTron
---

Category theory was developed as a generalization of mathematics itself, and so it is only natural
that its literature is full of mathematically abstract language and concepts. For this reason, the
standard category theory examples often fail to be useful, as they only make sense when one already
understands them. That sounds paradoxical, but it's true. For example, if one works with groups on a
daily basis, then it would be logical to learn category theory using examples from group theory.
Thus for programmers it is best to learn about category theory using examples from code.

Starting from the beginning, consider a programming language as a whole. Looking beyond the
language's syntax, what are the basic structures the language enables you to create? How can you
combine those structures to produce new ones? In Scala, for example, one can create a class and then
create a trait that extends that class. Or in Java, one can create a local variable, and then use
that variable in scope. Or in Python, one can create an if-statement that conditionally executes one
of two operations. Or in Haskell, one can compose two functions.

So how does category theory fit in to the larger picture of programming languages? Category theory
is one way to express the underlying structure of certain programming languages. Many programming
langauges have characteristics that can be described using category theory, and some of these
languages lend themselves quite naturally to such descriptions. Two such languages are Haskell and
Scala. They serve as a good examples due to the prominent role types and functions play in the both
languages. Furthermore, it's useful to see side-by-side examples in two languages so one can begin
to look past the syntax and understand the underlying concepts.

Let us now begin. We've decided that we want to write a function named `foo`. In Python, I could
write that function as follows:
```
def foo(input):
    # return stuff here
```
What do we know about `foo`? We know that if you provide it with an `input`, it will return
something. Or maybe it will just set variable or print, and then return nothing. We're not
exactly sure.

In Scala and Haskell, function signatures must provide more information, and it is exactly this
extra information that allows these languages to be described in terms of category theory. So what
is this extra information? Two types: an input (or argument) type and a return type. In Haskell this
looks like
```
foo :: Bool -> Int
```
and in Scala
```
def foo(input: Bool): Int
```
Each of these function signatures tells us something about `foo`, namely that it requires a boolean
as an argument and returns an integer. Colloquially, this is often spoken as: "foo takes a boolean and
returns an integer".

In practice, a function is a way to get from one type to another. In the function named `foo`, we
transform (or morph) from a boolean to an integer. The function implementation (which we omitted)
decides how we produce the integer from the boolean.

A type is a way to summarize a collection of values. Instead of saying `foo` returns true or false,
we say it returns a boolean. And instead of saying the argument of `foo` can be "a" or "b" or "c"
or ... we say the argument is a String. 

Consider all the Haskell (or Scala) types. Then for each pair of types, consider all the functions
from the first type to the second type.

Also, make your own types!

