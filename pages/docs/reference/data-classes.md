---
type: doc
layout: reference
category: "Classes and Objects"
title: "Data Classes"
---

# Data Classes

우리는 많은 데이터를 보유하고 있지만 아무것도 할 수 없는 클래스를 자주 만듭니다.  이러한 클래스들의 일부 표준 기능은 데이터에서 기계적으로 끌어낼 수 있습니다.  Kotlin에서는 이러한 클래스를 _data class_ 라고하고 클래스 앞에`data class` 라고 명시합니다. 

``` kotlin
data class User(val name: String, val age: Int)
```

컴파일러는 _primary constructor_ 에 선언된 모든 속성에서 하위의 멤버들을 자동으로 파생시킵니다:

* `equals()`/`hashCode()` 한 쌍, 
  *  `"User(name=John, age=42)"` 와 같은 형식은  `toString()`,
  * 선언 순서대로 속성에 해당하는 [`componentN()` functions](multi-declarations.html) ,
  * `copy()` 함수 (아래서 보실 수 있습니다).

이러한 함수들이 클래스에 명시적으로정의 되어 있을 때나, base type에서 상속된 경우에는 생성되지 않습니다.

생성 된 코드의 일관성과 의미있는 동작을 보장하려면 데이터 클래스가 다음 요구 사항을 충족해야합니다:  (To ensure consistency and meaningful behavior of the generated code, data classes have to fulfil the following requirements.)

* primary 생성자는 최소 한개의 매개변수를 가져야 합니다.
  * 모든 primary 생성자의 매개변수는  `val` 이나 `var` 중 하나를 표시해야합니다. 
  * Data 클래스는 _abstruct_ , _open_ , _sealed_ 이거나 _inner_ 일 수 없습니다.
  * (1.1 이후) Data 클래스는 인터페이스로만 implement할 수 있습니다.

Since 1.1, data 클래스는 다른 클래스를 확장 할 수 있습니다. ( [Sealed classes](sealed-classes.html) 를 참고하시면 예제를 보실 수  있습니다 ).

 JVM에서 생성 된 클래스에 매개 변수없는 생성자가 필요한 경우 , 모든 속성에 대한 default values(기본값)을 지정해 주어야합니다. 
( [Constructors](classes.html#constructors) 내용에서 확인하실 수 있습니다).

``` kotlin
data class User(val name: String = "", val age: Int = 0)
```

## Copying

속성의 일부를 변경하여 복사하여야하는 경우가 종종있지만, 나머지는 변경하지않고 남겨놔야 합니다. 
이러한 이유로 `copy()` 함수가 생겨난 것 입니다. 위의 `User` 클래스의 구현은 아래와 같습니다:
``` kotlin
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)     
```

이는 우리가 아래와 같이 쓸 수 있게 해줍니다.

``` kotlin
val jack = User(name = "Jack", age = 1)
val olderJack = jack.copy(age = 2)
```

## Data Classes and Destructuring Declarations

_Component functions_  generated for data classes enable their use in [destructuring declarations](multi-declarations.html):

데이터 클래스 용으로 생성된 component functions들은 선언을 없애는데 사용할 수 있습니다.

``` kotlin
val jane = User("Jane", 35) 
val (name, age) = jane
println("$name, $age years of age") // prints "Jane, 35 years of age"
```

## Standard Data Classes

기본적으로 표준라이브러리는 `Pair` 와 `Triple`를 제공합니다. 하지만 대부분의 경우 명명 된 데이터 클래스는 속성에 의미있는 이름을 제공하여 코드를보다 쉽게 읽을 수 있도록하기 때문에 더 나은 코드의 선택입니다. 
