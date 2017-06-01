---
type: doc
layout: reference
category: "Classes and Objects"
title: "Visibility Modifiers"
---

# Visibility Modifiers

( Classes, objects, interfaces, constructors, functions, properties and their setters)

클래스, 객체, 인터페이스, 생성자, 함수, 속성 및 해당 setters는 가시성 수정자를 가질 수 있습니다. (Getters는 항상 속성과 동일한 Visibility 가시성을 가집니다.)

kotlin에는 4가지의 visibility modifiers 가 있습니다. : `private`, `protected`, `internal` 그리고 `public`
명시적인 modifier가 없는 경우 defult visibility는  `public` 입니다.

아래는 다양한 유형의 선언의 scope에 대한 설명입니다.

## 패키지 Packages

함수, 속성, 클래스, 오브젝트와 인터페이스 "top-level" 즉, "최상위"로 패키지 내부에 바로 선언할 수 있습니다. 

``` kotlin
// file name: example.kt
package foo

fun baz() {}
class Bar {}
```

* 특정 visibility modifier를 지정하지 않으시면, `public` 으로 기본 지정 됩니다. 이말은 어디서나 볼 수 있다는 말이죠.
* `private`로 지정하신다면, 선언이 포함된 파일 내부안에서만 볼 수 있습니다.
* `internal` 로 표시하신다면 , 동일한  [module](#modules) 의 모든 곳에서 볼 수 있습니다.
* `protected` 는 top-level, 최상위 선언에서는 사용할 수 없습니다.

Examples:

``` kotlin
// file name: example.kt
package foo

private fun foo() {} // example.kt 파일 내부에서 볼 수 있음

public var bar: Int = 5 // property(속성) 는 모든곳에서 볼 수 있습니다.
    private set         // setter는 example.kt에서만 사용할 수 있습니다.
    
internal val baz = 6    // 같은 module안에서만 볼 수 있습니다.
```



## 클래스 와 인터페이스 Classes and Interfaces

class내부에서 선언된 멤버인 경우:

* 클래스 내부에서 `private` 의 의미는 이 클래스에서만 접근가능하다는 의미입니다. (모든 멤버 포함) 
* `protected` ---  `private` 와 동일 + 하위 클래스에서도 볼 수 있습니다.
* `internal` —  선언한 클래스를 보는 *이 모듈 내부의* 클라이언트는 내부 멤버를 볼 수 있습니다.
* `public` --- any client who sees the declaring class sees its `public` members.

*NOTE* for Java users: outer class does not see private members of its inner classes in Kotlin.

If you override a `protected` member and do not specify the visibility explicitly, the overriding member will also have `protected` visibility.

Examples:

``` kotlin
open class Outer {
    private val a = 1
    protected open val b = 2
    internal val c = 3
    val d = 4  // public by default
    
    protected class Nested {
        public val e: Int = 5
    }
}

class Subclass : Outer() {
    // a is not visible
    // b, c and d are visible
    // Nested and e are visible

    override val b = 5   // 'b' is protected
}

class Unrelated(o: Outer) {
    // o.a, o.b are not visible
    // o.c and o.d are visible (same module)
    // Outer.Nested is not visible, and Nested::e is not visible either 
}
```

### Constructors

To specify a visibility of the primary constructor of a class, use the following syntax (note that you need to add an
explicit *constructor*{: .keyword } keyword):

``` kotlin
class C private constructor(a: Int) { ... }
```

Here the constructor is private. By default, all constructors are `public`, which effectively
amounts to them being visible everywhere where the class is visible (i.e. a constructor of an `internal` class is only 
visible within the same module).
​     
### Local declarations

Local variables, functions and classes can not have visibility modifiers.


## Modules

The `internal` visibility modifier means that the member is visible with the same module. More specifically,
a module is a set of Kotlin files compiled together:

* an IntelliJ IDEA module;
  * a Maven or Gradle project;
  * a set of files compiled with one invocation of the <kotlinc> Ant task.
