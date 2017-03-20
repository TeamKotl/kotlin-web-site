---
type: doc
layout: reference
category: "Syntax"
title: "인터페이스"
---

# 인터페이스

코틀린에서 인터페이스는 자바 8과 매우 유사합니다. 인터페이스는 추상 메서드를 포함할 뿐만 아니라 메서드 구현도 가질 수 있습니다.
추상 클래스와의 차이점은 인터페이스는 상태를 가질 수 없다는 점입니다. 인터페이스는 프로퍼티를 가질 수 있지만 추상화가 필요하거나 접근자를 제공해야 합니다. 

Interfaces in Kotlin are very similar to Java 8. They can contain declarations of abstract methods, as well as method
implementations. What makes them different from abstract classes is that interfaces cannot store state. They can have
properties but these need to be abstract or to provide accessor implementations.

*interface*{: .keyword } 키워드를 사용하여 인터페이스를 정의합니다.

An interface is defined using the keyword *interface*{: .keyword }

``` kotlin
interface MyInterface {
    fun bar()
    fun foo() {
      // optional body
    }
}
```

## 인터페이스 구현

클래스나 오브젝트는 한 개 또는 그 이상의 인터페이스를 구현할 수 있습니다.

A class or object can implement one or more interfaces

``` kotlin
class Child : MyInterface {
    override fun bar() {
        // body
    }
}
```

## Properties in Interfaces

여러분은 인터페이스에 프로퍼티를 선언할 수 있습니다. 인터페이스에 선언 된 프로퍼티는 추상이거나 접근자를 위한 구현을 제공해야합니다.
인터페이스 의 프로퍼티는 backing 필드를 가질 수 없어서, 인터페이스에 선언한 접근자는 backing 필드를 참조할 수 없습니다.

You can declare properties in interfaces. A property declared in an interface can either be abstract, or it can provide
implementations for accessors. Properties declared in interfaces can't have backing fields, and therefore accessors
declared in interfaces can't reference them.

``` kotlin
interface MyInterface {
    val property: Int // abstract

    val propertyWithImplementation: String
        get() = "foo"

    fun foo() {
        print(property)
    }
}

class Child : MyInterface {
    override val property: Int = 29
}
```



## Resolving overriding conflicts

상위타입 리스트에 여러개의 타입을 선언하면, 같은 메서드에 대해 한개 이상의 인터페이스 구현을 상속받을 수 있습니다.

When we declare many types in our supertype list, it may appear that we inherit more than one implementation of the same method. For example

``` kotlin
interface A {
    fun foo() { print("A") }
    fun bar()
}

interface B {
    fun foo() { print("B") }
    fun bar() { print("bar") }
}

class C : A {
    override fun bar() { print("bar") }
}

class D : A, B {
    override fun foo() {
        super<A>.foo()
        super<B>.foo()
    }
}
```

*A*와 *B* 인터페이스가 *foo()*와 *bar()* 함수를 선언하고 있습니다. *foo()*는 두개의 인터페이스에서 모두 구현하고 있지만, *bar()*는 *B*에서만 구현하고 있습니다. (A에서 *bar()*는 abstract를 지정하지 않았는데, 
이는 인터페이스에서 함수가 본문을 가지고 있지 않을 경우, 기본으로 지정됩니다. 이제, 콘크리트 클래스 *C*가 *A*를 상속 받으면, 명백하게 *bar()*를 오버라이드 하고 구현을 제공해야 합니다.
그리고 *A*와 *B*를 상속받은 *D*는 단 하나의 구현만 상속받기 때문에 *bar()*를 오버라이드 할 필요가 없습니다. 하지만 *foo()*는 두개의 구현을 상속받기 때문에 컴파일러는 둘 중에서 무엇을 선택해야 하는지 알 수 없으므로, *foo()*를 명시적으로 강제 오버라이드 합니다.  

Interfaces *A* and *B* both declare functions *foo()* and *bar()*. Both of them implement *foo()*, 
but only *B* implements *bar()* (*bar()* is not marked abstract in *A*,
because this is the default for interfaces, if the function has no body).
Now, if we derive a concrete class *C* from *A*, we, obviously, 
have to override *bar()* and provide an implementation. 
And if we derive *D* from *A* and *B*,
 we don’t have to override *bar()*, because we have inherited only one implementation of it.
But we have inherited two implementations of *foo()*, 
so the compiler does not know which one to choose, 
and forces us to override *foo()* and say what we want explicitly.