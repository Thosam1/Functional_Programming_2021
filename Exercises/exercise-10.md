# Exercise Session 10

## Question 1

For this exercise, you are given the following ASTs representing an expression `Expr`

```scala
enum BinOpName:
  case Minus
  case Plus
  case Times
  case LessEq
  case Modulo

enum Expr:
  case Constant(value: Int) // Numeric constants
  case Name(name: String) // reference to a name
  case BinOp(op: BinOpName, arg1: Expr, arg2: Expr) // primitive binary operation
  case IfNonzero(cond: Expr, caseTrue: Expr, caseFalse: Expr) // conditional
  case Call(function: Expr, arg: Expr) // function call
  case Fun(param: String, body: Expr) // function definition // function definition

import BinOpName._
import Expr._
```

We also provide a few valid primitive operations that you may use

```scala
def minus(e1: Expr, e2: Expr) = BinOp(Minus, e1, e2)
def plus(e1: Expr, e2: Expr) = BinOp(Plus, e1, e2)
def leq(e1: Expr, e2: Expr) = BinOp(LessEq, e1, e2) // 1 if e1 <= e2; 0 otherwise // 1 if e1 <= e2; 0 otherwise
def times(e1: Expr, e2: Expr) = BinOp(Times, e1, e2)
def modulo(e1: Expr, e2: Expr) = BinOp(Modulo, e1, e2)
```

The global environment is a sequence of `"name"  -> definition` tuples (of type `(String, Expr)`).
All definitions can reference all names in the global environment.

Where for example one could define a division function `div` as follows:

```scala
"div" -> Fun("x", Fun("y",
  IfNonzero(BinOp(LessEq, Name("y"), Name("x")),
    plus(Constant(1),
      Call(Call(Name("div"), minus(Name("x"), Name("y"))),
      Name("y"))),
    Constant(0))))
```


Your task is to implement the greatest common divisor (`gcd`) function in `Expr`.

```scala
"gcd" -> ??? // TODO implement me
```

Hint: in Scala the `gcd` can be implemented as
```scala
def gcd(a: Int, b: Int): Int = if b == 0 then a else gcd(b, a%b)
```

## Question 2

For this exercise, we will add lists to our language

```scala
enum Expr:
  ...
  case Cons(head: Expr, tail: Expr) // Cons list
  case EmptyList // empty of a Cons list
  // matches a list
  case Match(scrutinee: Expr, caseEmpty: Expr, headName: String, tailName: String, caseCons: Expr)
```

For example, the following program a match in Scala would translate as

```scala
ls match
  case Nil => Nil
  case x :: xs => x :: xs
// becomes
Match(Name("ls"),
  EmptyList,
  "x", "xs", Cons(Name("x"), Name("xs"))
)
```


### Map
Your task is to implement the `map` function in `Expr`.

Hint:
```scala
def map(ls: List[Int])(f: Int => Int): List[Int] = ls match
  case Nil => Nil
  case x :: xs => f(x) :: map(xs)(f)
```


### Fold Left
Your task is to implement the `foldLeft` function in `Expr`.
Hint:
```scala
def foldLeft(ls: List[Int])(acc: Int)(f: (Int, Int) => Int): Int = ls match
  case Nil => acc
  case x :: xs => foldLeft(xs)(f(acc, x))(f)
```

## Question 3

For this exercise, we will add writable cells to our language. Assume we have a global array of memory that can be accessed by index.
```scala
enum Expr:
  ...
  // read from position `idx`
  case Read(idx: Expr)
  // write the `value` to position `idx` and then evaluates and return the `andThen` expression
  case Write(idx: Expr, value: Expr, andThen: Expr)
end Expr
```


Your task is to implement the `CAS` (compare and swap) function in `Expr`.

Hint:
```scala
val mem: Array[Int] = ???
def CAS(idx: Int)(old: Int)(nw: Int): Int =
  if mem(idx) != old then
    0
  else
    mem(idx) = nw
    1
```
