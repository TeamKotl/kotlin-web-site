---
type: doc
layout: reference
category: "Syntax"
title: "Returns and Jumps"
---

# Returns and Jumps ( 반환 및 분기 )

코틀린에는 3가지 구조의 jump 연산자가 있습니다.

* *return*{: .keyword } : 가장 가까운 함수를 기본적인 반환을 하거나 Anonymous 함수로 반환합니다.[anonymous function](lambdas.html#anonymous-functions).
* *break*{: .keyword } : 가장 가까운 바깥 쪽 루프를 종료합니다.
* *continue*{: .keyword } : 가장 가까운 바깥 쪽 루프의 다음 단계로 진행합니다.

## Break and Continue Labels (부호)

Kotlin의 표현식은 *label*{: .keyword } 과 같이 표현될 수 있습니다.
Label 은 식별자의 형식을 따릅니다. 예를들어 `abc@`, `fooBar@` 과 같은 것이 유효한 label 입니다. (문법 참고 [grammar](grammar.html#label)).
표현식 label 을 지정하려면, `@` 앞에 label을 넣으면 됩니다.

``` kotlin
@loop for ( i on 1..100) {
    // ...
}
```

이제, *break*{: .keyword }나  *continue*{: .keyword }를 label로 지정해 함께 쓸 수 있습니다. 

``` kotlin
loop@ for (i in 1..100) {
    for (j in 1..100) {
        f(...) break@loop
    }
}
```

*break*{: .keyword }로 지정된 label은 해당 label로 표시된 루프 직후 실행 지점으로 점프됩니다.
*continue*{: .keyword }는 다음 반복 루프로 진행됩니다.


## Return at Labels

함수 리터럴, local 함수 및 object 표현식으로, 함수는 kotlin에서 중첩 될 수 있습니다. 
*return*{: .keyword }는 외부 함수에서 return 되도록 하는 권한이 있습니다. 
가장 중요한 사용 예는 람다식에서의 반환입니다 아래와 같이 쓸 때 사용합니다.

``` kotlin
fun foo() {
    ints.forEach {
        if (it == 0) return
        print(it)
    }
}
```

*return*{: .keyword } 표현식은 가장 가까운 바깥 쪽 함수에서 반환됩니다. 예를들어 아래 `foo` 함수를 보시면,
이러한 로컬이 아닌 반환값은 전달된 람다식에서만 지원됩니다. 문법 참고 [inline functions](inline-functions.html).)
람다식으로 반환해야 할 경우, 여러분은 *return*{: .keyword }에 label을 붙여 처리하여 권한을 부여해야 합니다.

``` kotlin
fun foo() {
    ints.forEach lit@ {
        if (it == 0) return @lit
        print(it)
    }
}
```

이제, 위의 코드 `return @lit` 표현은 람다 표현식에서만 return됩니다. 때때로는 implicits(명시적인) label 을 사용하는 것이 더 편리할 수 있습니다.
이러한 label은 람다가 전달되는 함수와 같은 이름을 가지고있습니다.

``` kotlin
fun foo() {
    ints.forEach {
        if (it==0) return@forEach
        print(it)
    }
}
```

또한, 람다식을 대신하여 익명함수 (Anonymous function) 를 사용 할 수도 있습니다. [anonymous function](lambdas.html#anonymous-functions).
익명 함수의 *return*{: .keyword } 표현식은 anonymous function 자체에서 반환됩니다.

``` kotlin
fun foo() {
    ints.forEach(fun(value : Int) {
        if(value == 0)return
        print(value)
    })
}
```

값을 반환하는 경우, parser는 권한이 있는 return문 부터 순서를 우선시합니다.

``` kotlin
return@a 1
```
위 코드의 `return@a 1` 은 ,
" `@a` label에서 return `1`" 이 반환된다는 뜻이 아니라 "return a labeled expression `(@a 1)`" label이 지정된 표현식을 반환하는 것입니다,.
