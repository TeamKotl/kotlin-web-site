---
type: doc

layout: reference

category: "Syntax"

title: "Control Flow"

---

 

# Control Flow ( 제어 흐름 )

 

## If Expression

 

Kotlin 에서  *if* {: .keyword} 표현식은 반환값을 가집니다.

따라서 삼항연사자는 쓰지않아도 됩니다.(condition ? then : else), 왜냐하면 보통의 경우  *if*{: .keyword } 이 표현식으로도 잘 사용할 수 있기 때문입니다.

 

```kotlin

// Traditional usage ( 전통적인 사용 )

var max = a

if (a < b) max = b

 

// With else

var max: Int

if (a > b) {

    max = a

} else {

    max = b

}

 

// As expression ( 표현식으로 )

val max = if (a > b) a else b
```

 

*if*{: .keyword } 문이 여러 block을 사용할 수 있고, 마지막 표현식은 block 의 값입니다

 

``` kotlin

val max = if (a > b) {

    print("Choose a")

    a

} else {

    print("Choose b")

    b

}

```

 

다른 표현식을 사용하기 보다 *if*{: .keyword } 표현을 사용한다면,  (예를들어, 값을 반환하거나 변수에 할당 할 때) 

'else' 와같은 표현이 필요합니다.

 

문법을 참고할 수 있습니다.  [grammar for *if*{: .keyword }](grammar.html#if).

 

## When Expression

 

*when*{: .keyword } 표현은 c언어의 연산자 같은 switch문을 대신해서 사용할 수 있습니다.  가장 간단한 형태로는 다음과 같습니다.



```
when(x) {

    1 -> print("x == 1")

    2 -> print("x == 2")

    else -> { // Note the block

        print("x is neither 1 nor 2")

    }

}
```

*when*{: .keyword } 은 일부 분기의 조건이 충족 될 때까지 모든 조건 분기에 대해 해당 인수를 순차적으로 찾습니다. 

*when*{: .keyword }은 표현식 또는 명령문으로 사용할 수 있습니다. 이는 표현식으로 사용되는 경우 , 분기의 만족한 값은 전체 표현식의 값이 됩니다.

이것을 명령문으로 사용하는 경우, 각각의 분기는 무시되게 됩니다.

( *if*{: .keyword }, if 분기문 처럼, 각 분기와 값은 블록이 될 수 있습니다. 그값은 블록의 마지막 표현식의 값입니다. )



*else*{: .keyword } 문은 만약 다른 분기문이 조건을 충족시키지 못한경우 실행됩니다.

*when*{: .keyword } 문이 표현식으로 사용되어질때 , *else*{: .keyword } 분기문은 필수 입니다.

컴파일러를 사용하지 않는 한은, 모든 경우의 분기문 조건들로 다뤄진다는걸 증명할 수 없습니다.

 

많은 경우가 동일한 방식으로 이루어지게 된다면, 분기문의 조건들은 (,) 콤마로 합칠 수 있을 것입니다. 

 

```kotlin
when (x) {

    0, 1 -> print("x == 0 or x == 1")

    else -> print("otherwise")

}
```

 

우리는 분기문의 조건들로 임의의 표현식을 사용할 수 있습니다. (지속가능한 것이 아닌.) 

 

``` kotlin
when (x) {

    parseInt(s) -> print("s encodes x")

    else -> print("s does not encode x")

}
```

 

우리는 또한 *in*{: .keyword } 이나 *!in*{: .keyword } a [range](ranges.html) (링크 참조) 이거나 a collection 입니다.:

 

``` kotlin
when (x) {

    in 1..10 -> print("x is in the range")

    in validNumbers -> print("x is valid")

    !in 10..20 -> print("x is outside the range")

    else -> print("none of the ")

}
```

값을 체크할 수 있는 또 다른 방법으로는 특정한 타입인 *is*{: .keyword } 나 *!is*{: .keyword } 가 있습니다. 유의 사항으로는,

`smart casts` 형식 추론 때문에 , [smart casts](typecasts.html#smart-casts) (참고 링크) 여러분은 메소드에 접근할 수 있고 타입의 명시없이 속성을 정의 할 수 있습니다.

나머지 내용을 확인해 봅시다.

 

```kotlin
val hasPrefix = when(x) {

    is String -> x.startsWith("Prefix")

    else -> false

}
```

*when*{: .keyword } 문은 *if*{: .keyword }-*else*{: .keyword } *if*{: .keyword } 분기문으로 대체되어 사용될 수 있습니다.

인수가 있지 않은 경우는 -> 분기 조건이 단순 boolean 표현식이거나, 조건이 참인 경우일 때 분기가 실행됩니다. :

 

``` kotlin
when {

    x.isOdd() -> print("x is odd")

    x.isEven() -> print("x is even") 

    else -> print("x is funny")

}
```

when 문법 링크를 참고하세요. [grammar for *when*{: .keyword }](grammar.html#when).

 

## For Loops ( 루프 )

*for*{: .keyword } 문은 iterator를 제공하는 모든것을 통해 루프를 반복시킵니다. 구문은 다음과 같습니다.



``` kotlin
for (item in collection) print(item) 
```

body는 block될 수 있습니다. 

``` kotlin
for (item: Int in ints) {

    // ...

}
```

위에서 언급한것과 같이 , *for*{: .keyword } 문은 iterator에서 제공하는 모든것을 통해 반복할 수 있습니다. 즉,

 

* member 또는 extension-function `iterator()`의 반환 타입을 가진다.
* member 또는 extension-function `next()` , and
* member 또는 extension-function `hasNext()` 는 `Boolean` 리턴 값을 가진다.

 

이 세가지의 function 모두 `operator`로 표시해야 합니다.

`for` 문 배열을 반복하는 것은 iterator 객체를 생성하지 않고 인덱스 기반의 loop로 컴파일 됩니다.

당신이 리스트의 배열이나 리스트의 인덱스로 반복문을 돌리고 싶다면, 아래 예제와 같은 방식으로 하실 수 있습니다.

 

``` kotlin
for ( i in array.indices) {

    print(array[i])

}
```

이점에 유의하십시오. "범위가 정해진 반복"은 최종 실행까지 추가 객체를 생성하지않고 컴파일 됩니다.



다른 방법으로는`withIndex` 라이브러리 기능을 사용할 수 있습니다. :

``` kotlin
for ((index, value) in array.withIndex()) {

    println("the element at $index is $value")

} 
```

문법을 참고하세요. [grammar for *for*{: .keyword }](grammar.html#for).



## While Loops ( while문 ) 

*while*{: .keyword } 과 *do*{: .keyword }..*while*{: .keyword } 보통의 언어에서 볼 수 있듯이 사용됩니다. 

 

``` kotlin
while(x > 0) {

    x--

}

 

do {

    val y = retrieveData()

}while(y != null) // 여기서 y를 볼 수 있습니다!
```

문법을 참고하세요. [grammar for *while*{: .keyword }](grammar.html#while).

 

## Break and continue in loops (break, continue문)

코틀린은 통상적인 *break*{: .keyword } 와 *continue*{: .keyword } 루프 연산자를 지원합니다 . 문법을 참고하세요. [Returns and jumps](returns.html).

 

