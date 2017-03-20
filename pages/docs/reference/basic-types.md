---
type: doc
layout: reference
category : "syntax"
title : "기본 유형"
---
 
# 기본 유형
 
코틀린에서는  모든것이 오브젝트의 의미이고,  당신은 어느 변수에서건 멤버 함수와 속성을 호출 할 수 있습니다 . 구현이 최적화되어 있기 때문에 일부 유형에 내장되어 있지만, 사용자들은 보통 클래스처럼 생각하면 됩니다. 이 섹션에서 우리는 숫자, 문자, 부울 및 배열 등의 유형을 설명합니다.
 
## 숫자
 
코틀린 자바와 비슷한 방식으로 숫자를 처리하지만, 정확히 같지는 않습니다. 예를 들어, 숫자들에 대한 함축적인 확대 변환은없고, 리터럴은 경우에 따라 약간 다릅니다.
 
코틀린은 숫자를 나타내는데 다음과 같은 기본 유형을 제공합니다 (자바에 가까운):
 
| 타입    | 비트 길이  |
|--------|----------|
| Double | 64       |
| Float	 | 32       |
| Long	 | 64       |
| Int	 | 32       |
| Short	 | 16       |
| Byte	 | 8        |

위 표의 형식들은 코틀린에서는 숫자들이 아닙니다. 자바와는 다르게 모두 대문자를 씁니다.
 
### 리터럴 상수
 
리터럴 상수의 구성요소에는 다음과 같은 종류가 있습니다.
 
* 소수 :`123`
  * Long형의 문자 끝에 `L` 을 붙이면 됩니다. : `123L`
* 16진법 :  `0x0F`
* 바이너리 :    `0b00001011`
 
참고 : 8진법 리터럴은 지원되지 않습니다.
 
코틀린는 부동 소수점 숫자에 대한 기존의 표기법을 지원합니다 :
 
* 기본적인 Double 형 :`123.5`,`123.5e10`
* Float형의  문자 끝에 `f` 또는 `F` 를 붙이면 됩니다.  : `123.5f`
 
### 표현
 
우리가 nullable 숫자나, 제너릭을 포함시키지 않는다면 , 자바 플랫폼에서는 실제로 숫자들이 JVM 기본형식으로 저장됩니다.
후자의 경우 숫자들을 기본type 을 객체로 썼다.
 
기본type의 숫자들은 동일하게 유지되지 않습니다.
 
``` kotlin
val a: Int = 10000
print (a === a) // print 'true' 값을 비교할 때 '=='과 '==='으로 비교할 수 있습니다.
val boxedA: Int? = a val anotherBoxedA: Int? = a
print (boxedA === anotherBoxedA) // !!! print 'false' !!!
```
 
다른 한편으로는 같게 유지된다.
 
``` kotlin
val a: Int = 10000
print(a == a) // Prints 'true'
val boxedA: Int? = a
val anotherBoxedA: Int? = a
print(boxedA == anotherBoxedA) // Prints 'true'
```
 
### 명시적 형 변환
 
다른 표현 때문에 , 작은 유형은 더 큰것의 하위유형이 아닙니다. (Due to different representations, smaller types are not subtypes of bigger ones.)
그렇게 분류하게 된다면 문제가 생길지도 모릅니다.
 
``` kotlin
// Hypothetical code, does not actually compile:
val a: Int? = 1 // A boxed Int (java.lang.Integer)
val b: Long? = a // implicit conversion yields a boxed Long (java.lang.Long)
print(a == b) // Surprise! This prints "false" as Long's equals() check for other part to be Long as well
```
 
So not only identity, but even equality would have been lost silently all over the place. (그래서 정체성 뿐만 아니라 동일성까지도 모든 부분에서 조용히 사라질 수 있습니다.)
 
결론적으로, 작은 타입들은 암시적으로는 큰 타입으로 변환될 수 없습니다.
이것은 우리가 명시적으로 형변환하지 않고 `Int` 변수로  `Byte`'형식의 값을 할당 할 수 있다는 것을 의미합니다.
 
``` kotlin
val b: Byte = 1 // OK, literals are checked statically
val i: Int = b // ERROR
```
 
우리는 숫자를 확대하기 위해 명시적 형 변환을 사용할 수 있습니다.
 
