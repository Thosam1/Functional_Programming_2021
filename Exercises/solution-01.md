# Exercise Session 1, Solutions

## Question 1: Factorial

```scala
import scala.annotation.tailrec

def factorial(n: Int) =
  require(n >= 0)

  @tailrec
  def loop(n: Int, acc: Int): Int =
    if n == 0 then acc
    else loop(n - 1, acc * n)
  
  loop(n, 1)
```

## Question 2: Sum of elements on a list

```scala
import scala.annotation.tailrec

def sumList(ls: List[Int]) =
  @tailrec
  def loop(ls: List[Int], acc: Int): Int =
    if ls.isEmpty then acc
    else loop(ls.tail, acc + ls.head)
  
  loop(ls, 0)
```

## Question 3: Fast exponentiation

```scala
import scala.annotation.tailrec

def fastExp(base: Int, exp: Int): Int =
  require(exp >= 0)

  @tailrec
  def loop(base: Int, exp: Int, acc: Int): Int =
    if exp == 0 then
      acc
    else if (exp % 2) != 0 then
      loop(base, exp - 1, base * acc)
    else
      loop(base * base, exp / 2, acc)

  loop(base, exp, 1)
```

## Question 4: Tail recursive Fibonacci

```scala
import scala.annotation.tailrec

def fibonacci(n: Int): Int =
  require(n >= 0)

  @tailrec
  def loop(k: Int, previous: Int, current: Int): Int =
    if k == n then current
    else loop(k + 1, current, previous + current)

  if n == 0 then 0 else loop(1, 0, 1)
```
