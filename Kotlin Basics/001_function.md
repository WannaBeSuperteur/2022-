# 함수
## 설명
Kotlin에서는 다음과 같이 함수를 정의하여 사용
```kotlin
fun FUNCNAME(ARGNAME0: ARGTYPE0, ARGNAME1: ARGTYPE1, ...) : RETURNTYPE {
  // code to execute
}
```
 * ```FUNCNAME```은 함수 이름
 * ```ARGNAME0```, ```ARGNAME1```, ...은 변수 이름
 * ```ARGTYPE0```, ```ARGTYPE1```, ...은 변수의 자료형(data type)
 * ```RETURNTYPE```는 반환할 변수의 형태

다음과 같이 함수를 비교적 간단한 형태로 만들어서 사용할 수도 있음.
```kotlin
fun FUNCNAME(ARGNAME0: ARGTYPE0, ARGNAME1: ARGTYPE1, ...) : RETURNTYPE = // formula for the value to return
```

```kotlin
fun FUNCNAME(ARGNAME0: ARGTYPE0, ARGNAME1: ARGTYPE1, ...) = // formula for the value to return
```

## 예제 코드
```kotlin
// 시간 * 시급 + 보너스
fun money(hour: Int, salaryPerHour: Int, bonus: Int): Int {
    return hour * salaryPerHour + bonus
}

// 시간 * 시급 + 보너스
fun moneySimple0(hour: Int, salaryPerHour: Int, bonus: Int): Int = hour * salaryPerHour + bonus

// 시간 * 시급 + 보너스
fun moneySimple1(hour: Int, salaryPerHour: Int, bonus: Int) = hour * salaryPerHour + bonus

fun main() {
    println(money(5, 10000, 3000)) // 5 * 10000 + 3000
    println(moneySimple0(6, 10000, 3000)) // 6 * 10000 + 3000
    println(moneySimple1(7, 10000, 3000)) // 7 * 10000 + 3000
}
```

## 실행 결과
```
53000
63000
73000
```
