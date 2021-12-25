# 2021 Midterm, Solutions

## Exercise 1

```scala
trait Tree[T]:
    def height: Int = this match
        case EmptyTree(_) => 0
        case Node(left, _, right, _) => Math.max(left.height, right.height) + 1
    
    def isBalanced: Boolean = this match
        case EmptyTree(_) => true
        case Node(left, _, right, _) => left.isBalanced && right.isBalanced && Math.abs(left.height - right.height) <= 1

case class EmptyTree[T](leq: (T, T) => Boolean) extends Tree[T]
case class Node[T](left: Tree[T], elem: T, right: Tree[T], leq: (T, T) => Boolean)
extends Tree[T]
```

<details>

<summary>Tests</summary>

```scala
def intLeq(a: Int, b: Int) = a <= b

val exTree1 = Node(
    Node(
        Node(EmptyTree(intLeq), 1, EmptyTree(intLeq), intLeq),
        2,
        Node(EmptyTree(intLeq), 3, EmptyTree(intLeq), intLeq),
        intLeq
    ),
    5,
    Node(
        Node(EmptyTree(intLeq), 7, EmptyTree(intLeq), intLeq),
        9,
        Node(
            Node(EmptyTree(intLeq), 10, EmptyTree(intLeq), intLeq),
            15,
            Node(EmptyTree(intLeq), 25, EmptyTree(intLeq), intLeq),
            intLeq
        ),
        intLeq
    ),
    intLeq
)

val exTree2 = Node(
    Node(
        Node(
            Node(EmptyTree(intLeq), 25, EmptyTree(intLeq), intLeq),
            15,
            Node(EmptyTree(intLeq), 10, EmptyTree(intLeq), intLeq),
            intLeq
        ),
        9,
        Node(EmptyTree(intLeq), 7, EmptyTree(intLeq), intLeq),
        intLeq
    ),
    5,
    Node(
        Node(EmptyTree(intLeq), 3, EmptyTree(intLeq), intLeq),
        2,
        Node(EmptyTree(intLeq), 1, EmptyTree(intLeq), intLeq),
        intLeq
    ),
    intLeq
)

val tree3 = Node(
    Node(
        Node(
            Node(EmptyTree(intLeq), 25, EmptyTree(intLeq), intLeq),
            15,
            Node(EmptyTree(intLeq), 10, EmptyTree(intLeq), intLeq),
            intLeq
        ),
        9,
        Node(EmptyTree(intLeq), 7, EmptyTree(intLeq), intLeq),
        intLeq
    ),
    5,
    EmptyTree(intLeq),
    intLeq
)

assert(exTree1.height == 4)
assert(exTree2.height == 4)
assert(EmptyTree(intLeq).height == 0)
assert(exTree1.isBalanced)
assert(exTree2.isBalanced)
assert(!tree3.isBalanced)
```

</details>

## Exercise 2

```scala
enum Expr:
  case Var(name: String)
  case Op(name: String, args: List[Expr])

import Expr._

final case class UnknownVarException(name: String) extends Exception
final case class UnknownOpException(name: String) extends Exception
```

There was multiple correct solutions. Here two possible solutions:

```scala
def eval1(e: Expr, context: Map[Var, Int]): Int = e match 
    case Var(name) => context.get(Var(name)) match
        case Some(n) => n
        case _ => throw UnknownVarException(name)
    case Op(name, args) =>
        val nargs = args.map(eval1(_, context))
        name match
            case "*" => nargs.foldLeft(1)(_ * _)
            case "+" => nargs.foldLeft(0)(_ + _)
            case _ => throw UnknownOpException(name)

def eval2(e: Expr, context: Map[Var, Int]): Int = e match 
    case sym: Var if context.contains(sym) => context(sym)
    case sym: Var => throw UnknownVarException(sym.name)
    case Op("*", args) => args.foldLeft(1)(_ * eval2(_, context))
    case Op("+", args) => args.foldLeft(0)(_ + eval2(_, context))
    case Op(name, _) => throw UnknownOpException(name)
```

