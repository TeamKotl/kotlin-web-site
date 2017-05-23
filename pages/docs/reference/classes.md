---
type: doc
layout: reference
category: "Syntax"
title: "Classes and Inheritance"
related:
    - functions.md
    - nested-classes.md
    - interfaces.md
---



# Classes and Inheritance ( 클래스와 상속 )

## Classes ( 클래스 )

Kotlin의 클래스는  *class*{: .keyword } 키워드를 사용하여 선언합니다.

``` kotlin
class Invoice {
}
```

클래스의 선언은 클래스 이름, 클래스 헤더 그리고 `{}` 중괄호에 둘러싸인 class body로 선언됩니다. 

(매개 변수를 지정하고, 기본 생성자 등등..) 헤더와 본문은 모두 선택 사항입니다.

클래스에 body가 없는 경우, 괄호는 생략 될 수 있습니다.

``` kotlin
class Empty
```



### Constructors ( 생성자 )

Kotlin의 클래스는  **primary constructor** (초기 생성자)  와  하나이상의 **secondary constructors** 보조 생성자  , 부차적인 생성자를 가질 수 있습니다.

기본 생성자는 클래스 헤더의 일부입니다. 그것은 클래스 이름 (및 선택적 유형 매개 변수) 이후에 나옵니다.

``` kotlin
class Person constructor(firstName: String) {
}
```



아래와 같이 기본 생성자가  어떤 annotations도 가지고 있지 않거나 수정사항이 보이지 않는 경우라면 ,  *constructor*{: .keyword }는 생략 될 수있습니다.

``` kotlin
class Person(firstName: String) {
}
```

기본 생성자는 코드를 포함 할 수 없습니다.

오직 *init*{: .keyword } 키워드가 접두어로 사용된 **initializer blocks** ( 초기화 블럭 ) 에  초기화 코드만 넣을 수 있습니다.

``` kotlin
class Customer(name: String) {
  init {
    logger.info("Customer initiallized with value ${name}")
  }
}
```

 

기본 생성자 매개변수는 초기화 블록에서 사용할 수 있습니다. 또한 클래스 본문에 선언된 속성이나 initializers (초기화)도 사용할 수 있습니다. 

``` kotlin
class Customer(name: String) {
  val customerKey = name.toUpperCase() // 대문자로 변경
}
```

 

사실, kotlin에는 기본 생성자에 속성을 선언하고 속성을 초기화하기 위한 더 간결한 구문을 사용할 수있습니다.

``` kotlin
class Person(val firstName: String , val lastName:String, var age:Int) {
  // ...
}
```

 

일반 속성과 거의 같은 방식으로, 기본 생성자에서 선언 된 속성은 변경가능한 *var*{: .keyword }이나 read-only 인 상수 *val*{: .keyword } 로 선언 할 수 있습니다.

만약 생성자가 annotations이나 수식어구 ( visibility modifiers )가 있다면,  *constructor*{: .keyword } 를 코드에 명시해야한다. 그리고 수식어구는 명시한 constructor앞에 있어야합니다.

