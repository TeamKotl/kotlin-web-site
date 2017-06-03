---
type: doc
layout: reference
category: "Syntax"
title: "Extensions"
---

# Extensions

kotlin은 C#과 Gosu와 유사하게 class에서 상속하거나 Decorator와 같은 모든 유형의 디자인 패턴을 사용하지 않고도 새로운 기능으로 클래스를 확장 할 수 있습니다.  Extensions은 특별한 선언을 통해 이루어 집니다. kotlin은 extension functions 와 extensions 의 속성을 지원합니다.

## Extension Functions

Extensions function을 선언하려면, 이름앞에 수신자 유형 즉, `receiver type`  확장되는 유형을 붙이면 됩니다.

아래 예제는  `MutableList<Int>` 에 `swap` 함수를 추가합니다.

``` kotlin
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' corresponds to the list
    this[index1] = this[index2]
    this[index2] = tmp
}
```

extension function 내의  *this*{: .keyword } 키워드는 수신자객체(receiver object) 에 해당합니다. (점앞에 전달되는 객체 :  `MutableList<Int>.swap`)

이제 `MutableList<Int>` 에서 아래의 예제와 같은 함수를 부르실 수 있습니다.

``` kotlin
val l = mutableListOf(1, 2, 3)
l.swap(0, 2) // 'this' inside 'swap()' will hold the value of 'l'
```

물론, 위의 함수는 임의이 `MutableList<T>` 에 대해서만 의미가 있고, 여러분은 그것을 일반화 시킬 수 있습니다.

``` kotlin
fun <T> MutableList<T>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' corresponds to the list
    this[index1] = this[index2]
    this[index2] = tmp
}
```

여러분은 generic 형식 매개 변수를 함수 이름이  receiver type에서 사용 가능하기 전에 선언하시면 됩니다. (`fun <T> MutableList<T>.swap`)

 [Generic functions](generics.html) 를 참고 하시면 됩니다 .

## Extensions는 정적으로 해결 Extensions are resolved **statically**

Extensions는 실제로 확장된 class를 수정하지않습니다. extension을 정의하게 되면, 클래스에 새로운 member를  insert 하지 않고, but merely make new functions callable with the dot-notation on variables of this type.

We would like to emphasize that extension functions are dispatched **statically**, i.e. they are not virtual by receiver type.
This means that the extension function being called is determined by the type of the expression on which the function is invoked,
not by the type of the result of evaluating that expression at runtime. For example:

``` kotlin
open class C

class D: C()

fun C.foo() = "c"

fun D.foo() = "d"

fun printFoo(c: C) {
    println(c.foo())
}

printFoo(D())
```

This example will print "c", because the extension function being called depends only on the declared type of the
parameter `c`, which is the `C` class.

If a class has a member function, and an extension function is defined which has the same receiver type, the same name
and is applicable to given arguments, the **member always wins**.
For example:

``` kotlin
class C {
    fun foo() { println("member") }
}

fun C.foo() { println("extension") }
```

If we call `c.foo()` of any `c` of type `C`, it will print "member", not "extension".

However, it's perfectly OK for extension functions to overload member functions which have the same name but a different signature:

``` kotlin
class C {
    fun foo() { println("member") }
}

fun C.foo(i: Int) { println("extension") }
```

The call to `C().foo(1)` will print "extension".


## Nullable Receiver

Note that extensions can be defined with a nullable receiver type. Such extensions can be called on an object variable
even if its value is null, and can check for `this == null` inside the body. This is what allows you
to call toString() in Kotlin without checking for null: the check happens inside the extension function.

``` kotlin
fun Any?.toString(): String {
    if (this == null) return "null"
    // after the null check, 'this' is autocast to a non-null type, so the toString() below
    // resolves to the member function of the Any class
    return toString()
}
```

## Extension Properties

Similarly to functions, Kotlin supports extension properties:

``` kotlin
val <T> List<T>.lastIndex: Int
    get() = size - 1
```

