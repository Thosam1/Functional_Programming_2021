# Exercise Session 8, Solutions

## Question 1


### Question 1.1

```scala
given EqList[T](using Eq[T]): Eq[List[T]] with
  extension (x: List[T])
    def === (y: List[T]): Boolean =
      (x, y) match
        case (hx :: tx, hy :: ty) => hx === hy && tx === ty
        case (Nil, Nil) => true
        case _ => false
```

### Question 1.2

```scala
given EqTriple[T, U, S](using Eq[T], Eq[U], Eq[S]): Eq[(T, U, S)] with
  extension (x: (T, U, S))
    def === (y: (T, U, S)): Boolean =
      x._1 === y._1 && x._2 === y._2 && x._3 === y._3
```

### Question 1.3


```scala
given EqString: Eq[String] with
  extension (x: String)
    def === (y: String) = x == y

given EqInt: Eq[Int] with
  extension (x: Int)
    def === (y: Int) = x == y

given EqPerson(using Eq[(String, Int, List[String])]): Eq[Person] with
  extension (a: Person)
    def === (b: Person) =
      (a.name, a.age, a.neighbours) === (b.name, b.age, b.neighbours)
```

### Question 1.4

```scala
summon[Eq[Person]](using
  EqPerson(using
    EqTriple(using
      EqString,
      EqInt,
      EqList(using EqString)
    )
  )
)
```

## Question 2


### Question 2.1

```scala
add(S(Z), S(S(Z)))(using
  sumS(using
    sumZ(using
      succ(using
        succ(using
            zero
        )
      )
    )
  )
)
// res1: S[S[S[Z]]] = S(S(S(Z)))
```

*Note:* If you try to just write `add(S(Z), S(S(Z)))` without explictly specifying the implicit parameters in the Scala 3 compiler right now, you well get the wrong result. This is a known compiler bug: https://github.com/lampepfl/dotty/issues/7586.



### Question 2.2

```scala
given [N <: Nat]: Prod[Z, N, Z] = Prod(Z)
given [N <: Nat, M <: Nat, R1 <: Nat, R2 <: Nat](using
  prod: Prod[N, M, R1],
  sum: Sum[R1, M, R2]
): Prod[S[N], M, R2] =
  Prod(sum.result)

def prod[N <: Nat, M <: Nat, R <: Nat](n: N, m: M)(
  using prod: Prod[N, M, R]
): R = prod.result
```
