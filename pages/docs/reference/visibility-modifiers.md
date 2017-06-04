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
* `internal` —  선언한 클래스를 보는 *이 모듈 내부의*  클라이언트는 내부 멤버만 볼 수 있습니다.
* `public` — 클래스에 선언된 `public` 멤버는 누구든 볼 수 있습니다.

Java 사용자를 위한 *NOTE* :  Kotlin에서 외부 클래스는 내부 클래스의 `private` 멤버를 보지 못합니다.

만약 `protected` 멤버를 오버라이드 하거나 명시적으로 가시성을 지정하지 않는다면, 오버라이딩 되는 멤버는 `protected` 가시성을 가져야 합니다.

아래서 예시 보실 수 있습니다.:

``` kotlin
open class Outer {
    private val a = 1
    protected open val b = 2
    internal val c = 3
    val d = 4  // public 기본
    
    protected class Nested {
        public val e: Int = 5
    }
}

class Subclass : Outer() {
    // a 는 접근할 수 없습니다.
    // b, c 그리고 d 는 접근 가능합니다.
    // Nested 클래스와 e 는 접근 가능합니다.

    override val b = 5   // 'b'는 protected로 위에 선언되어있기 때문에 이것도 protected입니다.
}

class Unrelated(o: Outer) {
    // o.a, o.b are not visible
    // o.c and o.d are visible (same module)
    // Outer.Nested is not visible, and Nested::e is not visible either 
}
```

### 생성자 Constructors

클래스의 기본 생성자에 대한 표시 여부를 지정하려면 다음 구문을 사용합니다. (명시적으로  *constructor*{: .keyword } 키워드를 추가해야합니다 ):

``` kotlin
class C private constructor(a: Int) { ... }
```

위의 예제의 생성자는 `private` 입니다. 기본적으로는 모든 생성자는 `public` 이고,   클래스가 보이는 모든 곳에서 볼 수 있습니다. ( 즉, `internal` 클래스의 생성자는 동일한 모듈안에서만 볼 수 있습니다.)
​     
### 로컬 선언 Local declarations

로컬 변수, functions 그리고 classes는 가시성 수정자가 포함될 수 없습니다.


## 모듈 Modules

`internal` 가시성 수정자는 멤버가 동일한 모듈에서 볼 수 있음을 의미합니다. 보다 구체적으로 모듈은 함께 컴파일 된 Kotlin 파일 set입니다.

* IntelliJ IDEA 모듈;
  *  Maven 이나 Gradle project;
  *  <kotlinc> Ant 테스크를 한번 호출하여 컴파일된 파일 set.
