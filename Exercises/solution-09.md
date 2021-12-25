# Exercise Session 9, Solutions

## Question 1

```scala
def groupBy[T, S](f: T => S)(xs: List[T]): Map[S, List[T]] =
    var acc = Map[S, List[T]]()
    xs.reverse.foreach(el =>
        val key = f(el)
        val prevValue = acc.getOrElse(key, List())
        acc = acc.updated(key, el :: prevValue)
    )
    acc
```

```scala
def groupBy2[T, S](f: T => S)(xs: List[T]): Map[S, List[T]] =
    xs.foldRight(Map[S, List[T]]())((el: T, acc: Map[S, List[T]]) =>
        val key = f(el)
        val prevValue = acc.getOrElse(key, List())
        acc.updated(key, el :: prevValue)
    )
```

Example run:

```scala
groupBy[Int, Int](_ % 3)((0 to 10).toList)
// res0: Map[Int, List[Int]] = Map(
//   1 -> List(1, 4, 7, 10),
//   0 -> List(0, 3, 6, 9),
//   2 -> List(2, 5, 8)
// )
```

## Question 2

### Question 2.1


```scala
class LoggerBuffered extends Logger:
    private var output: String = ""
    def log(message: String): Unit = output = output + message + "\n"
    def getOutput(): String = output
```

### Question 2.2


```scala
def evalTracing(e: Expr, context: Map[Var, Int])(using logger: Logger): Int = e match 
    case sym: Var if context.contains(sym) =>
        val value = context(sym)
        logger.log(f"Get var ${sym.name} = ${value} from the context.\n")
        value
    case sym: Var => throw UnknownVarException(sym.name)
    case Op(name, args) =>
        val evaluatedArgs= args.map(evalTracing(_, context))
        logger.log(f"Apply operation ${name} to arguments ${evaluatedArgs.mkString(",")}")
        name match
            case "*" => evaluatedArgs.foldLeft(1)(_ * _)
            case "+" => evaluatedArgs.foldLeft(0)(_ + _)
            case _   => throw UnknownOpException(name)
```