Scala Language base Tutorial
=======================

#### Hot links

Scala Oficial

Laboratory 1

http://lampwww.epfl.ch/~michelou/scala/using-scala-from-java.html

#### Me and Scala

I'm recent scala developer, started programing in scala in november of 2015. 

Scala has the goods of of all paradigms Objects, Functional, Scripting.

## Summary


1. [Concepts](#concepts)
  * [Mutability and Immutability](#mutability-ummitability)
  * [Singleton object](#singleton)
1. [Scala as a Object Oriented Language](#scala-oo)
  * [Objects and Class](#objects-class)
  * [Traits](#traits)
  * [Case Class](#case-class)
  * [Trait and Extension](#generic-class)
  * [Generic Class](#generic-class)
1. [Scala as a Functional language](#scala-functional)
  * [Var and Val](#var-val)
  * [Higter order functions](#higter-order)
  * [Collections functions](#collections-functions)
  * [Implicit Variable](#-implicit-variable)
  * [Implicit Method](#-implicit-method)
  * [Implicit Class](#-implicit-class)
1. [Scala as a Script language](#scala-script)
  * [Type Inference](#type-inference)
  * [Syntax Sugar](#syntax-sugar) - map({}) don't, need return code block {} etc
  * []
1. [Scala as a cool language](#scala-cool)
  * [Scala Concurrency](#scala-concurrency)
    * [Futures and Promises](#futures-promises)
    * [@todo: add topics here](#todo)
  * [Scala Reflection](#scala-reflection)
    * [Universe](#universe)
    * [Add something here](#todo2)
  * [Scala Macros](#scala-macros)
    * [Whitebox and blackbox macros](#whitebox-blackbox)
    * [Annotations Macros](#annotations-macros)
  * [Scala XML](#scala-xml)
  * [Scala String Processing](#scala-string)

<a name="concepts"></a>
## Concepts

Before we get started, we must know about a few general concepts. 
Concepts that are not specific from the Scala language but general from the programming.
But where in Scala they are more used or has more importance.

<a name="mutability-ummitability"></a>
### Mutability and Immutability

Immutability is one of pillars of a functional language.

** Mutable variable ** its state can change with the time.

** Immutable variable ** its state can't change with the time.

Immutability advantages
  * Is very good for concurrency (many threads changing the value of one variable at same time may be dangerous )
  * Turn your code more clean

Immutability disadvantages
  * May be  costly to be recreating objects on each change

<a name="singleton"></a>
### Singleton object

Singleton objects are unique at the runtime. There will not exist at same time two instances of this objects types.
In Java there are the static keyword that puts the method or the variable into the singleton object of the class.
But in Scala there the classes don't have a singleton object.
Instead there are classes and singleton objects, they are managed differently.


<a name="scala-oo"></a>
## Scala as a Object Oriented Language

In Scala we can do everything that we can do in JAVA.

<a name="objects-class"></a>
### Objects and Class

**Class**: is equivalent to Java. 
~~~~
// declaring
class Example(favoriteNumber:Int, name:String) {
    private val dadFavoriteNumber:Int = favoriteNumber * 2
    def dadNumber():Int = dadFavoriteNumber
}
// instantiating
new Example(1, "ruiOliveira").dadNumber()
~~~~

**Object**: is a tool that can be used in different situations.
One of the used situations is to work has a singleton
(in the nested class and object you will see that not all objects are singleton)

~~~~~
class Example(favoriteNumber:Int, name:String) { ... }

// declaring object
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
~~~~~
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
~~~~~
object RuiOliveira extends Person{
    val name = "rui Oliveira";
}

testTraitPerson(RuiOliveira)
~~~~~
Has you can see a object is a variable that uses Person so it can be used in the testTraitPerson

**Class using a trait**, note that we are declaring value name in the parameters now.
~~~~~
class OtherPerson(val name:String) extends Person

val p = new OtherPerson('pedro')
testTraitPerson(p)
~~~~~

Has was expected we can use traits in class's


**Trait on variable**. The class Dog don't use the trait Person, but even so we can create a variable with that implementing what is need. 

~~~~
class Dog(val dogName:String)

val p = new Dog("doggy") with Person {
    val name = dogName;
}
testTraitPerson(p)
~~~~

<a name="case-class"></a>
### Case Class

Case class are a "modification" to normal classes
~~~~~
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

~~~~ 
var counter = 1;
val max = 100;

while(counter < max) {
  counter += 1;
  // in scala there aren't ++ operator
}
~~~~

On this example counter variable are being setted multiple times. While if you try to set a new value in the counter, it will gives you a compile error.

Usage on as a class parameter:

~~~~
class Person(val name:String, var age:Int) {
  def doBirthdate:Unit = age += 1;
}
~~~~

The parameter name will never got changed while age can be changed.

Var and Val with class types

~~~~
class Club(val peopleList:List[Person], val owner:Person)
~~~~

On this example we have the owner parameter as val, but this don't mean that we cannot use 'onwer.doBirthdate' and change the state of owner.
When we set a parameter has val, we are telling the compiler that the 'symbol' named 'owner' will only be setted one time.

We say that a class is Immutable when all the parameters are 'val' and all the parameters Types are also immutable.

<a name="higter-order"></a>
### Lambda functions

WARNING: Not writed yet
TODO: write this topic.

<a name="collections-functions"></a>
### Collections functions

WARNING: Not writed yet
TODO: write this topic.

<a name="-implicit-variable"></a>
### Implicit Variable

WARNING: Not writed yet
TODO: write this topic.

<a name="-implicit-method"></a>
### Implicit Method

WARNING: Not writed yet
TODO: write this topic.

<a name="-implicit-class"></a>
### Implicit Class

WARNING: Not writed yet
TODO: write this topic.

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

