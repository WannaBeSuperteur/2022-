# when 키워드
## 설명
 * ```when```은 ```if 문```을 중첩하는 것을 단순화하기 위한 키워드임.
 * 기본적으로 다음과 같이 사용할 수 있음.
```kotlin
when (ARGNAME) { // 변수 ARGNAME이 가리키는 변수에 대해,
  COND0 -> CODE0 // COND0 조건이 맞으면 CODE0 코드를 실행
  COND1 -> CODE1 // COND1 조건이 맞으면 CODE1 코드를 실행
  ...
  else -> ELSECODE // 아무 조건도 안 맞으면 ELSECODE 코드를 실행
}
```
 * 조건은 다음과 같이 지정할 수 있음.
   * ```VALUE``` : 변수의 값이 ```VALUE```인지 확인
   * ```is DATATYPE``` : 변수의 자료형이 ```DATATYPE```인지 확인
   * ```!is DATATYPE``` : 변수의 자료형이 ```DATATYPE```이 <b>아닌지</b> 확인
 * 일반적인 if 문과 비교하면 다음과 같음.
   * 일반적인 if 문은 다음과 같이 작성하지만, ```when``` 키워드 이용한 문은 위와 같이 작성함.

(특정 값과 일치하는지의 조건인 경우)
```kotlin
if (ARGNAME == COND0) CODE0 // COND0 조건이 맞으면 CODE0 코드를 실행
else if (ARGNAME == COND1) CODE1 // COND1 조건이 맞으면 CODE1 코드를 실행
...
else ELSECODE // 아무 조건도 안 맞으면 ELSECODE 코드를 실행
```

(특정 자료형인지의 조건인 경우)
```kotlin
if (ARGNAME COND0) CODE0 // COND0 조건이 맞으면 CODE0 코드를 실행
else if (ARGNAME COND1) CODE1 // COND1 조건이 맞으면 CODE1 코드를 실행
...
else ELSECODE // 아무 조건도 안 맞으면 ELSECODE 코드를 실행
```

## 예제 코드
```kotlin
// if 문을 사용한 ifTest와 동일 기능
// return하는 값이 없으므로 return할 값의 자료형을 지정하지 않음
fun whenTest(variable: Any) {
    when (variable) {
        1 -> println("one") // 1이면 "one"을 출력
        2 -> println("two") // 2이면 "two"를 출력
        "How are you" -> println("I am fine thank you") // "How are you"이면 "I am fine thank you"를 출력
        is Int -> println("datatype Int") // Int 자료형이면 "datatype Int"를 출력
        !is String -> println("not datatype String") // String 자료형이 아니면 "not datatype String"을 출력
        else -> println("unknown case") // 위의 어느 것도 해당되지 않으면 "unknown case"를 출력
    }
}

// when 키워드를 사용한 whenTest와 동일 기능
// return하는 값이 없으므로 return할 값의 자료형을 지정하지 않음
fun ifTest(variable: Any) {
    if (variable == 1) println("one")
    else if(variable == 2) println("two")
    else if(variable.equals("How are you")) println("I am fine thank you")
    else if(variable is Int) println("datatype Int")
    else if(variable !is String) println("not datatype String")
    else println("unknown case")
}

fun main() {
    
    // whenTest 함수 실행
    whenTest(1)
    whenTest(2)
    whenTest("How are you")
    whenTest(100) // Int 자료형이므로 "datatype Int"
    whenTest(3.14159) // float 또는 double이므로, 즉 String 자료형이 아니므로 "not datatype String"
    whenTest("str is unknown?") // String 자료형이므로 어느 조건에서 해당되지 않음. 따라서 "unknown case"를 출력
    println("\n")
    
    // ifTest 함수 실행 (whenTest와 동일 기능)
    ifTest(1)
    ifTest(2)
    ifTest("How are you")
    ifTest(100)
    ifTest(3.14159)
    ifTest("str is unknown?")
}
```

## 실행 결과
```
one
two
I am fine thank you
datatype Int
not datatype String
unknown case


one
two
I am fine thank you
datatype Int
not datatype String
unknown case
```
