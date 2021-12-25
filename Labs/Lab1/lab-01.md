# Lab 1: Recursion

**Before starting this lab, please complete the other [first week tasks](https://gitlab.epfl.ch/lamp/cs210#first-week-tasks)!**

# Setup

You can use the following commands to make a fresh clone of your repository:

```shell
git clone -b recfun git@gitlab.epfl.ch:lamp/students-repositories-fall-2021/cs210-GASPAR.git cs210-recfun
cd cs210-recfun
```

**If you encounter any issue with the IDE, please refer back to the
[Troubleshooting section of the example lab](example-lab.md#troubleshooting).**

You can always refer to:
  * [the example guide](https://gitlab.epfl.ch/lamp/cs210/blob/master/labs/example-lab.md) on the development workflow.
  * [this guide](https://gitlab.epfl.ch/lamp/cs210/blob/master/labs/grading-and-submission.md) for details on the submission system.
    **Make sure to submit your assignment before the deadline written in [README.md](/README.md)**
  * [The documentation of the Scala standard library](https://www.scala-lang.org/files/archive/api/2.13.3)
  * [The documentation of the Java standard
    library](https://docs.oracle.com/en/java/javase/15/docs/api/index.html)


# Be functional!

This course is about **functional** programming, where you'll learn to program
without using mutation, therefore you're not allowed to use the following
constructs in the labs:
- `var`
- `while`
- `return`
- Any class in the `scala.collection.mutable` package

# Exercise 1: Pascal's Triangle

The following pattern of numbers is called _Pascal's triangle_.

        1
       1 1
      1 2 1
     1 3 3 1
    1 4 6 4 1
       ...

The numbers at the edge of the triangle are all `1`, and each number
inside the triangle is the sum of the two numbers above it. Write a
function that computes the elements of Pascal's triangle by means of a
recursive process.

Do this exercise by implementing the `pascal` function in
`RecFun.scala`, which takes a column `c` and a row `r`, counting from
`0` and returns the number at that spot in the triangle. For example,
`pascal(0,2)=1`, `pascal(1,2)=2` and `pascal(1,3)=3`.

```scala
def pascal(c: Int, r: Int): Int
```

# Exercise 2: Parentheses Balancing

Write a recursive function which verifies the balancing of parentheses
in a string, which we represent as a `List[Char]` not a `String`. For
example, the function should return `true` for the following strings:

- (if (zero? x) max (/ 1 x))
- I told him (that it's not (yet) done).
  (But he wasn't listening)

The function should return `false` for the following strings:
<ul>
<li>:-)</li>
<li>())(</li>
</ul>

The last example shows that it's not enough to verify that a string
contains the same number of opening and closing parentheses.

Do this exercise by implementing the `balance` function in
`RecFun.scala`. Its signature is as follows:

```scala
def balance(chars: List[Char]): Boolean
```

There are three methods on `List[Char]` that are useful for this
exercise:

- `chars.isEmpty: Boolean` returns whether a list is empty
- `chars.head: Char` returns the first element of the list
- `chars.tail: List[Char]` returns the list without the first element

You can find more information on these methods in the [documentation of List](https://www.scala-lang.org/files/archive/api/2.13.3/scala/collection/immutable/List.html)

__Hint__: you can define an inner function if you need to pass extra
parameters to your function.

__Testing__: You can use the `toList` method to convert from a
`String` to a `List[Char]`: e.g. `"(just an) example".toList`.

# Exercise 3: Counting Change

Write a recursive function that counts how many different ways you can
make change for an amount, given a list of coin denominations. For
example, there are 3 ways to give change for 4 if you have coins with
denomiation 1 and 2: 1+1+1+1, 1+1+2, 2+2.

Do this exercise by implementing the `countChange` function in
`RecFun.scala`. This function takes an amount to change, and a list of
unique denominations for the coins. Its signature is as follows:

```scala
def countChange(money: Int, coins: List[Int]): Int
```

Once again, you can make use of functions `isEmpty`, `head` and `tail`
on the list of integers `coins`.

__Hint__: Think of the degenerate cases. How many ways can you give
change for 0 CHF? How many ways can you give change for >0 CHF, if you
have no coins?

# Submission

[When you're done, don't forget to submit your assignment!](grading-and-submission.md)
