# val과 var
## 설명
 * Kotlin에서 ```val```과 ```var``` 모두 변수를 선언할 때 사용
 * ```val```로 선언된 변수는 이후 <b>값 변경이 불가</b>
 * ```var```로 선언된 변수는 이후 값 변경이 가능

## 예제 코드
```kotlin
fun main() {
    val a: Int = 10
    var b: Int = 15
    val c = 20
    var d = 25
    // c = 30 (ERROR: Val cannot be reassigned)
    d = 30
    
    println("Hello, world!!!")
    println("a = " + a)
    println("b = " + b)
    println("c = " + c)
    println("d = " + d)
}
```

## 실행 결과
```
Hello, world!!!
a = 10
b = 15
c = 20
d = 30
```