``` kotlin
class Customer public @Inject constructor(name: String) { ... }
```

 더 자세한 사항을 알고싶으시다면 문서를 확인하세요. [Visibility Modifiers](visibility-modifiers.html#constructors).

 



#### Secondary Constructors ( 1개 이상의 생성자 )

클래스에는 constructor*{: .keyword } 를 앞에 붙인 **secondary constructors** ( 보조 생성자 ) 를 선언할 수도 있습니다.

``` kotlin
class Person {
    constructor(parent: Person) {
      parent.children.add(this)
   }
}
```



클래스에 기본 생성자가 있는경우 , 보조 생성자는 기본생성자에게  직접 또는 간접적으로 다른 보조 생성자를 통해서 위임해야합니다. 동일한 클래스의 다른 생성자로 위임할때  *this*{: .keyword } 를 사용하여 나타낼 수 있습니다.

``` kotlin
class Person(val name: String) {
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

만약 추상클래스가 아닌 클래스에서 어떤 생성자도 선언하지 않은경우 ( 기본 생성자나 보조 생성자), 인자가 없는 기본 생성자가 생성되게 됩니다.( 첫 번째 생성자는 `val name: String`만을 초기화할 수 있고, 2번째는 `name: String, parent:Person` 을 가지는 생성자이고, name은 키워드 이므로 보조생성자가 기존생성자로 넘겨주기 위해 this()키워드를 사용하여 정의하는것. )

생성자는 public으로 생성되게 됩니다. 만약 여러분이 클래스에 public한 생성자를 가지는것을 원하지 않는다면, private 로 지정한 기본 생성자가 아닌 빈생성자를 선언해야합니다. 

#### **`non-default visibility`*

#### **`The visibility of the constructor `*

``` kotlin
class DontCreateMe private constructor () {
}
```

>**NOTE** :  JVM에서는 기본생성자의 모든 매개변수가 모두 기본값을 가지고 있을 때, 컴파일러는 추가되는 매개변수가 없는 기본 생성자를 생성합니다.
>
>이는 매개 변수없는 생성자를 통해 클래스 인스턴스를 만드는 Jackson이나 JPA와 같은 라이브러리가있는 Kotlin을 사용하기가 더 쉽게 해줍니다.
>
>*Jackson* : [Jackson](https://github.com/FasterXML/jackson) 은 자바용 json 라이브러리로 잘 알려져 있지만 Json 뿐만 아니라 XML/YAML/CSV 등 다양한 형식의 데이타를 지원하는 data-processing 툴이다.
>
>JPA : **JPA(Java Persistent API)**
>
>```kotlin
>class Customer(val customerName: String = "")
>```
>
>{:.info}



### 클래스의 인스턴스 생성 ( Creating instances of classes )

클래스에서 인스턴스를 생성하기 위해서는, 우리가 평소 함수를 만들고 호출하는 것처럼 생성자를 호출하여야 합니다.

``` kotlin
val invoice = Invoice()

val customer = Customer("Joe Smith")
```

kotlin은 *new*{: .keyword } 를 가지고 있지 않습니다.

중첩된 인스턴스를 만들기와 내부 클래스 그리고 익명 내부 클래스는 문서를 참고해 주세요. [Nested classes](nested-classes.html)

### 클래스 멤버 ( Class Members )

클래스에 포함 가능한 것.

* 생성자와 초기화 블럭
* 함수 - [Functions](functions.html) 
* 속성 - [Properties](properties.html) 
* 중첩 및 내부 클래스 - [Nested and Inner Classes](nested-classes.html) 
* 오브젝트 선언 - [Object Declarations](object-declarations.html)




## 상속 ( Inheritance )

kotlin 의 모든 클래스는 공통적인  superclass `Any`를 가집니다. 이는 클래스의 supertype을 따로 선언하지 않은 클래스들에 default로 적용됩니다.

``` kotlin
class Example //supertype을 선언하지 않고, 암시적으로 Any를 상속받는다.
```

`Any` 는  `java.lang.Object`가 아닙니다.  특히, `Any`는 `equals()`, `hashCode()` and `toString()` 이외에는 다른 어떤 멤버도 가지고 있지 않습니다.
더 자세한 사항은  [Java interoperability](java-interop.html#object-methods) 세션에서 참조하시면 됩니다.

명시적으로 supertype을 선언하기 위해서는 , 클래스의 type을 콜론 앞에 명시해 주시면 됩니다.

``` kotlin
open class Base(p: Int)

class Derived(p: Int) : Base(p)
```

클래스에 기본 생성자가 있다면, 기본 type은 기본 생성자의 매개 변수를 사용하여 생성자에서 초기화 될수 있습니다. ( 또는 꼭 생성자에서 초기화 시켜야 할수도 있죠. ) 

하지만, 클래스에 기본 생성자가 없다면, 보조 생성자를 사용하여 기본 type을 초기화 해주면 됩니다.  *super*{: .keyword } 를 사용하거나 다른 생성자에 위임해서 기본 type을 초기화 해주면 됩니다.

위와 같은 경우, 다른 보조 생성자는 기본type의 다른 생성자를 호출할 수 있습니다.

``` kotlin
class MyView : View {
    constructor(ctx: Context) : super(ctx)

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs)
}
```



클래스의 *open*{: .keyword } annotation 은 java에서의 *final*{: .keyword } 과 반대의 의미입니다. 

 *open*{: .keyword } annotation 은  이 클래스가 다른 클래스에서 상속되어 재정의가 가능하도록 허용하는 것입니다.

#### 상속의 재 정의를 허용!!

기본적으로, 클래스를 상속로부터 상속받았을때는 kotlin의 모든 클래스가  final 로 선언됩니다. 

 [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html) 에서 해당 내용을 볼 수 있습니다.,
Item 17: *Design and document for inheritance or else prohibit it*. - 상속을 위한 설계 ,디자인 또는 금지할 것.



### 메서드 오버라이딩  ( Overriding Methods )

 앞에서도 언급되었듯이, kotlin에서는 명시적으로 분명하게 만들기에 집중합니다.

그리고 java를 그닥 좋아하지 않고, kotlin은 overridable  members와 재정의 를 위해 명시적인 annotation을 필요로 합니다.
(여기서는  *open*) 

``` kotlin
open class Base {
    open fun v() {}
    fun nv() {}
}
class Derived() : Base() {
    override fun v() {}
}
```

*override*{: .keyword } annotation 은 `Derived.v()` 를 위해 필요 합니다. 만약 `override` 키워드가 없다면, 컴파일이 되지 않습니다. 문법에러가 납니다.

작성한 메서드가  `Base.nv()`와 이름이 같은데   *open*{: .keyword } 이 annotation 되어있지 않거나  *override*{: .keyword }가 없다면, 서브 클래스에서 같은 이름을 가진 메소드를 선언하는 것은 규칙에 맞지 않습니다.

클래스에 *open*{: .keyword } annotation이 없는 final 클래스에서는  open members를 금지합니다.

*override*{: .keyword }로 지정된  member는 자체적으로( 기본적으로 ) open 되어져 있는것이고 , 서브 클래스에서 오버라이드 될 수 있을 것입니다. 만약 오버라이드 되는것을 막고싶다면  *final*{: .keyword } 키워드를 사용하면 되는겁니다.

``` kotlin
open class AnotherDerived() : Base() {
    final override fun v() {}
}
```



### 속성 오버라이딩 ( Overriding Properties ) 

속성을 오버라이딩 하는것은 메서드를 오버라이딩 했던 방식과 비슷합니다. 속성은 superclass 에서 정의 되는데 파생된 (?) 클래스에서 다시 선언되는 수퍼클래스의 속성은 *override * {: .keyword} 로 시작 되어야 합니다.그리고 compatible(호환되는 type) type을 가지고 있어야합니다.

선언 된 각 속성은 초기화가 있는 속성이나 getter 메서드가있는 속성으로 재정의 할 수 있습니다.

``` kotlin
open class Foo {
    open val x: Int get { ... }
}

