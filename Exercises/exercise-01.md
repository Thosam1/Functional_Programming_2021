# Exercise Session 1

We will work on tail recursion in this session.

## Question 1: Factorial

Recall the factorial function that you saw in class

```scala
def factorial(n: Int): Int = if (n <= 0) 1 else n * factorial(n - 1)
```

Define a tail recursive version of it

```scala
def factorial(n: Int): Int = fact(n, 1)
@tailrec
def fact(n: Int, acc: Int): Int = ???
```

What would be the advantage of making `fact` an inner function to `factorial`?

## Question 2: Sum of elements on a list

Define a function that takes a list of integers and sums them. You can use the functions `head`, `tail`, and `isEmpty` on lists, as you have seen for your homework.

```scala
def sumList(ls: List[Int]): Int = ???
```

Convert your definition into a tail-recursive one.

## Question 3: Fast exponentiation

Fast exponentiation is a technique to optimize the exponentiation of numbers:

```
b²ⁿ = (b²)ⁿ = (bⁿ)²
b²ⁿ⁺¹ = b * b²ⁿ
```

Define a function that implements this fast exponentiation. Can you define a tail recursive version as well?

```scala
def fastExp(base: Int, exp: Int): Int = ???
```

## Question 4: Tail recursive Fibonacci

Define a function that computes the nth Fibonacci number. Can you define a tail recursive version as well? The Fibonacci recurrence is given as follows:

```
fib(n) = n | n = 0, 1
fib(n) = fib(n - 1) + fib(n - 2) | otherwise
```

```scala
def fibonacci(n: Int): Int = ???
```
