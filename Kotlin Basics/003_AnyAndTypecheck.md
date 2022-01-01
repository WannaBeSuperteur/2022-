# Any와 변수의 자료형 확인
## 설명
 * Kotlin에서 변수가 어떤 자료형이든 가질 수 있도록 선언하는 경우 ```Any```를 사용
 * 함수의 인수의 자료형, 일반적으로 선언되는 변수의 자료형 등에서 사용할 수 있음
 * ```Any?``` 자료형 (```null``` 가능)을 사용할 수 있는데, 이때는 다음과 같은 제약 조건 존재
   * ```(Any?인 변수) = (Any인 변수)```와 같이 대입할 수 있음
   * 반대로, ```(Any인 변수) = (Any?인 변수)```와 같이 대입할 수는 없음
 * ```Any```로 선언된 변수 ```ANYVAR```에 대해 다음과 같이 특정 자료형인지 확인 가능
```kotlin
ANYVAR is DATATYPE // ANYVAR의 자료형이 DATATYPE이면 true
ANYVAR !is DATATYPE // ANYVAR의 자료형이 DATATYPE이 아니면 true
```

## 예제 코드
```kotlin
// variable이 Int 자료형이면 integer, String 자료형이면 string을 반환
// Int, String 중 어느 것도 아니면 not integer or string을 반환
fun getType(variable: Any): String {
    if (variable is Int) return "integer"
    else if (variable is String) return "string"
    else return "not integer or string"
}

fun main() {
    var anyValue: Any = "abc" // 처음에 String 자료형으로 선언
    
    println(anyValue)
    println("Int    자료형인가?      " + (anyValue is Int))
    println("Int    자료형이 아닌가? " + (anyValue !is Int))
    println("String 자료형인가?      " + (anyValue is String))
    println("String 자료형이 아닌가? " + (anyValue !is String))
    println("자료형: " + getType(anyValue) + "\n")
    
    anyValue = 123 // Any는 String 자료형 -> Int 자료형 가능
    
    println(anyValue)
    println("Int    자료형인가?      " + (anyValue is Int))
    println("Int    자료형이 아닌가? " + (anyValue !is Int))
    println("String 자료형인가?      " + (anyValue is String))
    println("String 자료형이 아닌가? " + (anyValue !is String))
    println("자료형: " + getType(anyValue) + "\n")
    
    var anyNotNull0: Any = 12
    var anyNotNull1: Any = 34
    var anyCanBeNull0: Any? = 56
    var anyCanBeNull1: Any? = 78
    
    anyCanBeNull1 = anyNotNull1 // Any? = Any 가능
    // anyNotNull0 = anyCanBeNull0 (ERROR: Type mismatch: inferred type is Any? but Any was expected)
}
```

## 실행 결과
```
abc
Int    자료형인가?      false
Int    자료형이 아닌가? true
String 자료형인가?      true
String 자료형이 아닌가? false
자료형: string

123
Int    자료형인가?      true
Int    자료형이 아닌가? false
String 자료형인가?      false
String 자료형이 아닌가? true
자료형: integer

```
