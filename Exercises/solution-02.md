# Exercise Session 2, Solutions

## Question 1

```scala
def flip(f: (Double, Int) => Boolean): (Int, Double) => Boolean =
  (x1: Int, x2: Double) => f(x2, x1)
```

## Question 2

### Question 2.1

```scala
val id: Int => Int = (x: Int) => x
```

### Question 2.2

```scala
def compose(f: Int => Int, g: Int => Int): Int => Int =
  (x: Int) => f(g(x))
```

`compose(id, f)(k)` evaluates to `f(k)`, as does `compose(f, id)(k)`.

More generally (see ["Identity Function" on Wikipedia](https://en.wikipedia.org/wiki/Identity_function#Algebraic_properties)):

> If f: M → N is any function, then we have f ∘ id<sub>M</sub> = f = id<sub>N</sub> ∘ f.

### Question 2.3

```scala
def repeated(f: Int => Int, n: Int): Int => Int =
  if n == 0 then identity
  else compose(f, repeated(f, n-1))
```

## Question 3

### Question 3.1

```scala
def curry2(f: (Double, Int) => Boolean): Double => (Int => Boolean) =
  (x1: Double) => (x2: Int) => f(x1, x2)
```

### Question 3.2

```scala
def uncurry2(f: Double => Int => Boolean): (Double, Int) => Boolean =
  (x1: Double, x2: Int) => f(x1)(x2)
```

## Question 4

```scala
import scala.annotation.tailrec

def fixedPoint(f: Int => Int): Int => Int =
  @tailrec
  def loop(guess: Int): Int =
    val image: Int = f(guess)
    if image == guess then guess else loop(image)
  loop
```

Or alternatively, using currying:

```scala
def fixedPointCurry(f: Int => Int)(guess: Int): Int =
  val image: Int = f(guess)
  if image == guess then guess else fixedPoint(f)(image)
```

- `fixedPoint(x => x/2)(4)` returns `0`.
- `fixedPoint(id)(123456)` returns `123456`.
- `fixedPoint(x => x + 1)(0)` does not terminate.
- `fixedPoint(x => if (x % 10 == 0) x else x + 1)(35)` returns `40`.
- `fixedPoint(x => x / 2 + 5)(20)` returns `10`.

## Question 5

### Question 5.1

As seen in [the slides](https://gitlab.epfl.ch/lamp/cs210/-/blob/master/slides/progfun1-2-2.pdf):

```scala
def sum(f: Int => Int)(a: Int, b: Int): Int =
  if a > b then 0 else f(a) + sum(f)(a + 1, b)
```

Tail-recursive version:

```scala
import scala.annotation.tailrec

def sumTailRec(f: Int => Int)(a: Int, b: Int): Int =
  @tailrec
  def loop(i: Int, acc: Int): Int =
    if i > b then acc else loop(i + 1, acc + f(i))
  
  loop(a, 0)
```

### Question 5.2

```scala
def quadratic(c: Int): Int => Int =
  (x: Int) => (x - c) * (x - c)
```

### Question 5.3

```scala
val quad3Integrate: (Int, Int) => Int =
  sum(quadratic(3))
```
