# 변수의 null
## 설명
 * Kotlin에서 변수는 기본적으로 null 값을 가질 수 없지만, <b>변수 선언 시 자료형의 오른쪽에 ```?```를 붙이면</b> null 값을 가질 수 있음.
 * 함수의 인수의 자료형 및 반환되는 값의 자료형에서도 사용할 수 있음.

## 예제 코드
```kotlin
// 인수는 a:Int, b:Int이므로 null 불가능
// 반환값은 Int?이므로 null 가능
fun sumWithNull0(a: Int, b: Int): Int? {
    
    println("a = " + a + ", b = " + b)
    
    // a와 b가 모두 0이면 null을 반환
    if (a == 0 && b == 0) {
        return null
    } else {
        return a + b
    }
}

// 인수는 a:Int?, b:Int?이므로 null 가능
// 반환값은 Int?이므로 null 가능
fun sumWithNull1(a: Int?, b: Int?): Int? {
    
    println("a = " + a + ", b = " + b)
    
    // a와 b 중 하나라도 null이면 null을 반환
    if (a == null || b == null) {
        return null
    } else {
        return a + b
    }
}

fun main() {
    var a: Int = 100
    // a = null (ERROR: Null can not be a value of a non-null type Int)
    println(a)
    
    var b: Int? = null
    println(b)
    
    // 함수 실행 (a와 b가 모두 0이면 null을 반환)
    println("\na와 b가 모두 0이면 null")
    println("반환 : " + sumWithNull0(3, 5))
    println("반환 : " + sumWithNull0(0, 0))
    println("반환 : " + sumWithNull0(15, -15))
    // println("반환 : " + sumWithNull0(null, 0)) (ERROR: Null can not be a value of a non-null type Int)
    
    // 함수 실행 (a와 b 중 하나라도 null이면 null을 반환)
    println("\na와 b 중 하나라도 null이면 null")
    println("반환 : " + sumWithNull1(5, 20))
    println("반환 : " + sumWithNull1(5, null))
    println("반환 : " + sumWithNull1(null, 20))
    println("반환 : " + sumWithNull1(null, null))
}
```

## 실행 결과
```
100
null

a와 b가 모두 0이면 null
a = 3, b = 5
반환 : 8
a = 0, b = 0
반환 : null
a = 15, b = -15
반환 : 0

a와 b 중 하나라도 null이면 null
a = 5, b = 20
반환 : 25
a = 5, b = null
반환 : null
a = null, b = 20
반환 : null
a = null, b = null
반환 : null
```
