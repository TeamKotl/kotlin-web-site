---
type: doc
layout: reference
category: "Basics"
title: "기본 문법"
---

# 기본 문법

## 패키지 정의

패키지 명세는 소스 파일 상단에 위치해야합니다.

``` kotlin
package my.demo

import java.util.*

// ...
```

디렉토리와 패키지는 일치하지 않아도 됩니다. 소스파일은 파일 시스템 내 어느 장소에나 위치할 수 있습니다.  

자세한 내용은 [패키지](packages.html)를 참조하세요. 

## 함수 정의

두 개의 `Int`형 파라미터와 `Int`형 리턴 타입을 갖는 함수 :

``` kotlin
fun sum(a: Int, b: Int): Int {
    return a + b
}
```

식 몸체와 리턴 타입을 추론하는 함수 :

``` kotlin
fun sum(a: Int, b: Int) = a + b
```

의미 있는 값을 리턴하지 않는 함수 :

``` kotlin
fun printSum(a: Int, b: Int): Unit {
    print(a + b)
}
```

`Unit` 리턴 타입은 생략할 수 있습니다 :

``` kotlin
fun printSum(a: Int, b: Int) {
    print(a + b)
}
```

자세한 내용은 [함수](functions.html)를 참조하세요.

## 로컬 변수 정의

한 번만 대입할 수 있는 (읽기 전용) 로컬 변수:

``` kotlin
val a: Int = 1
val b = 1   // `Int` type is inferred
val c: Int  // Type required when no initializer is provided
c = 1       // definite assignment
```

변경가능한 변수

``` kotlin
var x = 5 // `Int` type is inferred
x += 1
```

자세한 내용은 [프로퍼티와 필드](properties.html)를 참조하세요.


## 주석

Java와 JavaScript처럼 Kotlin 역시 라인 주석과 블럭 주석을 지원합니다.

``` kotlin
// 이게 end-of-line (라인 주석) 입니다.

/* 이 것은 블록 주석 입니다.
   여러 줄로 작성할 수 있습니다. */
```

Java와는 다르게 Kotlin의 블록 주석은 중첩이 가능합니다.

주석 문법에 대한 자세한 내용은 [코틀린 코드의 문서화](kotlin-doc.html)를 참조하세요.

## 문자열 템플릿 사용

``` kotlin
fun main(args: Array<String>) {
    if (args.size == 0) return

    print("First argument: ${args[0]}")
}
```

