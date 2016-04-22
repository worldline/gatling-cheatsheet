---
layout: default
---

# Scala CheatSheet

## Basics

### Values and definitions
        
```scala 
val x = 5 // constant, evaluated once
          // x: Int = 5

x = 6     // Will fail (because x is immutable)
          // error: reassignment to val

def y = 5 // definition (function), evaluated at each call
          // y: Int

var v = 5 // variable, only use when really needed
          // v: Int = 5

v = 8     // Will set v to 8 (because mutable)
          // v: Int = 8
   
```

### Conditional 	and control constructs

Conditional _expression_ :

```scala    
if (true) 1 else 2
// res0: Int = 1
```

**Avoid omitting the else part** :

```scala       
if (true) 1         
// this is the same as 
if (true) 1 else () 
// res1: AnyVal = 1
```

Loop structures for imperative programming :

```scala          
var i = 0
while (i < 5) { println(i); i += 1} 
// Output :
// > 0
// > 1
// > 2
// > 3
// > 4

do { println(i); i += 1} while (i < 5) 
// i still equals 5
```

## Data Structures

### Tuples

```scala 
val t = (1, 2, 3) 
// t: (Int, Int, Int) = (1,2,3)
```

Destructuring bind (tuple unpacking via pattern matching) :

```scala                                                               
val (x, y, z) = t 
// > x: Int = 1
// > y: Int = 2
// > z: Int = 3
```

Access to tuple elements; index starts at 1.
**Avoid if possible** (readability).

```scala                                                           
val i = t._1 
// i: Int = 1
```

### Case classes

They give more readable code.

```scala    
case class Vector3d(x: Int, y: Int, z: Int)

val p = Vector3d(1, 2, 3)
// p: Vector3d = Vector3d(1,2,3)
val j = p.x
// j: Int = 1
```

### Immutable Lists

```scala    
val xs = List(1, 2, 3)
// xs: List[Int] = List(1, 2, 3)

//cons : creates a new list with prepended element
0 :: xs 
// res1: List[Int] = List(0, 1, 2, 3)
```

Unpacking lists via pattern matching :

```scala                                                                
val List(a, b, c) = List(1, 2, 3) 
// > a: Int = 1
// > b: Int = 2
// > c: Int = 3
```

Paren indexing; sugar for ```xs.apply(2)```

```scala                                                                
xs(2) 
// res0: Int = 3
```

### Ranges

```scala 
1 to 5         
// res2 = Range(1, 2, 3, 4, 5)
1 until 6      // excludes the upper bound
// res3 = Range(1, 2, 3, 4, 5)
1 to 10 by 2   // specify step
// res4 = Range(1, 3, 5, 7, 9)

//(empty parens)	sole member of the Unit type (like C/Java void) 
()             
// Ouput : >    
```

## Functions

### Declaration 

```scala 
def f(x: Int): Int = { x * x }
def f(x: Int): Int = x * x     // optional {}
def f(x: Int) = x * x          // inferred return type

// mandatory return type for recursive function
def gcd(a:Int, b:Int): Int = 
  if (b == 0) a else gcd(b, a % b)  

// side effects only => Unit
def log(x: Any): Unit = println(x) 
```

### Anonymous 

```scala 
(x: Int) => x * x   

// with inferred type for argument
(1 to 5).map(x => x * x)

// in a function declaration, underscore is positionally matched argument 
(1 to 5).map(2 * _)        // 1 argument                                             
(1 to 5).reduceLeft(_ + _) // 2 arguments
                            
// block style; returns last expression
(1 to 5).map { x => val y=x*2; y } 
```

### Curry

```scala 
val zscore = (mean: Double, sd: Double) => (x: Double) => (x - mean) / sd   
// currying, obvious syntax
def zscore(mean: Double, sd: Double) = (x: Double) => (x - mean) / sd   
// currying, obvious syntax

def zscore(mean: Double, sd: Double)(x: Double) = (x - mean) / sd   
// currying, sugar syntax. but then, you need
// (only for the sugar version)
// trailing underscore to get the partial function : 
val normer = zscore(7, 0.4) _   

def sum(args: Int*) = args.reduceLeft(_ + _) // varargs   
```

## Object Oriented

### Classes

```scala 
class C(x: Int)
val c = new C(4) // instanciation

//same as 
class D(private val x: Int)
// (x is a constructor param and private member)
val d = new D(4)

class E(val x: Int)
// x is now also a public member
var e = new E(4)
e.x

class F(var x: Int) { // x is now modifiable
  assert(x > 0, "positive please") //class body == constructor

  def y = x //declare a public member
  val readonly = 5 // declare a gettable but not settable member
  private var secret = 1 // declare a private member
  def this() = this(42) // alternate constructor, calls primary constructor
}
```

### Literal

```scala 
val cl = classOf[String] 
//class literal (in java: String.class)

val isString = c.isInstanceOf[String] // type check (runtime)
val myString = c.asInstanceOf[String] // cast (runtime)
```

### Companion object

