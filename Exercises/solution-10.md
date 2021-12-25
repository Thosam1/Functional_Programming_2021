# Exercise Session 9, Solutions

## Question 1

```scala
"gcd" -> Name("Not provided (part of the lab)")
```

## Question 2

```scala
"map" -> Fun("ls", Fun("f",
  Match(Name("ls"),
    EmptyList,
    "x", "xs",
      Cons(
        Call(Name("f"), Name("x")),
        Call(Call(Name("map"), Name("xs")), Name("f"))))))
```

```scala
"foldLeft" -> Name("Not provided (part of the lab)")
```

## Question 3

```scala
"CAS" ->  Fun("idx", Fun("old", Fun("new",
  IfNonzero(minus(Read(Name("idx")), Name("old")),
    Constant(0),
    Write(Name("idx"), Name("new"), Constant(1)))))),
```
