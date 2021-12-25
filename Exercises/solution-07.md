# Exercise Session 7, Solutions

## Question 1

1.

```scala
1 3 1 1 2 2 2 1
```

2.

```scala
def nextLine(currentLine: List[Int]): List[Int] = {
  currentLine.foldLeft(List.empty[Int]) { (acc, x) =>
    acc match {
      case y :: count :: rest if x == y => x :: (count + 1) :: rest
      case _ => x :: 1 :: acc
    }
  }.reverse
}
```

3.

```scala
lazy val funSeq: LazyList[List[Int]] =
  LazyList.cons(List(1), funSeq.map(nextLine))
```

## Question 2

1.

```scala
val squares: LazyList[Int] = LazyList.from(1).map(x => x * x)
```

2.

```scala
lazy val codes: LazyList[String] = "0" #:: "1" #:: codes.flatMap {
  (s: String) => (s + "0") #:: (s + "1") #:: LazyList.empty[String]
}
```

3.

```scala
def isPalindrome(x: String): Boolean = x.reverse == x
val palCodes: LazyList[String] = codes.filter(isPalindrome)
```

4.

```scala
val palCodes: LazyList[String] = for {
  c <- codes
  middle <- Seq("", "0", "1")
} yield c + middle + c.reverse
```

5.

```scala
def interleave[A](xs: LazyList[A], ys: LazyList[A]): LazyList[A] =
  (xs, ys) match {
    case (x #:: xr, y #:: yr) => x #:: y #:: interleave(xr, yr)
    case (LazyList.Empty, _) => ys
    case (_, LazyList.Empty) => xs
  }

val allCodes = interleave(palCodes, otherCodes)
```
