# Exercise Session 6

For comprehensions and monads

## Question 1.1

Consider a directed graph given by its set of (directed) edges stored as a list of pairs of nodes:

```scala
type NodeId = Int
type DirectedEdge = (NodeId, NodeId)
type DirectedGraph = List[DirectedEdge]
```

Define, non-recursively, the `triangles` function that finds all cycles of length 3, with three distinct nodes, in the given graph. You should use a for comprehension.

```scala
def triangles(edges: DirectedGraph): List[(NodeId, NodeId, NodeId)] = for ...
```

Each cycle should appear only once. For instance, given the edges:

```
List((1, 2), (2, 3), (3, 1))
```

You should return exactly one of the three following possibilities:

```
(1, 2, 3), (2, 3, 1), (3, 1, 2)
```

You are free to decide which of the three you return.


## Question 1.2

After that, translate the for comprehension you wrote in the appropriate combination of `map`/`flatMap`/`filter` calls.


## Question 2.

Monads are often defined with a `map` method in addition to `flatMap`:

```scala
trait M[T] {
  def flatMap[U](f: T => M[U]): M[U]
  def map[U](f: T => U): M[U]
}
```

Where `map` and `flatMap` are related by the following law:

```
Monad/Functor Consistency:
m.map(f) === m.flatMap(x => unit(f(x)))
```

We introduce you a new algebraic structure called `Functor`-s. We say that a `type F` is a `Functor` if `F[T]` has a `map` method with the following signature:

```scala
trait F[T] {
  def map[U](f: T => U): F[U]
}
```

And there is a `unit` method for `F` with the following signature:

```scala
def unit[T](x: T): F[T]
```

Such that `map` and `unit` fulfill the following laws:

```
Identity:
m.map(x => x) === m

Associativity:
m.map(h).map(g) === m.map(x => g(h(x)))
```

Prove that any `Monad` with a `map` method that fulfills the `Monad/Functor Consistency` law is also a `Functor`.
