## 요구사항 분석
문자열이 오토마타에 의해 승인되는지 판단하는 함수 ```isFinal(string, rules, finalStates)```를 작성하시오.
* ```string``` : 승인 여부를 판단할 문자열
* ```rules``` : 오토마타의 규칙 (상태, 문자1, 전이상태1, 문자2, 전이상태2, ...)
* ```finalStates``` : 승인 상태의 집합

예를 들어 ```isFinal("aaaabbbb", [[0, 'a', 1, 'b', 2], [1, 'a', 1, 'b', 2], [2, 'a', 3, 'b', 2]], [1, 2])```의 값은 true이다.

단, 초기 상태는 무조건 0이다.

## 코드 및 실행 결과
### 코드
```kotlin
// 승인 상태 판정
fun isFinal(string: String, rules: List<List<Any>>, finalStates: List<Int>) {
    var currentState = 0
    println("초기 문자 : ${string[0]}, 초기 상태 : 0")
    
    string.forEach {
        
        // 현재 상태에 해당하는 규칙 찾기
        val currentRule = rules.find { it[0] == currentState }!!
        
        // 현재 문자에 따라 전이하기
        val movements = currentRule.subList(1, currentRule.size).chunked(2)
        val movementRule = movements.find { m -> m[0] == it }!!
        currentState = movementRule[1] as Int
        
        println("현재 문자 : ${it}, 새로운 상태 : ${currentState}")
    }
    
    val isFinal = currentState in finalStates
    println("\n문자열 ${string} 승인 상태 여부 : ${isFinal}\n")
}

fun main() {
    isFinal(
        "aaaabbbb",
        listOf(listOf(0, 'a', 1, 'b', 2), listOf(1, 'a', 1, 'b', 2), listOf(2, 'a', 3, 'b', 2), listOf(3, 'a', 3, 'b', 3)),
        listOf(1, 2)
    )
    isFinal(
        "aaaaaabbbbbbbba",
        listOf(listOf(0, 'a', 1, 'b', 2), listOf(1, 'a', 1, 'b', 2), listOf(2, 'a', 3, 'b', 2), listOf(3, 'a', 3, 'b', 3)),
        listOf(1, 2)
    )
    isFinal(
        "abababab",
        listOf(listOf(0, 'a', 1, 'b', 2), listOf(1, 'a', 3, 'b', 2), listOf(2, 'a', 1, 'b', 3), listOf(3, 'a', 3, 'b', 3)),
        listOf(1, 2)
    )
    isFinal(
        "aabbaabb",
        listOf(listOf(0, 'a', 1, 'b', 2), listOf(1, 'a', 3, 'b', 2), listOf(2, 'a', 1, 'b', 3), listOf(3, 'a', 3, 'b', 3)),
        listOf(1, 2)
    )
}
```

### 실행 결과
```kotlin
초기 문자 : a, 초기 상태 : 0
현재 문자 : a, 새로운 상태 : 1
현재 문자 : a, 새로운 상태 : 1
현재 문자 : a, 새로운 상태 : 1
현재 문자 : a, 새로운 상태 : 1
현재 문자 : b, 새로운 상태 : 2
현재 문자 : b, 새로운 상태 : 2
현재 문자 : b, 새로운 상태 : 2
현재 문자 : b, 새로운 상태 : 2

문자열 aaaabbbb 승인 상태 여부 : true

초기 문자 : a, 초기 상태 : 0
현재 문자 : a, 새로운 상태 : 1
현재 문자 : a, 새로운 상태 : 1
현재 문자 : a, 새로운 상태 : 1
현재 문자 : a, 새로운 상태 : 1
현재 문자 : a, 새로운 상태 : 1
현재 문자 : a, 새로운 상태 : 1
현재 문자 : b, 새로운 상태 : 2
현재 문자 : b, 새로운 상태 : 2
현재 문자 : b, 새로운 상태 : 2
현재 문자 : b, 새로운 상태 : 2
현재 문자 : b, 새로운 상태 : 2
현재 문자 : b, 새로운 상태 : 2
현재 문자 : b, 새로운 상태 : 2
현재 문자 : b, 새로운 상태 : 2
현재 문자 : a, 새로운 상태 : 3

문자열 aaaaaabbbbbbbba 승인 상태 여부 : false

초기 문자 : a, 초기 상태 : 0
현재 문자 : a, 새로운 상태 : 1
현재 문자 : b, 새로운 상태 : 2
현재 문자 : a, 새로운 상태 : 1
현재 문자 : b, 새로운 상태 : 2
현재 문자 : a, 새로운 상태 : 1
현재 문자 : b, 새로운 상태 : 2
현재 문자 : a, 새로운 상태 : 1
현재 문자 : b, 새로운 상태 : 2

문자열 abababab 승인 상태 여부 : true

초기 문자 : a, 초기 상태 : 0
현재 문자 : a, 새로운 상태 : 1
현재 문자 : a, 새로운 상태 : 3
현재 문자 : b, 새로운 상태 : 3
현재 문자 : b, 새로운 상태 : 3
현재 문자 : a, 새로운 상태 : 3
현재 문자 : a, 새로운 상태 : 3
현재 문자 : b, 새로운 상태 : 3
현재 문자 : b, 새로운 상태 : 3

문자열 aabbaabb 승인 상태 여부 : false
```
