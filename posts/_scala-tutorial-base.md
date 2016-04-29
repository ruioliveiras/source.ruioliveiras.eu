Scala Language base Tutorial
=======================

#### Hot links

Scala Oficial

Laboratory 1

http://lampwww.epfl.ch/~michelou/scala/using-scala-from-java.html

#### Me and Scala

I'm recent scala developer, started programming in scala in November of 2015, I'm not an expert, but I think I'm good enough to help you understand scala.

## Introduction
I start with some generic concepts ([Mutability and Immutability](#mutability-ummitability) and [Singleton object](#singleton)) that are not specific of the scala language.

Next, I have separated the main features in 3 Charters:  [Scala as a Object Oriented Language](#scala-oo), [Scala as a Functional language](#scala-functional), [Scala as a Script language](#scala-script). Where I have put syntax features in the Script chapter.

I the end I also some other topic, Reflection and Macros - this two things are still currently in experimental, but they are very cool and usable; Concurrency  which in Scala is very intuitive; Native XML scala feature; and some notes about string processing. 

<a name="concepts"></a>
## Concepts

Before we get started, we must know about a few general concepts. 
Concepts that are not specific from the Scala language but general from the programming.
But where in Scala they are more used or has more importance.

<a name="mutability-ummitability"></a>
### Mutability and Immutability

Immutability is one of pillars of a functional language.

** Mutable variable ** its state can change over time.

** Immutable variable ** its state can't change over time.

Immutability advantages
  * Is very good for concurrency (many threads changing the value of one variable at same time may be dangerous )
  * Code more clean

Immutability disadvantages
  * May be  costly to be recreating objects on each change

<a name="singleton"></a>
### Singleton object

Singleton objects are unique at the runtime. There will not exist at same time two instances of this objects types.

In Java a class have its static scope, where are the declared static methods and variables. The static scope of the class are a built-in singleton object

But in Scala there aren't the keyword static.
Instead there are classes and singleton objects, they are managed differently.


<a name="scala-oo"></a>
## Scala as a Object Oriented Language

In Scala we can do everything that we can do in JAVA.

<a name="objects-class"></a>
### Objects and Class

**Class**: is equivalent to Java. 
~~~~scala
// declaring
class Example(favoriteNumber:Int, name:String) {
    private val dadFavoriteNumber:Int = favoriteNumber * 2
    def dadNumber():Int = dadFavoriteNumber
}
// instantiating
new Example(1, "ruiOliveira").dadNumber()
~~~~

**Object**: is a tool that can be used in different situations.
One used situation is to work has a singleton
(in the nested class and object you will see that not all objects are singleton)

~~~~~scala
// a class
class Example(favoriteNumber:Int, name:String) { ... }

// a singleton object
object Example{
    var counter:Int = 10
    def counterPlusOne():Int ={ counter += 1; counter }  
    def newExample(name:String) = new Example(counterPlusOne(), name);
}
Example.newExample("Rui Oliveira")
~~~~~

In scala a class don't have static methods or variables, what you can do is create a Object with the same name of the class.

<a name="traits"></a>
### Traits

Traits is equivalent to JAVA interface, but with state variables and method implementations.
Note that traits are a conceptual thing resolver by the scala compiler in-lining methods, at the JVM is just a Interface


**Trait example**

Who uses this trait must declare a value name, and will have the remaining stuff for free.
~~~~~scala
trait Person{
    val name:String;
    var nameCounter = 0
    def getName():String = {
        nameCounter += 1
        // value name is the last thing of this block so will be returned 
        // Keep this in mind, scala don't require return keyword 
        name
    }

    def howManyTimesNameWasCalled() = nameCounter
}

def testTraitPerson(p:Person):Int = p.howManyTimesNameWasCalled()
~~~~~ 

**Object using a Trait**, note that we are defining name value.
~~~~~scala
object RuiOliveira extends Person{
    val name = "rui Oliveira";
}

testTraitPerson(RuiOliveira)
~~~~~
Has you can see a object is a variable that uses Person so it can be used in the testTraitPerson

**Class using a trait**, note that we are declaring value name in the parameters now.
~~~~~scala
class OtherPerson(val name:String) extends Person

val p = new OtherPerson('pedro')
testTraitPerson(p)
~~~~~

Has was expected we can use traits in class's


**Trait on variable**. (Anonymous declaration) The class Dog don't use the trait Person, even so we can create a variable with that implementing what is needed. 

~~~~scala
class Dog(val dogName:String)

val p = new Dog("doggy") with Person {
    val name = dogName;
}
testTraitPerson(p)
~~~~

<a name="case-class"></a>
### Case Class

Case class are a "modification" to normal classes
~~~~~scala
// normal class
class Onwer( name:String)
class Phone( number:String,  owner:Onwer)
// case class
case class Owner2( name:String)
case class Phone2( number:String, owner:Owner2)
~~~~~

Creating a case class you will be access:
  * default create functions: toString, copy, equal and hashcode.
  * class will be immutable by default
  * default create singleton object with method unapplay and apply functions


!explain !
!Here explain the power of unapplay and apply!

<a name="generic-class"></a>
### Generic Class


### Inner Class and Object 

<a name="scala-functional"></a>
## Scala as a Functional language

On this section, there will be explained some of the Scala features that are related to Functional World:
  * var and val (Mutability and Immutability)
  * Lambda functions
  * handling collection in a functional way
  * implicit things (variable, method and class) 

<a name="var-val"></a>
### Var and Val
Var and Val keyword can be used to declare objects or in class parameter.

**var** stands for variable
**val** stands for value

~~~~scala
var counter = 1;
val max = 100;

while(counter < max) {
  counter += 1;
  // in scala there aren't ++ operator
}
~~~~

On this example counter variable are being setted multiple times. While if you try to set a new value in the counter, it will gives you a compile error.

Usage on as a class parameter:

~~~~scala
class Person(val name:String, var age:Int) {
  def doBirthdate:Unit = age += 1;
}
~~~~

The parameter name will never got changed while age can be changed.

Var and Val with class types

~~~~scala
class Club(val peopleList:List[Person], val owner:Person)
~~~~

On this example we have the owner parameter as val, but this don't mean that we cannot use 'onwer.doBirthdate' and change the state of owner.
When we set a parameter has val, we are telling the compiler that the 'symbol' named 'owner' will only be setted one time.

We say that a class is Immutable when all the parameters are 'val' and all the parameters Types are also immutable.

<a name="higter-order"></a>
### Lambda functions

The syntax to create a lambda function are:

~~~~scala
val f = (x:Int) => x*2
f(2) 
// will give 4
~~~~

We can receive function as parameter

~~~~scala
val op1 = (x:Int, y:Int) => x * 2 + y
val f = (x:Int, y:Int, op: ((Int,Int) => Int) ) => op(x,y)
f(2,3,op1) 
// will give 7
~~~~

<a name="collections-functions"></a>
### Collections functions

WARNING: Not writed yet
TODO: write this topic.

<a name="-implicit-variable"></a>
### Implicit Variable

Implicit variables are a mechanism to avoid always be passing some variable into a function, normally this is very useful on recursive functions.

~~~~scala
// also on this example we are using Scala string processing
def f(name:String)(implicit location:String) = s"hello $name, you are at $location"
implicit  val l = "lisbon"
f("rui")
// will give 'hello rui, you are at lisbon'
f("rui")("porto")
// will give 'hello rui, you are at porto'
~~~~

The value l is been declared as implicit of type String, when ever its needed a Implicit String the value l will be used.

We can import implicit variable and use in other context:
~~~~scala
object o1{ implicit val i = 500}
def f(implicit h:Int) = h * 2
// importing the variable i
import o1._
// we are calling the function f (no parentheses need)
// and with the result call toString
printf(f.toString);
~~~~

Also we can have more than one variable as implicit input:

~~~~scala
def f(i:Int)(implicit k:Int, s:String):String = i match {
  case 0 => s + k
  case _ => f(i - 1)
}
implicit val i = 100;
implicit val i = "i has ";
f(50)
~~~~

<a name="-implicit-method"></a>
### Implicit Method

Implicit methods are used to convert a variable into other

<a name="-implicit-class"></a>
### Implicit Class

Implicit class are used to lay methods in certain types

<a name="scala-script"></a>
## Scala as a Script language

WARNING: Not writed yet
TODO: write this topic.

<a name="type-inference"></a>
### Type Inference

WARNING: Not writed yet
TODO: write this topic.

<a name="syntax-sugar"></a>
### Syntax Sugar

WARNING: Not writed yet
TODO: write this topic.

<a name="scala-cool"></a>
## Scala as a cool language

WARNING: Not writed yet
TODO: write this topic.

<a name="scala-concurrency"></a>
### Scala Concurrency

WARNING: Not writed yet
TODO: write this topic.

<a name="futures-promises"></a>
#### Futures and Promises

WARNING: Not writed yet
TODO: write this topic.

<a name="todo"></a>
#### @todo: add topics here

WARNING: Not writed yet
TODO: write this topic.

<a name="scala-reflection"></a>
### Scala Reflection

WARNING: Not writed yet
TODO: write this topic.

<a name="universe"></a>
#### Universe

WARNING: Not writed yet
TODO: write this topic.

<a name="todo2"></a>
#### Add something here

WARNING: Not writed yet
TODO: write this topic.

<a name="scala-macros"></a>
### Scala Macros

WARNING: Not writed yet
TODO: write this topic.

<a name="whitebox-blackbox"></a>
#### Whitebox and blackbox macros

WARNING: Not writed yet
TODO: write this topic.

<a name="annotations-macros"></a>
#### Annotations Macros

WARNING: Not writed yet
TODO: write this topic.

<a name="scala-xml"></a>
### Scala XML

WARNING: Not writed yet
TODO: write this topic.

<a name="scala-string"></a>
### Scala String Processing

WARNING: Not writed yet
TODO: write this topic.