**Comments**
- A lot of you assumed that the arguments of `Op`s can only be `Var`s, but these can be `Op`s as well as in `Op("*", List(Op("+", List(Var("x"), Var("x"))), Var("y")))`, so the function needs to call itself _recursively_ for each operation argument.
- Minor: `Map[Var, Int]` maps `Var` to `Int`s, so the argument passed to `context.get` must be a `Var`, not a `String`.

<details>

<summary>Tests</summary>

```scala
for eval <- Seq(eval1, eval2) do
    assert(eval(Op("+", List()), Map()) == 0)
    assert(eval(Op("+", List(Var("x"))), Map(Var("x") -> 2)) == 2)
    assert(eval(Op("+", List(Var("x"), Var("y"))), Map(Var("x") -> 2, Var("y") -> 3)) == 5)
    assert(eval(Op("*", List()), Map()) == 1)
    assert(eval(Op("*", List(Var("x"))), Map(Var("x") -> 2)) == 2)
    assert(eval(Op("*", List(Var("x"), Var("y"))), Map(Var("x") -> 2, Var("y") -> 3)) == 6)
    assert(eval(Op("*", List(Op("+", List(Var("x"), Var("x"))), Var("y"))), Map(Var("x") -> 2, Var("y") -> 3)) == 12)
```

</details>

## Exercise 3

### Exercise 3.1

- Type parameter `A` in `map`
- `def map[B >: A](f: B => C): Transform[A, C]`

    <details>
    <summary>Full code</summary>

    ```scala mdoc
    trait Transform[-A, +B]:
        def apply(x: A): B
        def map[C](f: B => C): Transform[A, C]
        def followedBy[C](t: Transform[B, C]): Transform[A, C]
    ```

    </details>

### Exercise 3.2

```
Is the following subtype assertion true?                yes    no

List[Triangle]            <:  List[AnyRef]              [X]    [ ]
List[Shape]               <:  List[AnyVal]              [ ]    [X]
List[String]              <:  List[Shape]               [ ]    [X]
Transform[Point, Circle]  <:  Transform[Point, Any]     [X]    [ ]
Transform[Point, Shape]   <:  Transform[Shape, Point]   [ ]    [X]
Transform[Shape, Point]   <:  Transform[Point, Shape]   [X]    [ ]
```

## Exercise 4

Axioms:

```
(1) Nil.reverse === Nil

(2) (x::xs).reverse === xs.reverse ++ (x::Nil)

(3) (xs++ys)++zs === xs++(ys++zs)

(4) Nil map f === Nil

(5) (x::xs) map f === f(x) :: (xs map f)

(6) Nil ++ ys === ys

(7) xs ++ Nil === xs

(8) x::xs ++ ys === x::(xs ++ ys)

(9) (l1 ++ l2).map(f) === l1.map(f) ++ l2.map(f)
```

### Exercise 4.1


```
We must prove:

(10) (l1 ++ l2).reverse === l2.reverse ++ l1.reverse


By induction on l1:

- l1 is Nil:

        (Nil ++ l2).reverse ===
    (6) l2.reverse

        l2.reverse ++ Nil.reverse ===
    (1) l2.reverse ++ Nil ===
    (7) l2.reverse

- l1 is x::xs

    IH: (xs ++ l2).reverse === l2.reverse ++ xs.reverse

         (x::xs ++ l2).reverse ===
    (8)  (x::(xs ++ l2)).reverse ===
    (2)  (xs ++ l2).reverse ++ (x::Nil) ===
    (IH) l2.reverse ++ xs.reverse ++ x::Nil ===
    (3)  l2.reverse ++ (xs.reverse ++ x::Nil) ===
    (2)  l2.reverse ++ (x::xs).reverse ===
         l2.reverse ++ l1.reverse
```

### Exercise 4.2