class Bar1 : Foo() {
    override val x: Int = ...
}
```

우리는 `val` 이나  `var` 속성도 재정의 할 수 있습니다. 하지만 반대의 경우, 이는  `val` 속성이 근본적으로 getter 메소드를 선언하기 때문에 허용됩니다. 그리고 `var` 로 속성을 오버라이드하는 것은 추가적으로 파생 된 클래스에서 setter 메소드를 선언하게 됩니다.

기본 생성자에서 * override * {: .keyword} 를 속성 선언의 일부로 사용할 수 있습니다.

그럼 생성자에서 지정해놓은 값이였거나 선언하지 않았던 값을 재정의 할 수있는것이죠!!!

```kotlin
interface Foo{
  val count: Int
}

class Bar1(override val count: Int) : Foo

class Bar2 : Foo {
  	override var count: Int = 0
}
```



### 오버라이딩 규칙  (Overriding Rules)

kotlin에서 구현 상속은 다음 규칙에 의해 규제되는데,  만약 클래스가 직접 슈퍼클래스로부터 같은 멤버의 구현을 상속받는다면, 그 멤버들을 override하여 독자적으로 기능을 구현하여 제공할 해야합니다. (아마 상속된 것들중 하나를 사용하여... )

상속된 구현을 가지고 있는 supertype을 나타내기 위해 , *super*{: .keyword }  를 사용할 수있습니다.  예를 들어보면, `super<Base>`꺽쇠 괄호 안의 `super`유형의 이름 으로 나타낼 수 있습니다. 

``` kotlin
open class A {
    open fun f() { print("A") } //
    fun a() { print("a") }
}

