# Exercise Session 9

## Question 1

Implement a function `groupBy` by yourself, without using the standard `groupBy` method:

```scala
def groupBy[T, S](f: T => S)(xs: List[T]): Map[S, List[T]] = ???
```

Write two versions:

- One using a mutable `var` and a `foreach` loop.
- One using `foldRight`.

## Question 2

### Question 2.1

When running code, it can be useful to print what is going on for debugging. In order to do so, let us define a trait `Logger`:

```scala
trait Logger:
    def log(message: String): Unit
```

Implement a `LoggerBuffered` what will accumulate messages in a private `buffer` mutable field.

```scala
class LoggerBuffered extends Logger:
    def log(message: String): Unit = ???
    def getOutput(): String = ???
```

### Question 2.2

Remember the `eval` function from the midterm exam. A possible implementation would be the following:

```scala
enum Expr:
  case Var(name: String)
  case Op(name: String, args: List[Expr])

import Expr._

final case class UnknownVarException(name: String) extends Exception
final case class UnknownOpException(name: String) extends Exception

def eval(e: Expr, context: Map[Var, Int]): Int = e match 
    case sym: Var if context.contains(sym) => context(sym)
    case sym: Var => throw UnknownVarException(sym.name)
    case Op("*", args) => args.map(eval(_, context)).foldLeft(1)(_ * _)
    case Op("+", args) => args.map(eval(_, context)).foldLeft(0)(_ + _)
    case Op(name, _) => throw UnknownOpException(name)
```

Write a new version that will log the evaluation steps to a logger implicitly passed as argument:


```scala
def evalTracing(e: Expr, context: Map[Var, Int])(using logger: Logger): Int = ???
```

When reading a value, it should log `Get var [name] = [value] from the context`. When applying an operation, it should log `Apply operation [name] to arguments [args]`, where `[args]` is a comma-separated list of evaluated arguments.


Example run:

```scala
given logger: LoggerBuffered = LoggerBuffered()

evalTracing(
    Op("*", List(Var("x"), Op("+", List(Var("y"), Var("z"))))),
    Map(Var("x") -> 2, Var("y") -> 3, Var("z") -> 4)
)
// res3: Int = 14

logger.getOutput()
// res4: String = """Get var x = 2 from the context.\n
// Get var y = 3 from the context.\n
// Get var z = 4 from the context.\n
// Apply operation + to arguments 3,4
// Apply operation * to arguments 2,7
// """
```