``` kotlin
val i: Int = b.toInt() // OK : 명시적인 확대
```
 
코틀린에서는 형 변환을 위해서 다음의 타입을 제공합니다.
 
* `toByte() : Byte`  : Byte로 변환
* `toShort() : Short`   : Short 로 변환
* `toInt() : Int`   : Int 로 변환
* `toLong() : Long`   : Long 으로 변환
* `toFloat() : Float`   : Float 로 변환
* `toDouble() : Double`   : Double 로 변환
* `toChar() : Char`   : Char로 변환
 
암시적 형 변환의 없는것는 왜냐하면 형식은 문맥에서 유추 되고, 적절한 연산을 위해 산술 연산이 오버로드 되기때문에 암시적 형변환이 없는 경우는 드뭅니다. 다음 예시를 보세요.
 
``` kotlin
val l = 1L + 3 // Long + Int => Long
```
 
### 연산
 
Kotlin은 적절한 클래스의 멤버로 선언 된 숫자에 대한 산술 연산의 표준 집합을 지원합니다 (그러나 컴파일러는 호출을 해당 지침으로 최적화합니다).
[연산자 오버로딩](operator overloading.html)를 참조하십시오.
 
비트 연산으로, 특정한 문자는 없지만 중위 형태로 호출 할 수있는 메서드를 제공합니다. 예시는 다음과 같습니다.
 
```kotlin
val x = (1 shl 2) and 0x000FF000
```
 
비트 연산의 전체 목록은 다음과 같습니다 (`Int`와`Long`에만 사용 가능) 
 
