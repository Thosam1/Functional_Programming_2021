# Exercise Session 3, Solutions

## Question 1

```scala
def eq[T](a: T, b: T, leq: (T, T) => Boolean) = leq(a, b) && leq(b, a)
def le[T](a: T, b: T, leq: (T, T) => Boolean) = !leq(b, a)
```

## Question 2

Using pattern matching:

```scala
trait Tree[T]:
  def size: Int = this match
    case EmptyTree(_) => 0
    case Node(left, _, right, _) => left.size + 1 + right.size

case class EmptyTree[T](leq: (T, T) => Boolean) extends Tree[T]
case class Node[T](left: Tree[T], elem: T, right: Tree[T], leq: (T, T) => Boolean) extends Tree[T]
```

Defining a method per subclass:

```scala
trait Tree[T]:
  def size: Int

case class EmptyTree[T](leq: (T, T) => Boolean) extends Tree[T]:
  def size: Int = 0

case class Node[T](left: Tree[T], elem: T, right: Tree[T], leq: (T, T) => Boolean) extends Tree[T]:
  def size: Int = left.size + 1 + right.size

```

## Question 3

```scala
def add(newElem: T): Tree[T] = this match
  case EmptyTree(leq) =>
    Node(EmptyTree(leq), newElem, EmptyTree(leq), leq)
  case Node(left, elem, right, leq) =>
    if leq(newElem, elem) then
      if leq(elem, newElem) then this
      else Node(left.add(newElem), elem, right, leq)
    else Node(left, elem, right.add(newElem), leq)
```

## Question 4

```scala
def toList: List[T] =
  def toListAcc(tree: Tree[T], acc: List[T]): List[T] = tree match
    case EmptyTree(_) => acc
    case Node(left, elem, right, _) =>
      toListAcc(left, elem :: toListAcc(right, acc))

  toListAcc(this, Nil)
```

Without using an accumulator:

```scala
def toListNoAcc: List[T] = this match
  case EmptyTree(_) => Nil
  case Node(left, elem, right, _) =>
    (left.toList :+ elem) ++ right.toList
    // shortand for left.toList.appended(elem).concat(right.toList)
```

## Question 5

```scala
def sortedList[T](leq: (T, T) => Boolean, ls: List[T]): List[T] =
  def buildTree(elems: List[T], acc: Tree[T]): Tree[T] = elems match
    case Nil => acc
    case elem :: rest => buildTree(rest, acc.add(elem))

  buildTree(ls, EmptyTree(leq)).toList
```

## Question 6

```scala
enum Tree[T]:
  def size: Int = /* ... */
  def add(newElem: T): Tree[T] = /* ... */
  def toList: List[T] = /* ... */
  
  case EmptyTree(leq: (T, T) => Boolean) 
  case Node(left: Tree[T], elem: T, right: Tree[T], leq: (T, T) => Boolean)
```