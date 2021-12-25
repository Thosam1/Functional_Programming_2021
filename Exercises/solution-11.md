# Exercise Session 11, Solutions

## Question 1

### Question 1.1

```scala
def succ = (n => f => x => f(n(f)(x)))
```

### Question 1.2

```scala
def add = (n => m => n(succ)(m))
```

## Question 2

### Question 2.1

```scala
def size = (l => l(zero)( h => t => succ(size(t)) ))
```

### Question 2.2


```scala
def map = (f => l => l(nil)( h => t => cons( f(h) )( map(f)(t) ) ))
```

### Question 2.3

```scala
def foldRight = (f => z => l => l(z)( h => t => f(h)(foldRight(f)(z)(t)) ))
```