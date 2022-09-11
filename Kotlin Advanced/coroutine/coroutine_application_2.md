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

## 예제 코드 (코루틴 이용)
```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.channels.*

// 작업의 순서를 저장한 배열
val taskOrder = listOf(0, 1, 3, 6, 7, 15, 16, 18, 20, 25, 26, 30, 34, 36, 38, 41, 42, 49, 50, 60, 65, 70, 71, 72, 79, 80, 81, 90, 92, 94, 95, 98, 99, 102, 107,
                       120, 124, 126, 127, 129, 131, 132, 135, 140, 141, 144, 145, 146, 150, 151, 152, 161, 165, 170, 172, 175, 178, 179, 191, 193, 197, 199,
                       200, 201, 202, 207, 209, 215, 217, 220, 221, 222, 229, 230, 231, 236, 245, 250, 265, 270, 271, 279, 280, 286, 289, 299, 301, 302, 303,
                       310, 312, 314, 316, 317, 320, 359, 370, 401, 402, 403, 414, 425, 436, 444, 445, 447, 448, 450, 460, 470, 471, 472, 483, 494, 495, 496)
var currentIndex = -1

fun main() {
    currentIndex = 0
    execute()
}

fun execute() = runBlocking {
    var count = 0
    
    while (count < taskOrder.size) { // currentIndex의 값이 이 함수에서는 계속 0으로 나오므로 대신 count를 이용
        val timeMsec = System.currentTimeMillis() % 1000
        
        println("${timeMsec} currentIndex = ${currentIndex}, count = ${count}")
        
        async { // 현재 인덱스에 대해 나머지가 0인 경우 작업 실행
            func1()
        }
        async { // 현재 인덱스에 대해 나머지가 1인 경우 작업 실행
            func2()
        }
        async { // 현재 인덱스에 대해 나머지가 2인 경우 작업 실행
            func3()
        }
        
        count++
    }
}

fun commonFunc(remainder: Int) {
    val timeMsec = System.currentTimeMillis() % 1000
    println("${timeMsec} remainder = ${remainder}")
    
    // 인덱스 오류 방지
    if (currentIndex >= taskOrder.size) {
        return
    }
    
    val currentRemainder = taskOrder[currentIndex] % 3
    
    // 나머지가 같은 경우에만 실행
    if (remainder == currentRemainder) {
        when (remainder) {
            0 -> println("${timeMsec} func1 : I completed task ${taskOrder[currentIndex]}. It was hard work. TT")
            1 -> println("${timeMsec} func2 : I completed task ${taskOrder[currentIndex]} ^o^ I feel happy!!")
            2 -> println("${timeMsec} func3 : I completed task ${taskOrder[currentIndex]}. I feel so good ^U^")
        }
        currentIndex++
    }
}

fun func1() = runBlocking { // 3으로 나눈 나머지가 0인 작업을 순서대로 실행
    commonFunc(0)
}

fun func2() = runBlocking { // 3으로 나눈 나머지가 1인 작업을 순서대로 실행
    commonFunc(1)
}

fun func3() = runBlocking { // 3으로 나눈 나머지가 2인 작업을 순서대로 실행
    commonFunc(2)
}
```

