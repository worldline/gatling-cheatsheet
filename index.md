---
layout: default
---

# Scala CheatSheet

[Printable (MS Word) version](https://github.com/worldline/scala-cheatsheet/releases/download/v1.0/Scala.CheatSheet.docx)

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

#### Loop structures for imperative programming :

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

#### Destructuring bind (tuple unpacking via pattern matching) :

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

#### List construction

```scala    
val fruits = "apples" :: "oranges" :: "pears" :: Nil
// Nil is same as List.empty[Nothing]

val fibos = List(1, 2, 3, 5, 8, 13)

//cons : creates a new list with prepended element
0 :: fibos
// List(0, 1, 2, 3, 5, 8, 13)
```

#### Paren indexing;
sugar for ```xs.apply(2)```

```scala                                                                
xs(2) 
// res0: Int = 3
```

#### Basic methods

```scala                                                                
fruits.head   // "apples"
fruits.tail   // List(oranges, pears)

fibos.isEmpty // false
Nil.isEmpty   // true

fruits.length // 3
fruits.size   // 3

fruits.contains("pears") // true

// pretty-print
fibos.mkString                // "1235813"
fibos.mkString(",")           // "1,2,3,5,8,13"
fibos.mkString("[", ", ","]") // "[1,2,3,5,8,13]"

// /!\ O(n) time
fruits.last     // "pear" 
fruits.init     // List(apples, oranges)
```

#### Methods which build new lists

```scala                                                                
fibos.take(4)     // List(1, 2, 3, 5)
fibos.drop(4)     // List(8, 13)
fibos.splitAt(4)  // (List(1, 2, 3, 5),List(8, 13))

fibos.reverse     // List(13, 8, 5, 3, 2, 1)

fruits.updated(1, "bananas") 
// List(apples, bananas, pears)

fruits ++ List("bananas", "melons") 
// List(apples, oranges, pears, bananas, melons)
fruits ::: List("bananas", "melons")
// same result (++ is defined for all collections 
// and ::: only for lists)
```

#### Order

```scala      
fibos.min // 1
fibos.max // 13

fruits.minBy(_.length) // "pears"
fruits.maxBy(_.length) // "oranges"

fruits.sorted // default order
// List(apples, oranges, pears)
fruits.sortBy(_.length)
// List(pears, apples, oranges)                                                          
```

#### Zip / unzip

```scala  
fruits zip fibos
// List((apples,1), (oranges,2), (pears,3))

val pairs=List((1,6), (3,4), (5,2))
val (l1, l2) = pairs.unzip
// l1 = List(1, 3, 5)
// l2 = List(6, 4, 2)
// see also unzip3
```

#### Unpacking lists via pattern matching :

```scala                                                                
val List(a, b, c) = List(1, 2, 3) 
// > a: Int = 1
// > b: Int = 2
// > c: Int = 3
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
"a string" match {
  case "a"       => // basic comparison
  case "b" | "c" => // or
  case _         => // matches anything, equivalent to default in Java
}

List() match {
  case x :: xs =>
  // matches a list head & tail
  // introduces x and xs to the scope
  case Nil     =>
}

Option(1) match {
  case Some(v) => v
  // introduces v to the scope
  case None    => "default"
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

val urlRe = """(https?:\/\/)?([\da-z\.-]+)([\/\w \.-]*)\/?""".r
"http://www.google.fr/search" match {
  case urlRe(protocol, host, path) =>
  // extract groups from the pattern
}

((1 to 5) zip (6 to 10)).map {
  case (x, y) => x * y
  // you need {} not () to use pattern matching 
  // (need to define an anonymous function)
}
```

## Collections API

### Higher order functions on collections

#### Filtering with a predicate

```scala 
fruits.exists(_.length == 5) 
// true ("pears".length is 5)  
fruits.forall(_.length == 5) 
// false ("apples".length is 6)

fibos.filter(i => i % 2 == 0) 
// List(2, 8)
fibos.filterNot(i => i % 2 == 0) 
// List(1, 3, 5, 13)

val (evens, odds) = fibos.partition(i => i % 2 == 0)
// evens = List(2, 8)
// odds = List(1, 3, 5, 13))

fibos.takeWhile(i => i <= 8)
// List(1, 2, 3, 5, 8)
fibos.dropWhile(i => i <= 8)
// List(13)
fibos.span(i => i <= 8)
(List(1, 2, 3, 5, 8),List(13))
```

#### Transformation

```scala 
fibos.map(i => i * 2) // List(2, 4, 6, 10, 16, 26)
// can also be written
fibos.map(_ * 2)
// or even (since * is a method of Int)
fibos.map(2*)

fruits.map(_.toList)
// List(List(a, p, p, l, e, s), List(o, r, a, n, g, e, s), List(p, e, a, r, s))
fruits.flatMap(_.toList)
// List(a, p, p, l, e, s, o, r, a, n, g, e, s, p, e, a, r, s)
fruits.map(_.toList).flatten 
// same, but less efficient
```

#### Combine elements

```scala 
fibos.reduce((x, y) => x + y)
fibos.sum     // 32

fibos.reduce(_ * _)
fibos.product // 3120

List.empty[Int].reduce((x, y) => x + y)
// UnsupportedOperationException

// fold to set the initial accumulator
List.empty[Int].fold(0)((x, y) => x + y) // 0
fibos.fold(1)((x, y) => x * y) // 3120
```

##### Use foldLeft, foldRight, reduceLeft, reduceRight when the operator is not associative

```scala 
List(1, 3, 8).foldLeft(100)((s, x) => s - x)    
// ((100 - 1) - 3) - 8 == 88
List(1, 3, 8).foldRight(100)((s, x) => s - x)    
// 1 - (3 - (8 - 100)) == -94
List(1, 3, 8).reduceLeft((s, x) => s - x)    
// (1 - 3) - 8 == -10
List(1, 3, 8).reduceRight((s, x) => s - x)    
// 1 - (3 - 8) == 6
```

### For comprehensions
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

### Maps

#### Basics

```scala 
val romanNumerals = Map("I" -> 1, "V" -> 5, "X" -> 10)
romanNumerals("V") // 5

// don't confuse Map (type) with map (method)
romanNumerals.map {
  case (rom, num) => (num, rom)
}
// Map(1 -> I, 5 -> V, 10 -> X)
```

#### Grouping

```scala 
val words = List("the", "quick", "fox", "jumped", "over", "the", "lazy", "dog")

words.groupBy(_.length)
// Map(5 -> List(quick), 
//     4 -> List(over, lazy), 
//     3 -> List(the, fox, the, dog),
//     6 -> List(jumped))
```

#### Updating an immutable Map

```scala 
val capitals = Map("USA" -> "Washington", "Switzerland" -> "Bern")
capitals.updated("France", "Paris")
capitals + ("France" -> "Paris")
// Map(USA -> Washington, Switzerland -> Bern, France -> Paris)

capitals - "USA"
// Map(Switzerland -> Bern)
```

#### Handling missing keys

```scala 
romanNumerals("II")
// NoSuchElementException: key not found: II

romanNumerals.get("II") // Option[Int] = None
romanNumerals.get("I") // Option[Int] = Some(1)

capitals.getOrElse("Italy", "undefined")
// undefined

val capitalsDef=  capitals.withDefaultValue("undefined")
capitalsDef("Italy")
// undefined

capitalsDef.get("Italy")
// None /!\
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