* `shl(bits)` – signed shift left (Java's `<<`)
* `shr(bits)` – signed shift right (Java's `>>`)
* `ushr(bits)` – unsigned shift right (Java's `>>>`)
* `and(bits)` – bitwise and
* `or(bits)` – bitwise or
* `xor(bits)` – bitwise xor
* `inv()` – bitwise inversion


## 캐릭터 
 
문자는 유형 `Char`로 표시됩니다. 문자 유형은 숫자로 직접  사용할 수 없습니다.
 
```kotlin
fun check(c: Char) {
    if (c == 1) { // ERROR: incompatible types
        // ...
    }
}
```
 
문자 리터럴은 작은 따옴표로 나타냅니다. `'1'`
특수 문자는 백슬래시를 사용하여 이스케이프 처리할 수 있습니다.
다음 이스케이프 시퀀스가 ​​지원됩니다`\t`, `\b`, `\n`, `\r`, `\'`, `\"`, `\\` and `\$`.
`'\uFF00'` : 다른 문자를 인코딩하려면 유니 코드 이스케이프 시퀀스 구문을 사용하면 됩니다.
 
`Int`형은 명시적으로 숫자 변환을 할 수 있습니다 
 
``` kotlin
fun decimalDigitValue(c: Char): Int {
    if (c !in '0'..'9')
        throw IllegalArgumentException("Out of range")
    return c.toInt() - '0'.toInt() // Explicit conversions to numbers
}
```
 
숫자와 마찬가지로 , 문자도 nullable 참조가 필요하다면 감싸져 사용할 수 있습니다. 기존 타입은 감싸진 작업에 의해 보존되지는 않습니다.
 
## 부울
 
`Boolean` 타입은  논리 값을 나타내고, *사실*{: .keyword }과 *거짓*{: .keyword } 같은 두가지의 값이 있습니다.
 
Boolean타입 또한 nullable참조가 필요하다면 감싸져 사용 할 수 있습니다.
 
부울의 내장된 연산에는 다음이 포함됩니다.
 
* `||` – lazy disjunction
* `&&` – lazy conjunction
* `!` - negation
 
## 배열
 
코틀린에서 배열을 표현하는 것은  `Array` 클래스에 `get`와`set` 기능의 메서드와 `size` 속성, 그리고 몇 가지 다른 유용한 멤버함수들이 함께 있습니다.
 
```kotlin
class Array<T> private constructor {
    val size: Int
    fun get (index: Int): T
    fun set(index: Int, value: T): Unit
 
    fun iterator(): Iterator<T>
    //...
}
```
 
배열을 생성하기 위해, 라이브러리 함수`arrayOf()` 을 사용하고  항목 값을 전달할 수 있는데, `arrayOf (1, 2, 3)` 은 [1, 2, 3] 으로 배열을 생성하도록 할 수 있습니다.
대안으로, `arrayOfNulls()`라이브러리 함수는 null 요소 채워진 크기의 배열을  만드는 데 사용될 수있습니다. 
 
 또 다른 기능은  인덱스를 가지는 각 배열 요소의  크기와  초기 값을 리턴 할 수있는 factory  함수를 사용하는 것입니다. 

 
``` kotlin
// Creates an Array<String> with values ["0", "1", "4", "9", "16"]
val asc = Array(5, { i -> (i * i).toString() })
```

위에서 말했듯이,`[]` 연산은 작업이 멤버 함수 `get ()`와`set ()`을 호출합니다.
 
NOTE : 자바와는 달리, 코틀린의 배열은 불변입니다. 코틀린은  `Array<String>` 에서 `Array<Any>`로 할당 할 수 없음을 의미합니다
이는 가능한 런타임 오류는 방지합니다.
참조하세요.  [Type Projections](generics.html#type-projections)).
 
`ByteArray` : 코틀린는  오버해드를 boxing 하지않는 초기타입의 배열을 나타낼 수 있습니다.
: `ShortArray`,`IntArray` 가 그것입니다. 이러한 클래스들은  `Array`클래스에 상속관계가 없지만, 
그 클래스들은 도일한 메서드 및 속성을 가지고있습니다. 또한 그 클래스들은  각각은 대응하는 factory function을 갖습니다.
 
```kotlin
val x:IntArray = intArrayOf(1,2,3)
x[0] = x[1] + x[2]
```
 
## 문자열 (String)
 
문자열은 유형 `String`로 표시됩니다.  문자열은 불변입니다. 
`s[i]`: 문자열의 원소는 인덱싱 연산으로 접근 가능한 문자입니다.
문자열은 다음과 같이 반복 될 수 있습니다 - *for*{: .keyword }-loop:
 
```kotlin
for (c in str) {
    printIn(c)
}
```
 
### 문자열 리터럴
 
코 틀린 문자열 리터럴의 두 가지 유형이 있습니다. 이스케이프 처리 된 문자열과 이스케이프 처리 된 문자열 및 개행 문자 및 임의의 텍스트를 포함 할 수있는 원시 문자열입니다. 이스케이프 된 문자열은 Java 문자열과 매우 비슷합니다.
 
```kotlin
val s = "Hello, world!!!\n"
```
 
이스케이프는 백 슬래시로, 관용적인 방법으로 이루어집니다. 지원되는 이스케이프 시퀀스 목록은 위의 [Characters](#characters) 를 참조하십시오.
 
Raw 문자열은 이스케이핑을 포함하지 않고 개행과 다른 문자들을 포함하며 삼중 따옴표로 구분됩니다.
 
```kotlin
val text = """
    for (c int "foo")
        printIn(c)
"""
```
 
여러분은  함수 선행 공백을 제거 할 수 있습니다 . [`trimMargin ()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/trim-margin.html) 
 
```kotlin
val text = """
    |Tell me and I forget.
    |Teach me and I remember.
    |Involve me and I learn.
    |(Benjamin Franklin)
    """.trimMargin()
```
 
기본적으로 `|` 마진 접두사로 사용됩니다,하지만 당신은`trimMargin ( ">")`과 같은 다른 문자를 선택하고 매개 변수로 전달할 수 있습니다.
 
### 문자열 템플릿 
 
문자열은 그 결과를 문자열로 연결됩니다 템플릿 형식, 평가 코드, 즉 조각을 포함 할 수 있습니다.
템플릿 표현식은 달러 기호 ($)로 시작하거나 간단한 이름으로 구성됩니다.
 
``` kotlin
val i = 10
val s = "i = $i" // evaluates to "i = 10"
```
 
중괄호 또는 임의의 표현 
 
``` kotlin
val s = "abc"
val str = "$s.length is ${s.length}" // evaluates to "abc.length is 3"
```
 
템플릿은 raw 문자열 내부에 내부 이스케이프 문자열 모두 지원됩니다.
여러분이 raw 문자열 리터럴`$` 문자 (백 슬래시 이스케이프 지원하지 않는) 표현해야하는 경우엔, 다음과 같은 구문을 사용할 수 있습니다.
 
``` kotlin
val price = """
${'$'}9.99
"""
```