## 예제 출력 (코루틴 이용)
```kotlin
733 currentIndex = 0, count = 0
789 currentIndex = 0, count = 1
789 currentIndex = 0, count = 2
789 currentIndex = 0, count = 3
789 currentIndex = 0, count = 4
790 currentIndex = 0, count = 5
790 currentIndex = 0, count = 6
790 currentIndex = 0, count = 7
790 currentIndex = 0, count = 8
790 currentIndex = 0, count = 9
790 currentIndex = 0, count = 10
790 currentIndex = 0, count = 11
791 currentIndex = 0, count = 12
791 currentIndex = 0, count = 13
791 currentIndex = 0, count = 14
791 currentIndex = 0, count = 15
791 currentIndex = 0, count = 16
791 currentIndex = 0, count = 17
791 currentIndex = 0, count = 18
791 currentIndex = 0, count = 19
792 currentIndex = 0, count = 20
792 currentIndex = 0, count = 21
792 currentIndex = 0, count = 22
792 currentIndex = 0, count = 23
792 currentIndex = 0, count = 24
792 currentIndex = 0, count = 25
793 currentIndex = 0, count = 26
793 currentIndex = 0, count = 27
793 currentIndex = 0, count = 28
793 currentIndex = 0, count = 29
793 currentIndex = 0, count = 30
793 currentIndex = 0, count = 31
793 currentIndex = 0, count = 32
794 currentIndex = 0, count = 33
794 currentIndex = 0, count = 34
794 currentIndex = 0, count = 35
794 currentIndex = 0, count = 36
794 currentIndex = 0, count = 37
794 currentIndex = 0, count = 38
794 currentIndex = 0, count = 39
795 currentIndex = 0, count = 40
795 currentIndex = 0, count = 41
795 currentIndex = 0, count = 42
795 currentIndex = 0, count = 43
795 currentIndex = 0, count = 44
795 currentIndex = 0, count = 45
796 currentIndex = 0, count = 46
796 currentIndex = 0, count = 47
796 currentIndex = 0, count = 48
796 currentIndex = 0, count = 49
799 currentIndex = 0, count = 50
800 currentIndex = 0, count = 51
800 currentIndex = 0, count = 52
800 currentIndex = 0, count = 53
801 currentIndex = 0, count = 54
801 currentIndex = 0, count = 55
801 currentIndex = 0, count = 56
801 currentIndex = 0, count = 57
801 currentIndex = 0, count = 58
801 currentIndex = 0, count = 59
801 currentIndex = 0, count = 60
801 currentIndex = 0, count = 61
802 currentIndex = 0, count = 62
802 currentIndex = 0, count = 63
802 currentIndex = 0, count = 64
802 currentIndex = 0, count = 65
802 currentIndex = 0, count = 66
802 currentIndex = 0, count = 67
802 currentIndex = 0, count = 68
802 currentIndex = 0, count = 69
802 currentIndex = 0, count = 70
803 currentIndex = 0, count = 71
803 currentIndex = 0, count = 72
803 currentIndex = 0, count = 73
803 currentIndex = 0, count = 74
803 currentIndex = 0, count = 75
803 currentIndex = 0, count = 76
803 currentIndex = 0, count = 77
803 currentIndex = 0, count = 78
803 currentIndex = 0, count = 79
804 currentIndex = 0, count = 80
804 currentIndex = 0, count = 81
804 currentIndex = 0, count = 82
804 currentIndex = 0, count = 83
804 currentIndex = 0, count = 84
815 currentIndex = 0, count = 85
815 currentIndex = 0, count = 86
815 currentIndex = 0, count = 87
815 currentIndex = 0, count = 88
815 currentIndex = 0, count = 89
815 currentIndex = 0, count = 90
815 currentIndex = 0, count = 91
816 currentIndex = 0, count = 92
816 currentIndex = 0, count = 93
816 currentIndex = 0, count = 94
816 currentIndex = 0, count = 95
816 currentIndex = 0, count = 96
816 currentIndex = 0, count = 97
816 currentIndex = 0, count = 98
828 currentIndex = 0, count = 99
829 currentIndex = 0, count = 100
829 currentIndex = 0, count = 101
829 currentIndex = 0, count = 102
829 currentIndex = 0, count = 103
829 currentIndex = 0, count = 104
829 currentIndex = 0, count = 105
829 currentIndex = 0, count = 106
829 currentIndex = 0, count = 107
829 currentIndex = 0, count = 108
829 currentIndex = 0, count = 109
830 currentIndex = 0, count = 110
830 currentIndex = 0, count = 111
830 currentIndex = 0, count = 112
830 currentIndex = 0, count = 113
830 currentIndex = 0, count = 114
830 currentIndex = 0, count = 115
851 remainder = 0
851 func1 : I completed task 0. It was hard work. TT
851 remainder = 1
851 func2 : I completed task 1 ^o^ I feel happy!!
851 remainder = 2
851 remainder = 0
851 func1 : I completed task 3. It was hard work. TT
851 remainder = 1
851 remainder = 2
851 remainder = 0
851 func1 : I completed task 6. It was hard work. TT
851 remainder = 1
851 func2 : I completed task 7 ^o^ I feel happy!!
851 remainder = 2
851 remainder = 0
851 func1 : I completed task 15. It was hard work. TT
851 remainder = 1
851 func2 : I completed task 16 ^o^ I feel happy!!
851 remainder = 2
852 remainder = 0
852 func1 : I completed task 18. It was hard work. TT
852 remainder = 1
852 remainder = 2
852 func3 : I completed task 20. I feel so good ^U^
852 remainder = 0
852 remainder = 1
852 func2 : I completed task 25 ^o^ I feel happy!!
852 remainder = 2
852 func3 : I completed task 26. I feel so good ^U^
852 remainder = 0
852 func1 : I completed task 30. It was hard work. TT
852 remainder = 1
852 func2 : I completed task 34 ^o^ I feel happy!!
852 remainder = 2
852 remainder = 0
852 func1 : I completed task 36. It was hard work. TT
852 remainder = 1
852 remainder = 2
852 func3 : I completed task 38. I feel so good ^U^
852 remainder = 0
852 remainder = 1
852 remainder = 2
852 func3 : I completed task 41. I feel so good ^U^
852 remainder = 0
852 func1 : I completed task 42. It was hard work. TT
852 remainder = 1
852 func2 : I completed task 49 ^o^ I feel happy!!
852 remainder = 2
852 func3 : I completed task 50. I feel so good ^U^
852 remainder = 0
852 func1 : I completed task 60. It was hard work. TT
853 remainder = 1
853 remainder = 2
853 func3 : I completed task 65. I feel so good ^U^
853 remainder = 0
853 remainder = 1
853 func2 : I completed task 70 ^o^ I feel happy!!
853 remainder = 2
853 func3 : I completed task 71. I feel so good ^U^
853 remainder = 0
853 func1 : I completed task 72. It was hard work. TT
853 remainder = 1
853 func2 : I completed task 79 ^o^ I feel happy!!
853 remainder = 2
853 func3 : I completed task 80. I feel so good ^U^
853 remainder = 0
853 func1 : I completed task 81. It was hard work. TT
853 remainder = 1
853 remainder = 2
853 remainder = 0
853 func1 : I completed task 90. It was hard work. TT
853 remainder = 1
853 remainder = 2
853 func3 : I completed task 92. I feel so good ^U^
853 remainder = 0
853 remainder = 1
853 func2 : I completed task 94 ^o^ I feel happy!!
853 remainder = 2
853 func3 : I completed task 95. I feel so good ^U^
853 remainder = 0
853 remainder = 1
853 remainder = 2
853 func3 : I completed task 98. I feel so good ^U^
854 remainder = 0
854 func1 : I completed task 99. It was hard work. TT
854 remainder = 1
854 remainder = 2
854 remainder = 0
854 func1 : I completed task 102. It was hard work. TT
854 remainder = 1
854 remainder = 2
854 func3 : I completed task 107. I feel so good ^U^
854 remainder = 0
854 func1 : I completed task 120. It was hard work. TT
854 remainder = 1
854 func2 : I completed task 124 ^o^ I feel happy!!
854 remainder = 2
854 remainder = 0
854 func1 : I completed task 126. It was hard work. TT
854 remainder = 1
854 func2 : I completed task 127 ^o^ I feel happy!!
854 remainder = 2
854 remainder = 0
854 func1 : I completed task 129. It was hard work. TT
854 remainder = 1
854 remainder = 2
854 func3 : I completed task 131. I feel so good ^U^
854 remainder = 0
854 func1 : I completed task 132. It was hard work. TT
854 remainder = 1
854 remainder = 2
854 remainder = 0
854 func1 : I completed task 135. It was hard work. TT
855 remainder = 1
855 remainder = 2
855 func3 : I completed task 140. I feel so good ^U^
855 remainder = 0
855 func1 : I completed task 141. It was hard work. TT
855 remainder = 1
855 remainder = 2
855 remainder = 0
855 func1 : I completed task 144. It was hard work. TT
855 remainder = 1
855 func2 : I completed task 145 ^o^ I feel happy!!
855 remainder = 2
855 func3 : I completed task 146. I feel so good ^U^
855 remainder = 0
855 func1 : I completed task 150. It was hard work. TT
855 remainder = 1
855 func2 : I completed task 151 ^o^ I feel happy!!
855 remainder = 2
855 func3 : I completed task 152. I feel so good ^U^
855 remainder = 0
855 remainder = 1
855 remainder = 2
855 func3 : I completed task 161. I feel so good ^U^
855 remainder = 0
855 func1 : I completed task 165. It was hard work. TT
855 remainder = 1
855 remainder = 2
855 func3 : I completed task 170. I feel so good ^U^
855 remainder = 0
856 remainder = 1
856 func2 : I completed task 172 ^o^ I feel happy!!
856 remainder = 2
856 remainder = 0
856 remainder = 1
856 func2 : I completed task 175 ^o^ I feel happy!!
856 remainder = 2
856 remainder = 0
856 remainder = 1
856 func2 : I completed task 178 ^o^ I feel happy!!
856 remainder = 2
856 func3 : I completed task 179. I feel so good ^U^
856 remainder = 0
856 remainder = 1
856 remainder = 2
856 func3 : I completed task 191. I feel so good ^U^
856 remainder = 0
856 remainder = 1
856 func2 : I completed task 193 ^o^ I feel happy!!
856 remainder = 2
856 func3 : I completed task 197. I feel so good ^U^
856 remainder = 0
856 remainder = 1
856 func2 : I completed task 199 ^o^ I feel happy!!
856 remainder = 2
856 func3 : I completed task 200. I feel so good ^U^
856 remainder = 0
856 func1 : I completed task 201. It was hard work. TT
856 remainder = 1
856 func2 : I completed task 202 ^o^ I feel happy!!
856 remainder = 2
856 remainder = 0
856 func1 : I completed task 207. It was hard work. TT
857 remainder = 1
857 remainder = 2
857 func3 : I completed task 209. I feel so good ^U^
857 remainder = 0
857 remainder = 1
857 remainder = 2
857 func3 : I completed task 215. I feel so good ^U^
857 remainder = 0
857 remainder = 1
857 func2 : I completed task 217 ^o^ I feel happy!!
857 remainder = 2
857 remainder = 0
857 remainder = 1
857 func2 : I completed task 220 ^o^ I feel happy!!
857 remainder = 2
857 func3 : I completed task 221. I feel so good ^U^
857 remainder = 0
857 func1 : I completed task 222. It was hard work. TT
857 remainder = 1
857 func2 : I completed task 229 ^o^ I feel happy!!
857 remainder = 2
857 func3 : I completed task 230. I feel so good ^U^
857 remainder = 0
857 func1 : I completed task 231. It was hard work. TT
857 remainder = 1
857 remainder = 2
857 func3 : I completed task 236. I feel so good ^U^
857 remainder = 0
857 remainder = 1
857 remainder = 2
857 func3 : I completed task 245. I feel so good ^U^
858 remainder = 0
858 remainder = 1
858 func2 : I completed task 250 ^o^ I feel happy!!
858 remainder = 2
858 remainder = 0
858 remainder = 1
858 func2 : I completed task 265 ^o^ I feel happy!!
858 remainder = 2
858 remainder = 0
858 func1 : I completed task 270. It was hard work. TT
858 remainder = 1
858 func2 : I completed task 271 ^o^ I feel happy!!
858 remainder = 2
858 remainder = 0
858 func1 : I completed task 279. It was hard work. TT
858 remainder = 1
858 func2 : I completed task 280 ^o^ I feel happy!!
858 remainder = 2
858 remainder = 0
858 remainder = 1
858 func2 : I completed task 286 ^o^ I feel happy!!
858 remainder = 2
858 remainder = 0
858 remainder = 1
858 func2 : I completed task 289 ^o^ I feel happy!!
858 remainder = 2
858 func3 : I completed task 299. I feel so good ^U^
858 remainder = 0
858 remainder = 1
858 func2 : I completed task 301 ^o^ I feel happy!!
858 remainder = 2
858 func3 : I completed task 302. I feel so good ^U^
859 remainder = 0
859 func1 : I completed task 303. It was hard work. TT
859 remainder = 1
859 func2 : I completed task 310 ^o^ I feel happy!!
859 remainder = 2
859 remainder = 0
859 func1 : I completed task 312. It was hard work. TT
859 remainder = 1
859 remainder = 2
859 func3 : I completed task 314. I feel so good ^U^
859 remainder = 0
859 remainder = 1
859 func2 : I completed task 316 ^o^ I feel happy!!
859 remainder = 2
859 func3 : I completed task 317. I feel so good ^U^
859 remainder = 0
859 remainder = 1
859 remainder = 2
859 func3 : I completed task 320. I feel so good ^U^
859 remainder = 0
859 remainder = 1
859 remainder = 2
859 func3 : I completed task 359. I feel so good ^U^
859 remainder = 0
868 remainder = 1
868 func2 : I completed task 370 ^o^ I feel happy!!
868 remainder = 2
868 func3 : I completed task 401. I feel so good ^U^
869 remainder = 0
869 func1 : I completed task 402. It was hard work. TT
869 remainder = 1
869 func2 : I completed task 403 ^o^ I feel happy!!
869 remainder = 2
869 remainder = 0
869 func1 : I completed task 414. It was hard work. TT
869 remainder = 1
869 remainder = 2
869 func3 : I completed task 425. I feel so good ^U^
869 remainder = 0
869 remainder = 1
869 func2 : I completed task 436 ^o^ I feel happy!!
869 remainder = 2
869 remainder = 0
869 func1 : I completed task 444. It was hard work. TT
869 remainder = 1
869 func2 : I completed task 445 ^o^ I feel happy!!
869 remainder = 2
869 remainder = 0
869 func1 : I completed task 447. It was hard work. TT
869 remainder = 1
869 func2 : I completed task 448 ^o^ I feel happy!!
869 remainder = 2
869 remainder = 0
869 func1 : I completed task 450. It was hard work. TT
869 remainder = 1
869 func2 : I completed task 460 ^o^ I feel happy!!
869 remainder = 2
869 func3 : I completed task 470. I feel so good ^U^
869 remainder = 0
869 func1 : I completed task 471. It was hard work. TT
869 remainder = 1
869 func2 : I completed task 472 ^o^ I feel happy!!
870 remainder = 2
870 remainder = 0
870 func1 : I completed task 483. It was hard work. TT
870 remainder = 1
870 remainder = 2
870 func3 : I completed task 494. I feel so good ^U^
870 remainder = 0
870 func1 : I completed task 495. It was hard work. TT
870 remainder = 1
870 func2 : I completed task 496 ^o^ I feel happy!!
870 remainder = 2
870 remainder = 0
870 remainder = 1
870 remainder = 2
870 remainder = 0
870 remainder = 1
870 remainder = 2
870 remainder = 0
870 remainder = 1
870 remainder = 2
870 remainder = 0
870 remainder = 1
870 remainder = 2
870 remainder = 0
870 remainder = 1
870 remainder = 2
870 remainder = 0
870 remainder = 1
870 remainder = 2
870 remainder = 0
870 remainder = 1
870 remainder = 2
871 remainder = 0
871 remainder = 1
871 remainder = 2
871 remainder = 0
871 remainder = 1
871 remainder = 2
871 remainder = 0
871 remainder = 1
871 remainder = 2
871 remainder = 0
871 remainder = 1
871 remainder = 2
871 remainder = 0
871 remainder = 1
871 remainder = 2
871 remainder = 0
871 remainder = 1
871 remainder = 2
871 remainder = 0
871 remainder = 1
871 remainder = 2
871 remainder = 0
871 remainder = 1
871 remainder = 2
871 remainder = 0
871 remainder = 1
871 remainder = 2
871 remainder = 0
871 remainder = 1
871 remainder = 2
871 remainder = 0
872 remainder = 1
872 remainder = 2
872 remainder = 0
872 remainder = 1
872 remainder = 2
872 remainder = 0
872 remainder = 1
872 remainder = 2
872 remainder = 0
872 remainder = 1
872 remainder = 2
872 remainder = 0
872 remainder = 1
872 remainder = 2
872 remainder = 0
872 remainder = 1
872 remainder = 2
872 remainder = 0
872 remainder = 1
872 remainder = 2
872 remainder = 0
872 remainder = 1
872 remainder = 2
872 remainder = 0
872 remainder = 1
872 remainder = 2
872 remainder = 0
872 remainder = 1
872 remainder = 2
872 remainder = 0
873 remainder = 1
873 remainder = 2
873 remainder = 0
873 remainder = 1
873 remainder = 2
873 remainder = 0
873 remainder = 1
873 remainder = 2
873 remainder = 0
873 remainder = 1
873 remainder = 2
873 remainder = 0
873 remainder = 1
873 remainder = 2
873 remainder = 0
873 remainder = 1
873 remainder = 2
873 remainder = 0
873 remainder = 1
873 remainder = 2
873 remainder = 0
873 remainder = 1
873 remainder = 2
873 remainder = 0
873 remainder = 1
873 remainder = 2
873 remainder = 0
873 remainder = 1
873 remainder = 2
873 remainder = 0
873 remainder = 1
873 remainder = 2
873 remainder = 0
874 remainder = 1
874 remainder = 2
874 remainder = 0
874 remainder = 1
874 remainder = 2
874 remainder = 0
874 remainder = 1
874 remainder = 2
874 remainder = 0
874 remainder = 1
874 remainder = 2
874 remainder = 0
874 remainder = 1
874 remainder = 2
874 remainder = 0
874 remainder = 1
874 remainder = 2
874 remainder = 0
874 remainder = 1
874 remainder = 2
874 remainder = 0
874 remainder = 1
874 remainder = 2
874 remainder = 0
874 remainder = 1
874 remainder = 2
874 remainder = 0
874 remainder = 1
874 remainder = 2
874 remainder = 0
874 remainder = 1
874 remainder = 2
874 remainder = 0
874 remainder = 1
874 remainder = 2
875 remainder = 0
875 remainder = 1
875 remainder = 2
```
