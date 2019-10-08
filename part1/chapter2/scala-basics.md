---
description: '(unfinished) including classes, Traits, and Tuples'
---

# 2.1 Scala basics

## Classes

Classes in Scala are blueprints for creating objects. They can contain methods, values, variables, types, objects, traits, and classes which are collectively called _members_. Types, objects, and traits will be covered later in this Chapter.

### Defining a class

A minimal class definition is simply the keyword `class` and an identifier. Class names should be capitalized.

```scala
class User
val user1 = new User
```

 As below, a more general class `Point` has 4 members: variables `x` and `y`, methods `move` and `toString`. Its constructor is in the class signature `(var x: Int, var y: Int)`. 

The `move` method takes 2 integer arguments and returns the Unit value `()`, which carries no information. This corresponds roughly with `void` in Java-like languages.

`toString`, on the other hand, does not take any arguments but returns a `String` value. Since `toString` overrides `toString` from [`AnyRef`](https://docs.scala-lang.org/tour/unified-types.html), it is tagged with the `override` keyword.

```scala
class Point(var x: Int, var y: Int) {
    def move(dx: Int, dy: Int): Unit = {
        x = x + dx
        y = y + dy
    }
    override def toString: String =
        s"($x, $y)"
}

val point1 = new Point(2,3)
point1.x //2
println(point) // prints (2,3)
```

{% hint style="info" %}
Similar to Java, Scala uses the keyword `new` to instantiate a class. 

However, instantiation shouldn't be written in the same file with the declaration of the class, in this case, _Point.scala._ So last 3 lines should be written in other file, say like, the _Main.scala_ file. 
{% endhint %}

### Constructors

Constructors can have optical parameters by providing default values: 

```scala
class Point(var x: Int = 0, var y: Int = 0)

val origin = new Point  // x and y are both set to 0

val point1 = new Point(1) // set x to be 1
println(point1.x)  // prints 1

val point2 = new Point(y=2) // to simply set y, you'll need to name the parameter
println(point2.y)  // prints 2
```

{% hint style="info" %}
Constructor reads arguments left to right, to simply set `y` \(the 2nd parameter\), you'll need to name the parameter. Doing so also enhances clarity of the code.
{% endhint %}

### Getter and Setter

Members are public by default.  Use the `private` access modifier to hide them from outside of the class.

```scala
class Point {
  private var _x = 0
  private var _y = 0
  private val bound = 100

  def x = _x  //Getter
  def x_= (newValue: Int): Unit = {  //Setter
    if (newValue < bound) {
      _x = newValue
      println("x value set to " + newValue)
    }
    else printWarning
  }

  def y= _y  //Getter
  def y_= (newValue: Int): Unit = {  //Setter
    if (newValue < bound) {
      _y = newValue
      println("y value set to " + newValue)
    }  else printWarning
  }

  private def printWarning= println("WARNING: Out of bounds")
}

val point1 = new Point
point1.x = 99 // prints "x value set to 99"
point1.y = 101 // prints the warning
```

Data is stored in the private variables `_x` and `_y` . Using methods `def x` and `def y` to access the private data, which is the **Getter**. Methods `def x_=` and `def y_=` are for validating and setting the value of `_x` and `_y`, which is the **Setter**. 

{% hint style="info" %}
**Be careful of the syntax!!!**

* Variables name \(attributes name\): `_abc`
* Getter method name: `def abc`
* Setter method name: `def abc_=`

**How to call a Getter/Setter function:**

```scala
val example = new Example
//setter
example.abc_=(10)
//getter
println(example.abc)
```
{% endhint %}



## Traits

Traits are used to share interfaces and fields between classes. They are similar to Java 8’s interfaces. Classes and objects can extend traits but traits cannot be instantiated and therefore have no parameters. 

### Defining a trait

Similar to defining a class, a minimal trait is simply the keyword `trait` and an identifier:

```scala
trait HairColor
```

Traits become especially useful as **generic types** and with **abstract methods**.  Extending the `trait Iterator[A]` requires a type `A` and implementations of the methods `hasNext` and `next`.

```scala
trait Iterator[A] {
  def hasNext: Boolean
  def next(): A
}
```

### Using Traits

Use the `extends` keyword to extend a trait. Then implement any abstract members of the trait using the `override` keyword:

```scala
trait Iterator[A] {
  def hasNext: Boolean
  def next(): A
}

class IntIterator(to: Int) extends Iterator[Int] {
  private var current = 0
  override def hasNext: Boolean = current < to
  override def next(): Int =  {
    if (hasNext) {
      val t = current
      current += 1
      t
    } else 0
  }
}

val iterator = new IntIterator(10)
iterator.next()  // returns 0
iterator.next()  // returns 1
```

This `IntIterator` class takes a parameter `to` as an upper bound. It `extends Iterator[Int]` which means that the `next` method must return an Int.

### Sub-type

Where a given trait is required, a sub type of the trait can be used instead.

```scala
import scala.collection.mutable.ArrayBuffer

trait Pet {
  val name: String
}

class Cat(val name: String) extends Pet
class Dog(val name: String) extends Pet

val dog = new Dog("Harry")
val cat = new Cat("Sally")

val animals = ArrayBuffer.empty[Pet]
animals.append(dog)
animals.append(cat)
animals.foreach(pet => println(pet.name))  // Prints Harry Sally
```

The `trait Pet` has an abstract field `name` which gets implemented by Cat and Dog in their constructors. On the last line, we call `pet.name` which must be implemented in any subtype of the trait `Pet`.