자세한 내용은 [문자열 템플릿](basic-types.html#string-templates)를 참조하세요.

## 조건식 사용

``` kotlin
fun max(a: Int, b: Int): Int {
    if (a > b) {
        return a
    } else {
        return b
    }
}
```

*if*{: .keyword }를 식으로 사용:

``` kotlin
fun max(a: Int, b: Int) = if (a > b) a else b
```

자세한 내용은 [*if*{: .keyword }-식](control-flow.html#if-expression)를 참조하세요.

## null이 가능한 값 (nullable)을 사용하여 null 체크하기

레퍼런스에 null을 대입할 수 있다면, 반드시 *null*{: .keyword } 가능하다고 표시해주어야 합니다.

`str`이 정수를 갖지 않을 경우 null을 리턴합니다. :

``` kotlin
fun parseInt(str: String): Int? {
    // ...
}
```

nullable 값을 리턴하는 함수 사용 : 

``` kotlin
fun main(args: Array<String>) {
    if (args.size < 2) {
        print("Two integers expected")
        return
    }

    val x = parseInt(args[0])
    val y = parseInt(args[1])

    // `x * y`는 null이 들어갈 수 있으므로 에러를 발생시킵니다.
    if (x != null && y != null) {
        // x와 y는 null 체크 후 자동으로 non-nullable로 캐스팅 됩니다.
        // x and y are automatically cast to non-nullable after null check
        print(x * y)
    }
}
```

또는

``` kotlin
    // ...
    if (x == null) {
        print("Wrong number format in '${args[0]}'")
        return
    }
    if (y == null) {
        print("Wrong number format in '${args[1]}'")
        return
    }

    // x와 y는 null 체크 후 자동으로 non-nullable로 캐스팅 됩니다.
    print(x * y)
```

자세한 내용은 [널 안전성 (Null-Safety)](null-safety.html)를 참조하세요.

## 타입 검사와 자동 캐스팅

*is*{: .keyword } 연산자는 좌측이 우측의 타입 인스턴스인지 검사합니다.
불변의 로컬 변수 또는 프로퍼티를 *is*{: .keyword } 연산자로 특정 타입인지 검사했다면, 명시적으로 캐스팅 할 필요가 없습니다. : 

``` kotlin
fun getStringLength(obj: Any): Int? {
    if (obj is String) {
        // `obj`는 이 브랜치 내에서 `String`으로 자동 캐스팅 됩니다. 
        return obj.length
    }

    // `obj`는 타입을 검사한 브랜치 밖에서 여전히 `Any`로 남아있습니다.
    return null
}
```

또는

``` kotlin
fun getStringLength(obj: Any): Int? {
    if (obj !is String) return null

    // `obj`는 이 브랜치 내에서 `String`으로 자동 캐스팅 됩니다. 
    return obj.length
}
```

혹은

``` kotlin
fun getStringLength(obj: Any): Int? {
    // `&&` 오른쪽의 `obj`는 `String`으로 자동 캐스팅 됩니다.
    if (obj is String && obj.length > 0) {
        return obj.length
    }

    return null
}
```

자세한 내용은 [클래스](classes.html)와 [타입 캐스팅](typecasts.html)을 참조하세요.

## `for` 루프 사용

``` kotlin
fun main(args: Array<String>) {
    for (arg in args) {
        print(arg)
    } 
}
```

또는

``` kotlin
for (i in args.indices) {
    print(args[i])
} 
```

자세한 내용은 [for 루프](control-flow.html#for-loops)를 참조하세요.

## `while` 루프

``` kotlin
fun main(args: Array<String>) {
    var i = 0
    while (i < args.size) {
        print(args[i++])
    }  
}
```

자세한 내용은 [while 루프](control-flow.html#while-loops)를 참조하세요.

## `when` 식

``` kotlin
fun cases(obj: Any) {
    when (obj) {
        1          -> print("One")
        "Hello"    -> print("Greeting")
        is Long    -> print("Long")
        !is String -> print("Not a string")
        else       -> print("Unknown")
    }
}
```

자세한 내용은 [when 식](control-flow.html#when-expression)을 참조하세요.

## 범위 (range) 사용

*in*{: .keyword } 연산자를 사용하면 숫자가 범위 내에 있는지 검사할 수 있습니다. :

``` kotlin
if (x in 1..y-1) {
    print("OK")
}
```

숫자가 범위 밖에 있는지 검사하기 : 

``` kotlin
if (x !in 0..array.lastIndex) {
    print("Out")
}
```
범위 내에서 반복처리 :

``` kotlin
for (x in 1..5) {
    print(x)
}
```

자세한 내용은 [범위 (range)](ranges.html)를 참조하세요.

## 컬렉션 사용

컬렉션 내에서 반복처리 : 

``` kotlin
for (name in names) {
    println(name)
}
```

 *in*{: .keyword }로 컬렉션이 객체를 포함 하고 있는지 검사하기 :  

``` kotlin
if (text in names) { // names.contains(text) 가 호출 됨.
    print("Yes")
}
```

필터와 맵 컬렉션에 람다식을 사용하기 :

``` kotlin
names
        .filter { it.startsWith("A") }
        .sortedBy { it }
        .map { it.toUpperCase() }
        .forEach { print(it) }
```

자세한 내용은 [고차원 함수와 람다](lambdas.html)를 참조하세요