```
We must prove:

(l1 ++ l2).reverse.map(f) === l2.reverse.map(f) ++ l1.reverse.map(f)


Direct proof using axiom 10 proved in 4.1:

     (l1 ++ l2).reverse.map(f) =
(10) (l2.reverse ++ l1.reverse).map(f) =
(9)  l2.reverse.map(f) ++ l1.reverse.map(f)


Or without using axiom 10:

By induction on l1:

- l1 is Nil:

        ((l1 ++ l2).reverse).map(f) ===
    (6) l2.reverse.map.(f)

        l2.reverse.map(f) ++ l1.reverse.map(f) ===
    (1) l2.reverse.map(f) ++ Nil.map(f) ===
    (4) l2.reverse.map(f) ++ Nil ===
    (7) l2.reverse.map(f)


- l1 is x::xs

    IH: ((xs ++ l2).reverse).map(f) === l2.reverse.map(f) ++ xs.reverse.map(f)

         ((x::xs) ++ l2).reverse.map(f) ===
    (8)  (x::(xs ++ l2)).reverse.map(f) ===
    (2)  ((xs ++ l2).reverse ++ (x::Nil)).map(f) ===
    (9)  ((xs ++ l2).reverse.map(f)) ++ ((x::Nil).map(f)) ===
    (IH) l2.reverse.map(f) ++ xs.reverse.map(f) ++ (x::Nil).map(f) ===
    (3)  l2.reverse.map(f) ++ (xs.reverse.map(f) ++ (x::Nil).map(f)) ===
    (9)  l2.reverse.map(f) ++ (xs.reverse ++ x::Nil).map(f)) ===
    (2)  l2.reverse.map(f) ++ ((x::xs).reverse.map(f)) ===
```

## Exercise 5

### Exercise 5.1

There was multiple correct solutions:

```scala
def takeWhileStrictlyIncreasing1(list: List[Int]): List[Int] = list match
    case Nil => Nil
    case x :: Nil => list
    case x1 :: x2 :: xs => if x1 < x2 then x1 :: takeWhileStrictlyIncreasing1(x2 :: xs) else List(x1)

def takeWhileStrictlyIncreasing2(list: List[Int]): List[Int] = {
    def fun(ls: List[Int], last: Int): List[Int] = ls match
        case x :: xs if x > last => x :: fun(xs, x)
        case _ => Nil

    list match
        case x :: xs => x :: fun(xs, x)
        case Nil => Nil
}

def takeWhileStrictlyIncreasing3(list: List[Int]): List[Int] = {
    def fun(ls: List[Int], last: Option[Int]): List[Int] = (ls, last) match
        case (x :: xs, Some(last)) if x > last => x :: fun(xs, Some(x))
        case (x :: xs, None) => x :: fun(xs, Some(x))
        case _ => Nil

    fun(list, None)
}
```


**Comments**
- It was possible to write a correct implementation using foldLeft. Note that foldLeft traverses the whole list and cannot "early return" like a recursive call. This possible solution was not very clean as it required a flag or a condition to check that foldLeft should do nothing once it passed the first decreasing step. Avoid using foldLeft when you do not need to traverse the whole list every time.
- When we do not ask for a tail recursive implementation, the solution can be more readable without an accumulator. You should favor a simple and readable version.
- The solution `takeWhileStrictlyIncreasing3` uses `Option` and is elaborate. We include this solution for a good example of using `Option` but `takeWhileStrictlyIncreasing2` is simpler and more readable.

### Exercise 5.2

```scala
def increasingSequences(list: List[Int]): List[List[Int]] = list match
    case Nil => Nil
    case _ =>
        val prefix = takeWhileStrictlyIncreasing(list)
        prefix :: increasingSequences(list.drop(prefix.length))
```

**Comments**
- It is important to store `prefix` to avoid calling twice `takeWhileStrictlyIncreasing`.
- In the empty list case, some students made the confusion of returning a list containing the empty list. In this case, we have: `List[List[Int]]() == Nil != List(Nil) == List(List[Int]())`.