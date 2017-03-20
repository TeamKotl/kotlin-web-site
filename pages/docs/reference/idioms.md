 

# Idioms

코틀린에서는 랜덤 컬렌션과  idioms(어휘)가 자주 사용됩니다. 당신이 즐겨 사용하는 idioms가 있는 경우, contribute 해주세요! PR을 해주세요.

 

### DTOs 만들기 (POJOs/POCOs)

```kotlin
data class Customer(val name: String, val email: String)
```

다음과 같은 기능을 가진`Customer` 클래스를 제공합니다 .

 

- getters (and setters in case of *var*{: .keyword }s) for all properties
- `equals()`
- `hashCode()`
- `toString()`
- copy()
- `component1()`, `component2()`, ..., for all properties (see [Data classes](data-classes.html))



### Default values for function parameters

```kotlin
fun foo(a: Int = 0, b: String = "") { ... }
```

 

### Filtering a list ( 목록 필터링 )

```kotlin
val positives = list.filter { x -> x > 0 }
```

또는

```kotlin
val positives = list.filter { it > 0 }
```

### 문자열 Interpolation

```kotlin
println("Name $name")
```



### Instance 검사

```kotlin
when (x) {
    is Foo -> ...
    is Bar -> ...
    else   -> ...
}
```

### Traversing a map/list of pairs



```kotlin
for ((k, v) in map) {

    println("$k -> $v")

}
```

 

`k`,`v`는 어느 것이든 호출 할 수 있습니다.

 

### Using ranges

```kotlin
for (i in 1...100) {...} //closed range: includes 100

for (i in until 100) { .... } // half-open range: does not include 100

for (x in 2..10 step 2) { ... }

for ( x in 10 downTo 1) { .... } 

if (x in 1..10) { ... }
```

 

### Read-only list

```kotlin
val list = listOf("a", "b","c")
```

 

### Read-only map

```kotlin
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
```

 

### Accessing a map

```kotlin
println(map["key"])

map["key"] = value
```

 

### Lazy property

```kotlin
val p: String by lazy {

    // compute the string

}
```

 

### Extension Functions ( 확장 기능 )

```kotlin
fun String.spaceToCamelCase() { .... } 
"Convert this to camelcase".spaceToCamelCase()
```

 

### Creating a singleton ( 싱클톤 생성 )

```kotlin
object Resource {
    val name = "Name"
}
```

 

### If not null shorthand ( 널이 아닌경우 간단한 전달법 )

```kotlin
val files = File("Test").listFiles() 

println(files?.size) // 파일 크기는 ?
```



### If not null and else shorthand

```kotlin
val files  = File("Test").listFiles()
println(files?.size?: "empty")
```

 

### Executing a statement if null ( 실행중인 코드라인(문단)이 널이라면 )

```kotlin
val data = ...
val emaili = data["email"]? : throw IllegalStateException("Email is missing !!! ")
```

 

### Execute if not null (  null  이 아닌경우 실행 )

```kotlin
val data = ...
data?.let {

    ... // execute this block if not null

}
```

 

### Return on when statement ( Return 문)

```kotlin
fun transform(color: String): Int {

    return when (color) {

        "Red" -> 0

        "Green" -> 1

        "Blue" -> 2

        else -> throw IllegalArgumentException("Invalid color param value")

    }

}
```

 

### 'try/catch' expression

```kotlin
fun test() {
    val result = try {
        count()
    } catch (e: ArithmenticException) {
        throw IllefalStateException(e)
    }
    // Working with result
}
```

 

### 'if' expression

```kotlin
fun foo(param: Int) {
    val result = if (param == 1) {
        "one"
    } else if (param == 2) {
        "two"
    } else {
        "three"
    }
}
```

 

### Builder-style usage of methods that return `Unit` ( `Unit`을 반환하는 메서드의 작성 스타일 사용

```kotlin
fun arrayOfMinusOnes(size: Int): IntArray {
    return IntArray(size).apply { fill(-1) }
} 
```

 

### Single-expression functions ( 단일 표현식 함수 )

```kotlin
fun theAnswer() = 42
```

 

이는 아래와 같다.

```kotlin
fun theAnswer(): Int {

    return 42

}
```

 

이는 효과적으로 다른 idioms와 결합될 수 있고, 이는 곧 짧은 코드로 이어지게 됩니다. E.g. with the *when*{: .keyword }-expression:

```kotlin
fun transform(color: String): Int = when (color) {
    "Red" -> 0
    "Green" -> 1
    "Blue" -> 2
    else -> throw IllegalArgumentException("Invalid color param value")
}
```

 

### Calling multiple methods on an object instance ('with') , 객체 인스턴스에서 멀티 메서드 호출

```kotlin
class Turtle {
    fun penDown()
    fun penUp()
    fun turn(degrees: Double)
    fun forward(pixels: Double)
}

val myTurtle = Turtle()
with(myTurtle) { //draw a 100 pix square
    penDown()
    for(i in 1..4) {
        forward(100.0)
        turn(90.0)
    }
    penUp()
}
```



### Java 7's try with resources , 자바7 리소스 사용하기

```kotlin
val stream = Files.newInputStream(Paths.get("/some/file.txt"))
stream.buffered().reader().use { reader ->
    println(reader.readText())
}
```

 

### Convenient form for a generic function that requires the generic type information , 제네릭 타입 정보가 필요한 편리한 제네릭 형식 

```kotlin
//  public final class Gson {
//     ...
//  public <T> T fromJson(JsonElement json, Class<T> classOfT) throws JsonSyntaxException {
//     ...
inline fun <reified T: Any> Gson.fromJson(json): T = this.fromJson(json, T::class.java)
```

 

### Consuming a nullable Boolean ( nullable Boolean 사용 )

```kotlin
val b: Boolean? = ...

if (b == true) {
    ...
} else {
    // `b` is false or null
}
```
