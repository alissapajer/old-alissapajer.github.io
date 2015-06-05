---
title: Category Theory in Code: What is a Category?
author: LuneTron
---

# Introduction

Category theory was developed as a generalization of mathematics itself, and so it is only natural
that its literature is full of mathematically abstract language and concepts. For this reason, the
standard category theory examples often fail to be useful, as they only make sense when one already
understands them. That sounds paradoxical, but it's true. For example, if you are a mathematician
who works with groups on a daily basis, then it would be logical to learn category theory using
examples from group theory. Thus as a programmer, it is best to learn category theory using examples
from programming langauges.

Starting from the beginning, consider a programming language as a whole. Looking beyond the
language's syntax, what are the basic structures the language enables you to create? How can you
combine those structures to produce new ones? In Scala, for example, you can write a class and then
create a trait that extends that class. Or in Java 8, you can create a lambda and then apply it to a
collection. Or in Python, you can write an if-statement that conditionally executes one of two
operations. Or in Haskell, you can compose two functions.

Category theory is a means to express the underlying structure of certain programming languages.
Many programming langauges have characteristics that can be described using category theory, and
some of these languages lend themselves quite naturally to such descriptions. Two such languages are
Haskell and Scala. They serve as a good examples due to the prominence therein of types and
functions. Furthermore, it is useful to see side-by-side examples in two languages so one can begin
to look past the syntax to the underlying concepts.

# Typed Functions

Let us now begin. We've decided that we want to write a function named `foo`. In Python, one could
write that function as follows:
```
def foo(input):
    # return stuff here
```
What do we know about `foo`? We know that if you provide it with an `input`, it will return
something. Or maybe it will just set a variable or print, and then return nothing. We're not
exactly sure.

In Scala and Haskell, function signatures provide us with more information, and it is exactly this
extra information that allows these languages to be described in terms of category theory. So what
is this extra information? This extra information consists of two types: an input (or argument) type
and a return type. In Haskell this looks like:
```
foo :: Bool -> Int
```
and in Scala:
```
def foo(input: Boolean): Int
```
Each of these function signatures tells us something about `foo`, namely that it requires (or takes)
a boolean as an argument and returns an integer.

In practice, a function is a means to produce one type given another. In the function `foo`, we
transform (or morph) from a boolean to an integer. The function implementation (which we omitted)
decides how we produce the integer from the boolean.

It is useful to think of a type as a way to summarize a collection of values. Instead of saying the
argument of `foo` requires one of two values (true or false), we say it requires a boolean. And
instead of saying the return type of `foo` could be 0 or 1 or -1 or ... we say the return type is an
integer.

# More about Types

Now the types we have at our disposal are not just the types built in to the language. In Scala and
Haskell is is possible to create your own types.

For example in Scala, one can create the types `Animal`, `Cat`, and `Dog`, and then write a function that
takes an `Animal` and returns a `Cat`:
```
sealed trait Animal
final case class Cat(name: String) extends Animal
final case object Dog(name: String) extends Animal

def catify(animal: Animal): Cat = animal match {
  case cat @ Cat(_) => cat
  case Dog(name) => Cat(name)
}
```
And the equivalent Haskell example:
```
lalala
```

# More about Functions

Now consider all the Haskell (or Scala) types. This includes all the built-in types, as well as any
type you could create yourself. Then for each pair of types `A` and `B`, consider all the functions
from type `A` to type `B`. In Scala we have
```
type A
type B
def foo(a: A): B = ???
```
And in Haskell:
```
lalala - think about forall
```

# Categories Hask and Scal

It's now time to introduce some category theory terminology. A category consists of "objects",
"morphisms", and "morphism composition". In the case of a programming langauge, the objects in the
category are the types, and the morphisms in the category are functions from one type to another.
And morphism composition is standard function composition, namely using the output of one function
as the input to another function. For example:
```
def f(a: A): B
def g(b: B): C

val a: A
g(f(a)): C    // also written as (g compose f)(a)   TODO is this right?
```
and
```
lalala
```

In summary, all the types in a category, in conjunction with functions between those types, form a
category. Sometimes it is useful to call a category be a name. The category of Haskell types is
named **Hask**. The category of Scala types doesn't have a popular name that I know of, so I'm going
to call it **Scal**.

# Laws

So there we have it! Now, to ensure we've defined something sane, there three rules that the objects
(types) and morphisms (functions) must satisfy in order to be a real category. Rules such as these
are the minimal set of rules that produce a sensical construction. Here go the rules:

(1) Imagine `f(g(h(x)))` .... This rule is know as the "associativity of function composition".

(2) What if `f compose g` weren't a function? It's hard to imagine what that would even mean. 

(3) What can we compose `f` with to produce exactly `f`?

# A Proof for Fun

Lastly, we're going to write a proof. Consider the identity function rules from (3). How many
ways can you implement an identity funciton that satisfies those rules? An obvious implementation in
Scala is:
```
def identity[T](a: T): T = a
def foo[A, B](a: A): B

val x: X
foo[X, Y](identity[X](x)) == identity[Y](foo[X, Y](x))
```
and in Haskell:
```
lalala
```

The 