interface B {
    fun f() { print("B") } // 인터페이스의 멤버는 `open`이 기본입니다.
    fun b() { print("b") }
}

class C() : A(), B {
    // 컴파일러는 override된 f() 를 요구합니다.
    override fun f() {
        super<A>.f() // call to A.f()
        super<B>.f() // call to B.f()
    }
}
```

위의 예제를보면  `A` 와`B` 클래스를 모두 상속받으면, `a()` 와  `b()`  는 아무 문제가 없다. 왜냐하면`C`는 이들 각각의 함수의 한 구현만을 상속 받기 때문이다.

하지만 `c` 로 부터 f() 는 두개의 구현 상속을 받고, 따라서 우리는`C`에서`f ()`를 오버라이드해야합니다. 그리고 모호성을 제거하도록  자체적인 구현을 제공하시면 됩니다.

## 추상 클래스  ( Abstract Classes )

A class and some of its members may be declared *abstract*{: .keyword }.
An abstract member does not have an implementation in its class.
Note that we do not need to annotate an abstract class or function with open – it goes without saying.

We can override a non-abstract open member with an abstract one

``` kotlin
open class Base {
    open fun f() {}
}

abstract class Derived : Base() {
    override abstract fun f()
}
```

## Companion Objects -  kotlin에서 클래스 내부 접근위한 Static 지정.?

In Kotlin, unlike Java or C#, classes do not have static methods. In most cases, it's recommended to simply use
package-level functions instead.

If you need to write a function that can be called without having a class instance but needs access to the internals
of a class (for example, a factory method), you can write it as a member of an [object declaration](object-declarations.html)
inside that class.

Even more specifically, if you declare a [companion object](object-declarations.html#companion-objects) inside your class,
you'll be able to call its members with the same syntax as calling static methods in Java/C#, using only the class name
as a qualifier.


## Sealed Classes ( 1.1 업데이트 되어있음 )

열거 형태로 자기 자신을 return이 가능하고, 다음과 같이 `class`와 `object`에 자기 자신을 `return`하는 클래스 형태를 제공합니다.

Sealed classes are used for representing restricted class hierarchies, when a value can have one of the types from a
limited set, but cannot have any other type. They are, in a sense, an extension of enum classes: the set of values
for an enum type is also restricted, but each enum constant exists only as a single instance, whereas a subclass
of a sealed class can have multiple instances which can contain state.

To declare a sealed class, you put the `sealed` modifier before the name of the class. A sealed class can have
subclasses, but all of them must be nested inside the declaration of the sealed class itself.

``` kotlin
sealed class Expr {
    class Const(val number: Double) : Expr()
    class Sum(val e1: Expr, val e2: Expr) : Expr()
    object NotANumber : Expr()
}
```

Note that classes which extend subclasses of a sealed class (indirect inheritors) can be placed anywhere, not necessarily inside
the declaration of the sealed class.

The key benefit of using sealed classes comes into play when you use them in a [`when` expression](control-flow.html#when-expression). If it's possible
to verify that the statement covers all cases, you don't need to add an `else` clause to the statement.

``` kotlin
fun eval(expr: Expr): Double = when(expr) {
    is Expr.Const -> expr.number
    is Expr.Sum -> eval(expr.e1) + eval(expr.e2)
    Expr.NotANumber -> Double.NaN
    // the `else` clause is not required because we've covered all the cases
}
```