Note that, since extensions do not actually insert members into classes, there's no efficient way for an extension 
property to have a [backing field](properties.html#backing-fields). This is why **initializers are not allowed for 
extension properties**. Their behavior can only be defined by explicitly providing getters/setters.

Example:

``` kotlin
val Foo.bar = 1 // error: initializers are not allowed for extension properties
```


## Companion Object Extensions

If a class has a [companion object](object-declarations.html#companion-objects) defined, you can also define extension
functions and properties for the companion object:

``` kotlin
class MyClass {
    companion object { }  // will be called "Companion"
}

fun MyClass.Companion.foo() {
    // ...
}
```

Just like regular members of the companion object, they can be called using only the class name as the qualifier:

``` kotlin
MyClass.foo()
```


## Scope of Extensions

Most of the time we define extensions on the top level, i.e. directly under packages:

``` kotlin
package foo.bar
 
fun Baz.goo() { ... } 
```

To use such an extension outside its declaring package, we need to import it at the call site:

``` kotlin
package com.example.usage

import foo.bar.goo // importing all extensions by name "goo"
                   // or
import foo.bar.*   // importing everything from "foo.bar"

fun usage(baz: Baz) {
    baz.goo()
)

```

See [Imports](packages.html#imports) for more information.

## Declaring Extensions as Members

Inside a class, you can declare extensions for another class. Inside such an extension, there are multiple _implicit receivers_ -
objects members of which can be accessed without a qualifier. The instance of the class in which the extension is declared is called
_dispatch receiver_, and the instance of the receiver type of the extension method is called _extension receiver_.

``` kotlin
class D {
    fun bar() { ... }
}

class C {
    fun baz() { ... }

    fun D.foo() {
        bar()   // calls D.bar
        baz()   // calls C.baz
    }

    fun caller(d: D) {
        d.foo()   // call the extension function
    }
}
```

In case of a name conflict between the members of the dispatch receiver and the extension receiver, the extension receiver takes
precedence. To refer to the member of the dispatch receiver you can use the [qualified `this` syntax](this-expressions.html#qualified).

``` kotlin
class C {
    fun D.foo() {
        toString()         // calls D.toString()
        this@C.toString()  // calls C.toString()
    }
```

Extensions declared as members can be declared as `open` and overridden in subclasses. This means that the dispatch of such
functions is virtual with regard to the dispatch receiver type, but static with regard to the extension receiver type.

``` kotlin
open class D {
}

class D1 : D() {
}

open class C {
    open fun D.foo() {
        println("D.foo in C")
    }

    open fun D1.foo() {
        println("D1.foo in C")
    }

    fun caller(d: D) {
        d.foo()   // call the extension function
    }
}

class C1 : C() {
    override fun D.foo() {
        println("D.foo in C1")
    }

    override fun D1.foo() {
        println("D1.foo in C1")
    }
}

C().caller(D())   // prints "D.foo in C"
C1().caller(D())  // prints "D.foo in C1" - dispatch receiver is resolved virtually
C().caller(D1())  // prints "D.foo in C" - extension receiver is resolved statically
```


## Motivation

In Java, we are used to classes named "\*Utils": `FileUtils`, `StringUtils` and so on. The famous `java.util.Collections` belongs to the same breed.
And the unpleasant part about these Utils-classes is that the code that uses them looks like this:

``` java
// Java
Collections.swap(list, Collections.binarySearch(list, Collections.max(otherList)), Collections.max(list))
```

Those class names are always getting in the way. We can use static imports and get this:

``` java
// Java
swap(list, binarySearch(list, max(otherList)), max(list))
```

This is a little better, but we have no or little help from the powerful code completion of the IDE. It would be so much better if we could say

``` java
// Java
list.swap(list.binarySearch(otherList.max()), list.max())
```

But we don't want to implement all the possible methods inside the class `List`, right? This is where extensions help us.
