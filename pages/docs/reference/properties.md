---
type: doc
layout: reference
category: "Syntax"
title: "Properties and Fields"
---

# Properties and Fields (속성과 필드)

## Declaring Properties ( 속성 선언 )

kotlin의 클래스는 속성을 가질 수 있고,  *var*{: .keyword } 키워드를 붙여 변경 가능하도록 선언할 수 있습니다. 그리고 *val*{: .keyword } 키워드를 붙여 읽기만 가능한 상수로 선언할 수도 있습니다.

``` kotlin
public class Address { 
  public var name: String = ...
  public var street: String = ...
  public var city: String = ...
  public var state: String? = ...
  publiv var zip: String = ...
}
```

속성을 사용하려면 Java의 필드 처럼 단순히 이름으로 참조하여 사용하면 됩니다.

``` kotlin
fun copyAddress(address: Address): Address {
    var result = Adress() // 여기에 new 키워드는 kotlin에서 필요하지 않아요
  result.name = address.name // accessors(접근자)가 불립니다.
  result.street = address.street
  
  //...
  
  return result
}
```



## Getters and Setters 

속성을 선언하기 위한 전체 구문은 다음과 같습니다.

``` kotlin
var <propertyName>: <propertyType> [= <property_initalizer]
   [<getter>]
   [<setter>]
```

초기화 (initalizer), getter 와 setter 는 선택사항입니다. 그리고 initalizer로부터 상속을 받거나 기본 클래스이 멤버에서 상속 될 수 있는 경우도 속성 type은 선택사항 입니다.

예시를 봅니다:

``` kotlin
var allByDefault: Int? //error: 명시적인 초기화가 필요하고, 기본적인 getter, setter 암시적 입니다(?).

var initalized = 1 //Int 타입을 가지고, getter와 setter가 기본입니다.
```



read-only 속성의 선언의 전체 구문은 변경이가능한 두가지 방법이 있다는 것에서 다릅니다. read-only의 속성은 `val`로 시작하고 setter를 허용하지 않습니다.

``` kotlin
val simple: Int? // Int 타입이고 기본적으로 getter를가지고 꼭 생성자에서 초기화해야합니다.
val inferredType = 1 // Int 타입을 가지고 기본적으로 getter를 가집니다.
```



우리는 (선언된 변수의) 오른쪽 속성의 선언 내에서 , 매우 일반적인 기능과 같은 사용자 접근자 (accessors)를 쓸 수 있습니다. 아래 custom (사용자 정의) getter 의 예를 보실 수 있습니다.

``` kotlin
val isEmpty: Boolean 
   get()= this.size == 0
```

custom (사용자 정의) setter 의 예는 아래와 같이 볼 수 있습니다.

``` kotlin
var stringRepresentation: String 
   get() = this.toString()
   set(value) {
      setDataFormString(value) //문자열을 파싱하고 다른 속성에 값을 할당합니다.
   }
```

규칙에 따라서 , setter 파라미터의 이름은 `value` 지만, 여러분은 원하는것이 다를 경우 다른 이름을 선택할 수 있습니다.

만약 여러분이 접근자(accessor)의 가시성을 변경하거나 annotation을 추가해야하는 경우에는, 기본적인 구현을 변경할 필요는 없습니다. 여러분은 아래의 예시처럼 body의 선언없이 접근자를 정의할 수 있습니다.

``` kotlin
var setterVisibility: String = "abc"
   private set // setter는 private 이며 기본적인 구현을 가집니다.(implementation)
```

### Backing Fields (백업 필드)

kotlin의 클래스에는 필드(fields)가 존재하지 않습니다. 하지만 때때로 custom 접근자가 를 사용하여 백업 필드(backing field)가 필요할 때가 있을 수 있습니다. 이러한 목적으로, kotlin은 `field` 식별자를 사용하여 접근이 가능한 백업 필드를 제공합니다

``` kotlin
var counter = 0 // 최기화된 값은 백업필드에서 직접 쓰여진다.
    set(value) {
      if(value >= 0) field = value
    }
```

`field` 접근자는 속성의 접근자에서만 사용되어 집니다.

접근자 중 적어도 하나 이상의 기본구현을 사용하거나  사용자 접근자(custom accessor)가 필드 실별자를 통해 참조하는 경우 속성에 대한 백업 필드가 생성됩니다.

예를들면, 아래와 같은 케이스의 경우에는 백업필드가 없습니다.

``` kotlin
val isEmpty: Boolean
    get() = this.size == 0
```

### Backing Properties

If you want to do something that does not fit into this "implicit backing field" scheme, you can always fall back to having a *backing property*:

``` kotlin
private var _table: Map<String, Int>? = null
public val table: Map<String, Int>
    get() {
        if (_table == null) {
            _table = HashMap() // Type parameters are inferred
        }
        return _table ?: throw AssertionError("Set to null by another thread")
    }
```

In all respects, this is just the same as in Java since access to private properties with default getters and setters is optimized so that no function call overhead is introduced.


## Compile-Time Constants

Properties the value of which is known at compile time can be marked as _compile time constants_ using the `const` modifier.
Such properties need to fulfil the following requirements:

* Top-level or member of an `object`
  * Initialized with a value of type `String` or a primitive type
  * No custom getter

Such properties can be used in annotations:

``` kotlin
const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"

@Deprecated(SUBSYSTEM_DEPRECATED) fun foo() { ... }
```


## Late-Initialized Properties

Normally, properties declared as having a non-null type must be initialized in the constructor.
However, fairly often this is not convenient. For example, properties can be initialized through dependency injection,
or in the setup method of a unit test. In this case, you cannot supply a non-null initializer in the constructor,
but you still want to avoid null checks when referencing the property inside the body of a class.

To handle this case, you can mark the property with the `lateinit` modifier:

``` kotlin
public class MyTest {
    lateinit var subject: TestSubject

    @SetUp fun setup() {
        subject = TestSubject()
    }

    @Test fun test() {
        subject.method()  // dereference directly
    }
}
```

The modifier can only be used on `var` properties declared inside the body of a class (not in the primary constructor), and only
when the property does not have a custom getter or setter. The type of the property must be non-null, and it must not be
a primitive type.

Accessing a `lateinit` property before it has been initialized throws a special exception that clearly identifies the property
being accessed and the fact that it hasn't been initialized.

## Overriding Properties

See [Overriding Properties](classes.html#overriding-properties)

## Delegated Properties

The most common kind of properties simply reads from (and maybe writes to) a backing field. 
On the other hand, with custom getters and setters one can implement any behaviour of a property.
Somewhere in between, there are certain common patterns of how a property may work. A few examples: lazy values,
reading from a map by a given key, accessing a database, notifying listener on access, etc.

Such common behaviours can be implemented as libraries using [_delegated properties_](delegated-properties.html).