```scala 
object F {
  def apply(v: Int) = new F(v)
}

val one = new F(1) // Default constructor. 
val two = F(2)     // Using the "apply" method defined in the companion object.

val s = new { val x = 2 } //anonymous class
println(s.x)
                        
abstract class Abstract(i: Int) { // non-instanciable 
  def prop: Int //abstract method}	
}

class Concrete(i: Int) extends Abstract(i) { // inherited class with call to super constructor 
  def prop = i //implements the abstract method
}

object Singleton extends Concrete(5)

trait T { def m: String } // purely abstract, like an interface
trait U { val n = "toto" } // traits can have concrete implementation
class Mixin extends Concrete(5) with T with U { // multiple inheritance
  val m = "hello" // a val or var can implement a def
  override def toString = "my mixin"
}   

List(1, 2, 3) // factory method in companion object
```

## Imports
```scala 
//wildcard import : 
import collection._ 

//selective imports
import collection.immutable.Vector
import collection.immutable.{ Vector, List }

// renames import to avoid conflict with types alreading in the scope
import java.util.{ List => JList } 	

// import all from java.util except Date.
import java.util.{ Date => _, _ }

// syntax to define a package container for functions, types
package object mypkg {
  type Predicate = Int => Boolean

  def abs(x: Int) = if (x > 0) x else -x
}   
```

## Parametrization (generics)
```scala 
// generic type.
def mapmake[T](g: T = >T)(seq: List[T]) = seq.map(g)

// Variance.
class MyList[A](var head: A, var tail: List[A])

// Ok
val s = new MyList(4, List(1,2,3)) // Set[Int]
// Not Ok
val nok = new MyList("head", List(1,2,3))

// Type Bounds
trait A
trait B

// A as upper bound (java "<T extends A>" equivalent)
class Upper[T<:A](var item: T)

// B as lower bound (java "<T super B>" equivalent)
class Lower[T>:A](var item: T)   
```

## Pattern Matching
```scala 
List() match {
  // Using "case" reserved word
  case x :: xs =>
  // matches a list head & tail
  // introduces x and xs to the scope
  case _       =>
  // matches anything, equivalent to default in Java
}

Option(1) match {
  case Some(v) => v
  // introduces v to the scope
  case None        => "default"
}

val value = 3
Option(1) match {
  case Some(`value`)         =>
  // compares to an existing value, same as 
  case Some(v) if v == value =>
}

val UppercaseVal = 42
Some(3) match {
  case Some(UppercaseVal) => "42"
  // /!\ UppercaseVal is treated as an existing val, rather than a new pattern variable,
  //     because it starts with an uppercase letter. 
  case _                  => "Not 42"
  //     Thus, the value contained within UppercaseVal is checked against 3, 
  //     and "Not 42" is returned.
}

List(1, "") match {
  case List(e1: String, e2: Int)   =>
  // checks the types of e1 and e2
  case l @ List(a @ List(b, c), d) =>
  //creates aliases for the matched elements
}

((1 to 5) zip (6 to 10)).map {
  case (x, y) => x * y
  // you need {} not () to use pattern matching 
  // (need to define an anonymous function)
}   
```

## For comprehensions
```scala 
// "for" declaration :
for (x <- 1 to 10 if x % 2 == 0) yield x * 10

// or (preferred for longer expressions)
for {
  x <- 1 to 10
  if x % 2 == 0
} yield x * 10

// both are sugar for  
(1 to 10).filter(_ % 2 == 0).map(_ * 10)
// Vector(20, 40, 60, 80, 100) 

// pattern matching
for {
  (x, y) <- (1 to 5) zip (6 to 10) // inside the block
} yield x * y

// sugar for
((1 to 5) zip (6 to 10)).map {
  case (x, y) => x * y
}
// Vector(6, 14, 24, 36, 50)

// default example :
// for comprehension: cross product (all possible combinations of x and y)
for (x <- 1 to 5; y <- 6 to 10) yield x + y
// same as :
1 to 5 flatMap { x => 6 to 10 map { y => x + y } }
// Vector(6, 7, 8, 9, 10, 12, ...

// imperative style example :   
for (x <- 1 to 5; y <- 6 to 10) {
  val frac = x.toFloat / y.toFloat
  println(f"$x/$y = $frac%.1f")
  // f"..." substitutes $x and $y, and formats $frac with %.1f
}
// Output :
// > 1/6 = 0,2
// > 1/7 = 0,1
// > 1/8 = 0,1
// > 1/9 = 0,1
// > 1/10 = 0,1
// > 2/6 = 0,3
// ...   
```

## Lazy evaluations

### Lazy val declaration 

```scala 
lazy val x = { println("created"); 1 } // Not yet initialized.
// x: Int = <lazy>

x * 2                                  // Initialized. "created" will be displayed.
// Output : 
// > created
// > res0: Int = 2

x * 3                                  // Already initialized. No "created" displayed.
// res1: Int = 3
```

### Call-by-name arguments

```scala                                       
def log(level: Int, x: => Any): Unit = if (level > 0) println(x)
// log: (level: Int, x: => Any)Unit

log(0, {println("Not displayed."); 3})
// Output : >

log(1, {println("Displayed !"); 3})
// Output :
// > Displayed !
// > 3
   
```

