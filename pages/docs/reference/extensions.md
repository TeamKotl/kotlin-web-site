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

## Extensions의 정적으로 해결 Extensions are resolved **statically**

Extensions는 실제로 확장된 class를 수정하지않습니다. extension을 정의하게 되면, 클래스에 새로운 member를  insert 하지 않고, 이 유형의 변수에 점 표기법으로 호출 할 수있는 새로운 함수를 만들뿐입니다. 

kotlin은 extension기능을 **정적으로** 전달 된다는 점을 강조하고 싶습니다. 즉, receiver type에 따라 가상의 함수가 아닙니다.

이것은 호출되는 extension 함수는 런타임에 해당 표현식을 평가한 결과의 유형이 아니라 호출되는 함수의 표현식의 type에 따라 결정됩니다.

아래 예제를 보실 수 있습니다. 

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

위의 예제는 "c"를 결과로 print할 것입니다. extension 함수가 `C`클래스이고 생성한 매개변수 `c`의 선언된 유형에만 종속되기 때문에 `c`를 print하게 되는 것입니다.

클래스에 멤버 함수가 있고, 같은 receiver type과  같은 이름 및 주어진 인수에 적용할 수 있는 extension 함수가 정의 되어 있다면 **"member"는 항상 우선으로 출력됩니다.**
아래 예제를 보세요:

``` kotlin
class C {
    fun foo() { println("member") }
}

fun C.foo() { println("extension") }
```

`C` type의 `c`중 `c.foo()` 을 호출하면, "extension"이 아니라 "member"가 출력될 것입니다.

그러나 , 같은 이름을 가지지만 signature가 다른  extension 함수를 오버로드(overload) 하는것은 온전히 괜찮으니 걱정하지 마세요.

``` kotlin
class C {
    fun foo() { println("member") }
}

fun C.foo(i: Int) { println("extension") }
```

 `C().foo(1)`을 호출하면  "extension"이 호출됩니다 .


## Nullable Receiver

kotlin의 extension은 nullable receiver type으로 정의 될 수 있습니다. 이러한 extension은 값이 null인 경우에도 object(객체) 변수에서 호출할 수 있으며, 본문 코드 안에서  `this == null` 을 확인할 수 있습니다.
Kotlin에서는  여러분인 null체크를 하지않고 toString()을 호출할 수 있다는것 을 알 수 있습니다. (null 체크는 extension 함수에서 발생합니다. `Any?.toString()`)

아래 예제에서 확인하실 수 있습니다.

``` kotlin
fun Any?.toString(): String {
    if (this == null) return "null"
    // null체크 뒤에, 'this'는 non-null type으로 auto캐스팅됩니다. 그리고 toString() 아래 
    // Any 클래스의 멤버 함수를 확인합니다.
    return toString()
}
```

## Extension Properties

kotlin에서는 extension함수와 마찬가지로 extension properties를 제공합니다.

``` kotlin
val <T> List<T>.lastIndex: Int
    get() = size - 1
```

유의사항으로는 extension은 실제로 클래스에 insert되지 않기 때문에, extension property이  [backing field](properties.html#backing-fields) 가지는 효율적인 방법이 없다는것을 주의하셔야 합니다. 이것이 **extension properties가 초기화를(initalisers) 허락하지 않는 **이유입니다.  extension property 의 동작은 getters / setter를 명시 적으로 제공함으로써 정의 할 수 있습니다. 

아래 예시를 보시면 됩니다.

``` kotlin
val Foo.bar = 1 // error: extension property에는 초기화가 허용되지 않습니다.
```


## Companion 객체의 Extensions

클래스에 [companion object](object-declarations.html#companion-objects)가 정의 되어 있다면, companion 객체에 extension functions 와 extension properties를  정의할 수 있습니다.

``` kotlin
class MyClass {
    companion object { }  // "Companion"이 호출됩니다.
}

fun MyClass.Companion.foo() {
    // ...
}
```

companion 객체는 일반 멤버와 마찬가지로, 클래스의 이름만 수식어구(한정어)로 사용하여 호출할 수 있습니다.

``` kotlin
MyClass.foo()
```


## Extensions의 범위 Scope of Extensions

대부분의 extensions는 최상위 레벨, 즉 package바로 아래 정의합니다.

``` kotlin
package foo.bar
fun Baz.goo() { ... } 
```

extension을 package밖에서 사용하고 싶다면,   해당 extension이 있는 곳을 import해야합니다.

``` kotlin
package com.example.usage

import foo.bar.goo // importing all extensions by name "goo"
                   // or
import foo.bar.*   // importing everything from "foo.bar"

fun usage(baz: Baz) {
    baz.goo()
)
```

자세한 정보는  [Imports](packages.html#imports) 에서 확인할 수 있습니다.

## Extensions의 멤버 선언 Declaring Extensions as Members

클래스 안에서 다른 클래스의 extensioin을 선언할 수 있습니다.  이러한 extension에는 암시적인 receivers가 있는데, 이 receiver는 한정자없이 엑세스 할 수 있습니다.  extension이 선언 되어 있는  클래스의 인스턴스를  _dispatch receiver_ 라고 하고, extension method의 receiver 타입 인스턴스를 _extension receiver_ 라고 합니다.

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

위의 예제는 이름으로 인한 충돌이 발생합니다. 이렇게  dispatch receiver 와 the extension receiver가 이름 충돌이 발생하면 ,  extension receiver가 우선합니다. dispatch receiver의 멤버를 참조하려면  [qualified `this` syntax](this-expressions.html#qualified) 문서를 참조하시면 됩니다.

``` kotlin
class C {
    fun D.foo() {
        toString()         // calls D.toString()
        this@C.toString()  // calls C.toString()
    }
```

멤버로 선언된 Extensions은  `open`으로 선언 될 수도 있고 서브 클래스에서 override 될 수도 있습니다. 이러한 기능의 dispatch 의미는 dispatch receiver type과 관련하여 가상이지만 extension receiver type에 관하여 static합니다.(here)

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

여러분이 알고 계시는  Java에서는 "\*Utils" 클래스가 익숙하실 겁니다. `FileUtils`, `StringUtils` 등등과 같이 말이죠. 가장 잘 알려진  `java.util.Collections` 와 같은 클래스들 입니다. ( always getting in the way - 방해되다 )

자주 사용하지만 Utils-classes를 사용하면서 불편한 점은 아래 코드를 보시면 바로 아실 수 있을 겁니다.

``` java
// Java에서 사용되는 예입니다
Collections.swap(list, Collections.binarySearch(list, Collections.max(otherList)), Collections.max(list))
```

그 클래스 이름들은 항상 코드를 쓰는데 항상 방해 받고 있습니다. 여러분은 이제 static import를 사용하여 다음과 같이 사용할 수 있습니다.

``` java
// Java
swap(list, binarySearch(list, max(otherList)), max(list))
```

위의 방법으로도 훨씬 나아진것 일 수 있지만,  IDE의 강력한 코드 작성으로 인한 도움은 거의없거나 전혀 없습니다. 위와 같은 방법으로 한다면 훨씬 더 나아질 것입니다. (It would be so much better if we could say)ㄴ

``` java
// Java
list.swap(list.binarySearch(otherList.max()), list.max())
```


