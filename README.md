# Scala: Getting Started

I needed a course to help me understand how Scala and SBT are configured.

* [Scala: Getting Started](https://app.pluralsight.com/library/courses/scala-getting-started/discussion)

### Module 2: Building Blocks
*  sbt seemed to create a bunch of folders not mentioned in the videos.
*  sbt tried to override the `/project/build.properties` file.
*  Had to use a different version of the eclipse plugin.  In the `/project/plugins.sbt` file, I used:
`addSbtPlugin("com.typesafe.sbteclipse" % "sbteclipse-plugin" % "5.2.4")`
*  Run `sbt eclipse` runs the plugin's eclipse task.  
*  If in sbt interactive mode and you change any sbt settings, you need to use `reload` to update those changes.  This may have been one of my problems with the Spark project setup.
*  Opening in IntelliJ as an SBT or Eclipse Project did not set up the folder structure correctly.
    *  I built out the src/main/scala, etc.
    *  I would just skip this, but the tutorial seems to use this eclipse plugin a lot. I wish they would just use the defaults.
    *  sbt eclipse doesn't update the IntelliJ iml files, though.  So I had to close the project and reopen it.  That seemed to pick up the dependencies.  Seems like a bad idea to be using two different project settings.
#### ScalaTest
Many libraries and patterns are available:
* xUnit -> FunSuite
* BDD -> FlatSpec
    * Suggested by Scala
* Ruby RSpec -> FlatSuite
* Acceptance -> FeatureSpec

Need to fix dependencies again:
In Preferences, search for sbt and select sbt shell: use for project reloads and builds.

It also looks like there is a library version difference.  We need:
`org.scalatest.flatspec.AnyFlatSpec`

#### Companion Objects
When an object is created with the same name as a class, it is a companion object to that class.
Any object can ommit the `.apply()` and be acted on directly.  
So having .apply() take the same parameters as the constructor allows us to create a singleton object that can return new instances of itself.

#### Clean up
*  Can remove {} for classes with no implementation beyond the constructors.
*  Can remove {} around method bodies that are single, but complex expressions.  
    (Everything is an expresion in scala; every statement has a return value even if it's not explicit.)
*  Infix notation - any method that takes only one parameter can be called without the dot or the parentheses.
    *  Should also only be used for methods with no side effects 
*  Any part of a class that is not part of a method will run as part of construction.
*  Adding val to a method parameter exposes it as a public variable on the object.
    *  Just realized `val` vs. `var`.  `val` is safer.

#### Case Classes
*  Classes with the keyword case.
    *  need to add back var to the method parameters
*  Always public so no longer need val for constructor arguments
*  Come with own companion objects built in (so don't need new)
*  Builds its own equals implementation, so you don't need to store the matched file object.
*  Also allow Object Decomposition and Pattern matching
*  `case`s can also contain guard expressions
*  `???` throws a not-implemented exception

#### Exception Handling
*  Mostly the same as in Java
*  can omit brackets if the try block is a single expression
*  catch block use case blocks against the exception type.

#### Recursion
*  Scala implement tail call recursion if your method is tail-recursive
*  A method is tail recursive if it only calls itself as its last action.
    *  Often requires helper methods and accumulators.
    *  The method call becomes the *only* (or main) thing in the else block.
        -  i.e., `n * fact(n - 1)` vs. `fact(n - 1, acc * n)`
    *  The method can be popped off the stack after each call, because the accumulator is holding the results.

*  Cons operator `::` splits lists, for instance:
    -  case `ioObject :: rest` takes a list and puts the first value in `ioObject` and the rest in `rest`
    -  used as an expression, it can also append items to lists such as `recursiveMatch(rest, file :: currentList)`
*  Triple-colon operator `:::` can append two lists.    
*  In the case of recursion, return type inference is not allowed!
*  Use `@tailrec` to guarantee the compiler uses tail recursion.
Nesting methods within methods is possible in Scala, helping to encapsulate helper methods.

### Module 3: Diving for Data
*  May need admin mode for console since we will be doing file inspection.

#### Scala.io
*  Scala's IO library was built with very limited functionality and has some bugs?
*  Generally suggested to use Java's `nio` library.
*  But works okay for simple cases.

#### General Points
*  Can import within methods themselves.
*  Use `case NonFatal(_) => false` to turn all "normal" exceptions into false.  Better than catching *all* exceptions.
*  `filter` method is like `map`, but loops through all of the objects are returns a list of those where the function evaluates to true

#### File IO
Inner tries can be provide safety and allow `finally`s that have access to the variables
scoped within the try block.
```
try {
    val fileSource = Source.fromFile(file)
    try {

    } catch {
        case NonFatal(_) => false
    }
} catch {
  case NonFatal(_) => false
}
```

#### Options
*  Options are wrappers that provide null-check behaviors for other types, and allows us to avoid "magic types".
    * Some or None
*  Useful with matches.  If we match on an Option type, the constructor arguments are exposed and can be captured by the case.
```
val x = Option(y: String)
x match {
    case Some(yvalue) => doSomething(yvalue)
}
```
*  Also, with matches, Some() and None cases have to both be provided, otherwise we get a compiler warning.

#### Folding Lists
Folding left - starting from the left of the list, we will fold all of its elements into one final product using a provided fold function.
Uses an accumulator value to keep track of current value.  Accumulator needs a seed value.
Fold-rights do the same thing but start from the right side of the list.

#### Tuples
An object that can store up to 22 different values.
Written as a comma separate list surrounded by parentheses.
Similar to Boxing with Object, but with Boxing you lose type safety.
With tuples, each slot is typed.

#### Writing Data
You can use Java .io FileWriter/PrintWriter patterns.
String interpolation: If a string is preceded by an `s`, then we can use any scoped variable within hte string using `$`.

### Module 4: Wrapping Up
#### Creating a Scale console application

