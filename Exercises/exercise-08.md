# Exercise Session 8

## Question 1

In this exercise, we will define instances of the `Eq` type class.
Recall the `Ordering[A]` type class introduced in the lecture.
An instance of `Ordering[A]` allows us to compare values of type `A`
to see which one (if any) is smaller.
Similarly, an instance of `Eq[A]` allows us to compare values of type `A` to see if they are equal.
We can define it as follows:

```scala
trait Eq[T]:
  extension (x: T)
    def === (y: T): Boolean
```

### Question 1.1

Write a `given` instance to create `Eq[List[T]]` from a `Eq[T]`.


### Question 1.2

Write a `given` instance to create `Eq[(T, U, S)]` from `Eq[T]`, `Eq[U]` and `Eq[S]`.

### Question 1.3

Write `given` instance to create `Eq[Person]`. Make use of both the definitions you have written previously.

```scala
case class Person(name: String, age: Int, neighbours: List[String])
```

### Question 1.4

Explicitly write the `using` argument to `summon` (you may need to assign names to your `given` definitions):

```scala
summon[Eq[Person]](using ...)
```

## Question 2

In this exercise, we will use term inference to calculate types based on other types.

First, we define `Nat`:

```scala
trait Nat

/** Zero */
case object Z extends Nat
type Z = Z.type

/** The Successor of N */
case class S[N <: Nat](pred: N) extends Nat
```

`Nat` encodes natural numbers on the type level:

```scala
S(S(Z))
// res0: S[S[Z]] = S(S(Z))
```

The type `S[S[Z]]` represents the successor of the successor of zero: two.

We can add two values of type `Nat` as follows:

```scala
def add1(n: Nat, m: Nat): Nat =
  n match
    case Z => m
    case S(n1) => S(add1(n1, m))
```

However, this definition has an issue: the result is imprecisely typed:

```scala
add1(S(Z), S(Z))
// res1: Nat = S(S(Z))
```

To fix this, we will define the `Sum[N, M, R]` type class. An instance of Sum represents evidence that `N + M = R`.

With the appropriate given definitions, the compiler can infer an instance of `Sum[N, M, R]` such that `N + M = R`, for any pair of natural numbers `N` and `M`:

```scala
case class Sum[N <: Nat, M <: Nat, R <: Nat](result: R)

given zero: Z = Z
given succ[N <: Nat](using n: N): S[N] = S(n)

given sumZ[N <: Nat](using n: N): Sum[Z, N, N] = Sum(n)

given sumS[N <: Nat, M <: Nat, R <: Nat](
  using sum: Sum[N, M, R]
): Sum[S[N], M, S[R]] = Sum(S(sum.result))
```

Note how the last two `given` definitions reflect the definition of the `add1` method: `sumZ` corresponds to the case for `Z + M`, and `sumS` corresponds to the case for `S[N] + M`. The second case is recursive, explicitly for `add1` and using term inference for the `given` definition.

Using the above definitions, we can write a function that adds two `Nat`
values and assigns a precise type to the result:

```scala
def add[N <: Nat, M <: Nat, R <: Nat](n: N, m: M)(
  using sum: Sum[N, M, R]
): R = sum.result
```

As an example, the result of adding `S(Z)` to `S(Z)` is now precisely typed as `S[S[Z]]`:

```scala
add(S(Z), S(Z))
// res2: S[S[Z]] = S(S(Z))
```

### Question 2.1

Write explicitly the `using` argument to `sum` (it may help to write all type parameters explicitly):

```scala
add(S(Z), S(S(Z)))(using ...)
```

*Note:* If you try this in the Scala 3 compiler right now, the result won't be correct,
this is a known compiler bug: https://github.com/lampepfl/dotty/issues/7586.

### Question 2.2

Write `given` definitions that create an instance of the
`Product[N, M, R]` type class, representing the evidence that `N * M = R`.

```scala
case class Product[N <: Nat, M <: Nat, R <: Nat](result: R)
```

Hint 1: Multiplying two natural numbers is defined as follows:

```scala
def mul1(n: Nat, m: Nat): Nat =
  n match
    case Z => Z
    case S(n1) => add1(m, mul1(n1, m))
```

Hint 2: The `R` type parameter is key to making the `add` definition work.
When we start inferring a term of type `Sum[N, M, R]`, we already know what
`N` and `M` are, but we don't know what `R` is. Inferring the term will also
infer the type that stands for `R`.
