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

