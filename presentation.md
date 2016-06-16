autoscale: true
build-lists: true

---

# A Tour of Functional Typeclasses #

A brief introduction to some basic typeclasses ilustrating
the power of coding to abstractions

[@raulraja](https://twitter.com/raulraja) CTO [@47deg](https://twitter.com/47deg)

---

## Acknowledgment ##

- [Cats](https://github.com/typelevel/cats)
- [Typeclassopedia](https://wiki.haskell.org/Typeclassopedia)
- [FP](https://wiki.haskell.org/Functional_programming)
- [Abstractions](https://en.wikipedia.org/wiki/Abstraction_%28software_engineering%29)
- [Simulacrum](https://github.com/mpilquist/simulacrum)

---

## Overview ##

Typeclasses & Data Structures

---

## What is Functional Programming ##

> In computer science, functional programming 
> is a programming paradigm. 

> A style of building the structure and elements 
> of computer programs that treats computation 
> as the evaluation of mathematical functions 
> and avoids changing-state and mutable data.

-- Wikipedia

---

## Common traits of Functional Programming ##

- Higher-order functions
- Immutable data
- Referential transparency
- Lazy evaluation
- Recursion
- Encouragement of Abstractions

---

### Higher Order Functions ##

When a functions takes another function as argument 
or returns a function as return type:

```scala
def transform[B](list : List[Int])(transformation : Int => B) =
	list map transformation
	
transform(List(1, 2, 4))(x => x * 10)
```

---

## Inmutable data ##

Once a value is instantitated it can't be mutated in place.
How can we change it's content then?

```scala
case class Conference(name : String)
```

---

## Referential Transparency ##

When a computation returns the same value
each time is invoked

Transparent : 

```scala
def pureAdd(x : Int, y : Int) = x + y
```

Opaque :

```scala
var x = 0
def impureAdd(y : Int) = x + y
```

---

## Lazy Evaluation ##

When a computation returns the same value
each time is invoked 

```scala
def boom = throw new RuntimeException

def strictEval(f : Any) = println { "Hello Strict World!" ; f }

def lazyEval(f : => Any) = println { "Hello Lazy World!" ; f }
```

---

## What is a Typeclass ##

A typeclass is an interface/protocol that provides
a behavior for a given data type.

This is also known as **Ad-hoc Polymorphism**

We will learn typeclasses by example...

---

## Typeclasses ##

[ ] **Monoid**
[ ] **Functor**
[ ] **Cartesian**

---

## Typeclasses ##

[ ] **Monoid** : Combine values of the same type
[ ] **Functor** : Transform values inside contexts
[ ] **Cartesian** : Compose independent computations 

---

## Monoid ##

A `Monoid` expresses the ability of a value of a 
type to `combine` itself with other values of the same type
in addition it provides an `empty` value.

```scala
import simulacrum._

@typeclass trait Monoid[A] {
	@op("|+|") def combine(x : A, y : A) : A
	def empty : A
}
```

---

## Monoid ##

```scala
implicit val IntAddMonoid = new Monoid[Int] {
	def combine(x : Int, y : Int) : Int = ???
	def empty = ???
}
```

---

## Monoid ##

```scala
implicit val IntAddMonoid = new Monoid[Int] {
	def combine(x : Int, y : Int) : Int = x + y
	def empty = 0
}
```

---

## Monoid ##

```scala
implicit val StringConcatMonoid = new Monoid[String] {
	def combine(x : String, y : String) : String = x + y
	def empty = ""
}
```

---

## Monoid ##

```scala
implicit def ListConcatMonoid[A] = new Monoid[List[A]] {
	def combine(x : List[A], y : List[A]) : List[A] = x ++ y 		
	def empty = Nil
}
```

---

## Monoid ##

We can code to abstractions instead of coding to concrete
types.

```scala
import Monoid.ops._

def uberCombine[A : Monoid](x : A, y : A) : A =
	x |+| y

uberCombine(10, 10)
```

---

## Functor ##

A `Functor` expresses the ability of a container
to transform its content given a function

```scala
@typeclass trait Functor[F[_]] {
	def map[A, B](fa : F[A])(f : A => B) : F[B]
}
```

---

## Functor ##

Most containers transformations can be expressed as
Functors.

```scala
implicit def ListFunctor = new Functor[List] {
	def map[A, B](fa : List[A])(f : A => B) = fa map f
}
```

---

## Functor ##

Most containers transformations can be expressed as
Functors.

```scala
implicit def OptionFunctor = new Functor[Option] {
	def map[A, B](fa : Option[A])(f : A => B) = fa map f
}
```

---

## Functor ##

Most containers transformations can be expressed as
Functors.

```scala
import scala.concurrent.Future
import scala.concurrent.ExecutionContext.Implicits.global

implicit def FutureFunctor = new Functor[Future] {
	def map[A, B](fa : Future[A])(f : A => B) = fa map f
}
```

---

## Functor ##

We can code to abstractions instead of coding to concrete
types.

```scala
def uberMap[F[_] : Functor, A, B](fa : F[A])(f : A => B) : F[B] =
	Functor[F].map(fa)(f)

uberMap(List(1, 2, 3))(x => x * 2)
```

---

## Cartesian ##

`Cartesian` captures the idea of composing independent effectful values

```scala
@typeclass trait Cartesian[F[_]] {
  @op("|@|") def product[A, B](fa: F[A], fb: F[B]): F[(A, B)]
}
```

---

## Cartesian ##

`Cartesian` captures the idea of composing independent effectful values

```scala
implicit def FutureCartesian = new Cartesian[Future] {
	def product[A, B](fa: Future[A], fb: Future[B]): Future[(A, B)] = 
		fa zip fb
}
```

---

## Cartesian ##

`Cartesian` captures the idea of composing independent effectful values

```scala
implicit def ListCartesian = new Cartesian[List] {
	def product[A, B](fa: List[A], fb: List[B]): List[(A, B)] = 
		fa zip fb
}
```

---

## Cartesian ##

We can code to abstractions instead of coding to concrete
types.

```scala
def allProducts[F[_] : Cartesian, A, B](fa : F[A], fb : F[B]) : F[(A, B)] =
	Cartesian[F].product(fa, fb)

allProducts(List(1, 2, 3), List(4, 5, 6))
```

---

## Typeclasses ##

Can we combine multiple abstractions and
behaviors?



---

## Recap ##

---

## What's next? ##

---

## Questions? & Thanks! ##

@raulraja
@47deg
http://github.com/47deg/typeclass-tour
https://speakerdeck.com/raulraja/typeclass-tour

---
