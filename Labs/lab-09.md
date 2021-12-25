# Lab 9: Recursive Language (interpreter)

You can use the following commands to make a fresh clone of your repository:

```shell
git clone -b interpreter git@gitlab.epfl.ch:lamp/students-repositories-fall-2021/cs210-GASPAR.git cs210-interpreter
cd cs210-interpreter
```

You can always refer to:
  * [the example guide](https://gitlab.epfl.ch/lamp/cs210/blob/master/labs/example-lab.md) on the development workflow.
  * [this guide](https://gitlab.epfl.ch/lamp/cs210/blob/master/labs/grading-and-submission.md) for details on the submission system.
    **Make sure to submit your assignment before the deadline written in [README.md](/README.md)**
  * [The documentation of the Scala standard library](https://www.scala-lang.org/files/archive/api/2.13.3)
  * [The documentation of the Java standard
    library](https://docs.oracle.com/en/java/javase/15/docs/api/index.html)


## Overview

The goal of this final assignment is to extend the simple programming language presented in the lecture with a list-like datatype. Recall the datatype of expressions:

~~~scala
enum Expr:
  case Constant(value: Int)
  case Name(name: String)
  case BinOp(op: BinOps, arg1: Expr, arg2: Expr)
  case IfNonzero(cond: Expr, caseTrue: Expr, caseFalse: Expr)
  case Call(function: Expr, arg: Expr)
  case Fun(param: String, body: Expr)
~~~

We extend this datatype with 3 extra cases:

~~~scala
enum Expr:
  [...]
  case Empty
  case Cons(head: Expr, tail: Expr)
  case Match(scrutinee: Expr, caseEmpty: Expr,
             headName: String, tailName: String, caseCons: Expr)
~~~

`Empty` corresponds to the empty list, also known as `Nil` in Scala. `Cons` *cons*-tructs memory objects holding two values, called head and tail. It is our language analog to Scala's `::`. Finally, `Match` is a pattern matching expression over lists. It takes 5 arguments:

- `scrutinee`, the expression that's being matched over
- `caseEmpty `, the result of the pattern matching when `scrutinee` evaluates to `Empty`
- `caseCons`, the result of the pattern matching when `scrutinee` evaluates to `Cons`
- `headName` and `tailName` are names that *bind* the two values of `Cons` in `caseCons`.

`Match` can be also explained by analogy to Scala's pattern matching. The 5 arguments described above correspond to the placeholders in the following expression:

~~~scala
$scrutinee match
  case Nil => $caseEmpty
  case $headName :: $tailName => $caseCons
~~~

## Running the interpreter

You can use (and update!) the `main` method at the end of the `RecursiveLanguage` object to execute programs using the interpreter (call `run` from sbt to run the program). To execute programs, use eval (or tracingEval, the debugging with logging enabled). These functions takes two arguments, the expression that's being interpreted, and a set of top-level definitions given to the interpreter.

## Examples functions

Once you completed the recitation session, transcribe your implementations of `gcd`, `map` and `foldLeft` into the lab. These definitions should go into `val definitions` at the end of `RecursiveLanguage.scala`. You can see your `gcd` implementation in action by changing the body of the `main` method to:

~~~scala
def main(args: Array[String]): Unit =
  tracingEval(Call(Call(N("gcd"), C(6)), C(9)), definitions)
~~~

Execute the interpreter using `run` from sbt. With the reference implementation, the trace looks as follows:

~~~
gcd(6)(9)
|  gcd(6)
|  FUN: (a => (b => (if b then gcd(b)((% a b)) else a)))  ARG: 6
|  (b => (if b then gcd(b)((% 6 b)) else 6))
|  +--> (b => (if b then gcd(b)((% 6 b)) else 6))
[...]
+--> 3
 ~~> Constant(3)
~~~

A correct implementation of `gcd` should already give you a passing test (it doesn't use any list constructs!). Can will be able to play around with your `map` and `foldLeft` implementations once you complete the assignment.

Update the `show` function to support pretty-printing of lists. We suggest (but do not test) that you mirror the Scala syntax for list constructors (`1 :: 2 :: 3 :: Nil`) and for pattern matching.

Also implement `foldRight`. In the reference solution, it pretty prints as follows:

~~~scala
def foldRight =
  (ls => (z => (fold => ls match { case nil => z; case x :: xs => fold(x)(foldRight(xs)(z)(fold)) })))
~~~

You might have noticed while compiling the project the presence of several warnings about pattern matching exhaustivity. These warnings appeared when we extended `enum Expr`, and should be all gone once you completed your implementation.

## Evaluation

Update the `eval` function to support `Empty`, `Cons` and `Match`. `Empty` should evaluate to itself. Likewise, `Cons` should evaluate to a `Cons` with evaluated arguments. `Match` should first evaluate its `scrutinee`, then either reduce to `caseEmpty`, reduce to `caseCons` or raise an error if the `scrutinee` is not a list.

Adding cases in the `eval` function should be sufficient to evaluate `Match` expressions in isolation (see `evalTests` in `RecursiveLanguageSuite.scala`), but extra work is needed to support `Match` used into functions and nested `Match`-s.

### Substitution

Substitution is implemented using 3 functions, `subst`, `freeVars` and `alphaConvert`. That complexity is need to solve the variable capture problem, which we will explain in this section using an example. Suppose the following definitions for factorial and twice (in Scala):

~~~scala
val fact:
  Int => Int =
  n   => if n == 0 then 1 else n * fact(n - 1)

val twice:
  (Int => Int) => Int  => Int =
  f            => fact => f(f(fact))
~~~

Note the name conflict between the second argument of `twice` and the fact function. Let's evaluate `twice(fact)(3)` using naive substitution:

~~~scala
twice(fact)(3)
--> twice(n => if n == 0 then 1 else n * fact(n - 1))(3)
--> fact => f(f(fact)) [substitute f by n => if n == 0 then 1 else n * fact(n - 1)]
--> *BOOM*
~~~

The naive substitution would break with the example above because of the recursion in factorial. A simple syntactic replacement of `f` would *change* the meaning of `fact` from a reference to the `fact` function (the recursive call) to a reference to the second argument of twice.

To solve this problem, substitution needs to carefully avoid such accidental capture. To detect name clashes, we first compute the set of names "at-risk", which we call *free variables*. When substituting `f` by `n => if n == 0 then 1 else n * fact(n - 1)` the name at risk is `fact`. The variable `fact` is called a *free variable* in that example because it doesn't have local meaning, it's "free" when we consider that expression in isolation.

Whenever we detect a name clash, we can work around it by renaming the problematic variable. This operation is traditionally called alpha conversion. The correct version of substitution would evaluate `twice(fact)(3)` as follows:

~~~scala
twice(fact)(3)
--> twice(n => if n == 0 then 1 else n * fact(n - 1))(3)
--> (fact => f(f(fact)))(3)   [rename fact to fact', then substitute f by n => if n == 0 then 1 else n * fact(n - 1)]
--> (fact' => f(f(fact')))(3) [substitute f by n => if n == 0 then 1 else n * fact(n - 1)]
--> [...]
--> 720
~~~

Looking back at pattern matching expression, we can see that the two binders of a `Match` suffer from the same variable capturing problem than function. Indeed, given an expression of shape `(h :: t) match { case x :: xs => body }`, when we substitute `x` by `h` and `xs` by `t` in `body`, we need to avoid changing the meaning of free variables in `h` and `t`.

Update `freeVars` and `alphaConvert` to with cases for `Empty`, `Cons` and `Match`. Have a look at the corresponding unit tests, `freeVarsTests` and `alphaConvertTests` in `RecursiveLanguageSuite.scala`. Finally, update `subst` with capture avoiding substitution for pattern matching. Note that `subst` is only tested using integration tests, so make sure that you have all other tests passing before starting to implement that last function. Also note that this assignment doesn't have any black-box tests (no need to run `grading:test`, `test` will run all the tests).
