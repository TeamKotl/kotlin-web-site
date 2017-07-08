---
type: doc
layout: reference
category: "Classes and Objects"
title: "Sealed Classes"
---

# Sealed Classes

Sealed 클래스는 값이 제한된 집합의 type중 하나를 가질 수있지만, 다른 type은  가질 수 없는 한정된 클래스의 계층구조를 나타내는데 사용됩니다. 

sealed클래스는 enum클래스의 확장입니다:  enum type의 값들의 집합 또한 제한되어있고, 각각 다른 enum 상수는 오직 싱글 인스턴스로만 존재하지만, sealed클래스의 하위클래스는 상태를 포함할 수 있는 여로 인스턴스를 가질 수 있습니다.  sealed클래스를 선언하기 위해서는, 클래스의 이름 앞에 `sealed` 라는 한정자를 넣어야 합니다. 

sealed클래스는 하위 클래스를 만들어 사용할 수 있지만, sealed클래스 자체와 동일한 파일 안에서 선언되어야 합니다. ( Kotlin 1.1 전에 이부분 규칙은 훨씬 더 엄격하였습니다. 클래스는 sealed 클래스의 선언안에 중첩되어야 했습니다.   ) 

``` kotlin
sealed class Expr
data class Const(val number: Double) : Expr()
data class Sum(val e1: Expr, val e2: Expr) : Expr()
object NotANumber : Expr()

fun eval(expr: Expr): Double = when (expr) {
    is Const -> expr.number
    is Sum -> eval(expr.e1) + eval(expr.e2)
    NotANumber -> Double.NaN
}
```

( 위에 사용된 예제는 Kotlin 1.1의 새로운 기능중에 하나인 데이터 클래스가 sealed 클래스를 포함하여 다른 클래스를 확장할 수 있도록 추가로 사용할 수 있게 되었습니다. )

주의할점은, sealed 클래스의 서브 클래스를 확장하는 클래스(간접적인 상속)은 어디에도 있을 수 있으며, 동일한 파일에 있지않아도 됩니다.

sealed 클래스를 사용하는데 가장 주요한 이점은   [`when` expression](control-flow.html#when-expression) 식에서 사용할 때 유용합니다. The key benefit of using sealed classes comes into play when you use them in a [`when` expression](control-flow.html#when-expression). `when`문이 모든 케이스를 다 폼함하는지 알 수 있다면, `else`절을 추가 작성할 필요가 없습니다.

``` kotlin
fun eval(expr: Expr): Double = when(expr) {
    is Expr.Const -> expr.number
    is Expr.Sum -> eval(expr.e1) + eval(expr.e2)
    Expr.NotANumber -> Double.NaN
    // the `else` clause is not required because we've covered all the cases
}
```
