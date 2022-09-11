## 요구사항 분석
작업의 순서를 지정한 배열이 전역 변수로 정의되어 있다. 이 배열에는 먼저 해야 하는 작업의 순서대로 작업의 ID가 기록되어 있으며, 각 함수 ```func1```, ```func2```, ```func3```는 이들 순서에 맞게 작업을 실행한 후 작업을 완료했다는 내용을 출력하면 된다.

이때 작업의 ID는 항상 오름차순으로 기록되어 있으므로 두 작업의 번호가 서로 다를 때, 앞 번호의 작업을 먼저 수행해야 한다. 서로 다른 함수가 수행하는 작업 간에도 이 규칙을 지켜야 한다.

이때, ```func1```, ```func2```, ```func3```은 작업의 ID를 3으로 나눈 나머지가 각각 ```0```, ```1```, ```2```인 작업을 수행한다.

### 기본 코드 구조
```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.channels.*

// 작업의 순서를 저장한 배열
val taskOrder = listOf(0, 1, 3, 6, 7, 15, 16, 18, 20, 25, 26, 30, 34, 36, 38, 41, 42, 49, 50)

fun main() {
    execute()
}

fun execute() = runBlocking {
    launch {
        func1()
    }
    launch {
        func2()
    }
    launch {
        func3()
    }
}

fun func1() = runBlocking { // 3으로 나눈 나머지가 0인 작업을 순서대로 실행
    
}

fun func2() = runBlocking { // 3으로 나눈 나머지가 1인 작업을 순서대로 실행

}

fun func3() = runBlocking { // 3으로 나눈 나머지가 2인 작업을 순서대로 실행

}
```

## 예제 코드 (코루틴 없이)
```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.channels.*

// 작업의 순서를 저장한 배열
val taskOrder = listOf(0, 1, 3, 6, 7, 15, 16, 18, 20, 25, 26, 30, 34, 36, 38, 41, 42, 49, 50)
val channel = Channel<Int>() // 데이터 송수신용 채널

fun main() {
    execute()
}

fun execute() = runBlocking {
    launch {
        func1(0)
    }
    launch {
        func2(0)
    }
    launch {
        func3(0)
    }
}

fun commonFunc(remainder: Int, i: Int) {
    if (taskOrder[i] % 3 == remainder) { // 해당 task를 수행하는 함수인 경우
        when (remainder) {
            0 -> println("func1 : I completed task ${taskOrder[i]}. It was hard work. TT")
            1 -> println("func2 : I completed task ${taskOrder[i]} ^o^ I feel happy!!")
            2 -> println("func3 : I completed task ${taskOrder[i]}. I feel so good ^U^")
        }

        if (i < taskOrder.size - 1) {
            when (taskOrder[i + 1] % 3) {
                0 -> func1(i + 1)
                1 -> func2(i + 1)
                2 -> func3(i + 1)
            }
        }
    }
}

fun func1(i: Int) { // 3으로 나눈 나머지가 0인 작업을 순서대로 실행
    commonFunc(0, i)
}

fun func2(i: Int) { // 3으로 나눈 나머지가 1인 작업을 순서대로 실행
    commonFunc(1, i)
}

fun func3(i: Int) { // 3으로 나눈 나머지가 2인 작업을 순서대로 실행
    commonFunc(2, i)
}
```
