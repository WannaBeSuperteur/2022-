## 요구사항 분석
정수들로 구성된 숫자의 목록이 주어진다. 여기서 다음을 구하시오.
* 최빈값 (단, 최빈값이 여러 개(```n```)인 경우 이들의 중앙값 (```n / 2```번째 인덱스)을 출력한다.)
* 고유한 값의 개수
* 각각의 고유한 값별로 등장하는 횟수의 평균값, 최댓값, 최솟값 및 표준편차
* 5번째로 큰 값과 3번째로 작은 값
* 고유한 값 중 5번째로 큰 값과 3번째로 작은 값

### 데이터 클래스
데이터 클래스는 주어지지 않고, 정수들의 목록만 주어진다.

## 코드 및 실행 결과
### 코드
```kotlin
import kotlin.math.sqrt

// 표준편차 구하기 (예제 34의 함수를 그대로 사용)
fun computeStandardDeviation(values: List<Double>): Double {
    val n = values.size
    val mean = values.sum() / n.toDouble()
    val sumPart = (0 until n).sumOf {
        (values[it] - mean) * (values[it] - mean)
    }
    
    val v = sumPart / n.toDouble()
    return sqrt(v)
}

// 답 출력
fun answer(numbers: List<Int>) {
    
    // 최빈값
    val occur = numbers.groupBy { it }.map {
        listOf(it.key, it.value.size)
    }
    val occurMax = occur.maxOf { it[1] }
    
    val modes = occur.filter {
        it[1] == occurMax
    }.sortedBy {
        it[0]
    }
    val mode = modes[modes.size / 2][0]
    
    println("1. ${occur}")
    println("2. ${occurMax}")
    println("3. ${modes}")
    println("최빈값 = ${mode}")
    
    // 고유한 값의 개수
    println("고유한 값의 개수 = ${occur.size}\n")
    
    // 각각의 고유한 값별로 등장하는 횟수의 평균값, 최댓값, 최솟값 및 표준편차
    val occurCount = occur.map { it[1] }
    val occurCntMean = occurCount.sum() / occurCount.size.toDouble()
    val occurCntMax = occurCount.max()
    val occurCntMin = occurCount.min()
    val occurCntSd = computeStandardDeviation(occurCount.map { it.toDouble() })
    
    println("4. ${occurCount}")
    println("평균: ${occurCntMean}, 최대: ${occurCntMax}, 최소: ${occurCntMin}, 표준편차: ${occurCntSd}\n")

    // 5번째로 큰 값과 3번째로 작은 값
    val numSorted = numbers.sorted()
    println("5. ${numSorted}")
    println("5번째로 큰 값: ${numSorted[numSorted.size - 5]}, 3번째로 작은 값: ${numSorted[2]}\n")

    // 고유한 값 중 5번째로 큰 값과 3번째로 작은 값
    val occurNums = occur.map { it[0] }.sorted()
    println("6. ${occurNums}")
    println("고유한 값들 중 5번째로 큰 값: ${occurNums[occurNums.size - 5]}, 3번째로 작은 값: ${occurNums[2]}\n")
}

fun main() {
    val numberList = listOf(
        13, 17, 25, 19, 16, 14, 14, 15, 16, 18, 23, 26, 20, 13, 12,
        11, 10, 11, 12, 15, 16, 12, 20, 18, 22, 29, 24, 21, 10, 17,
        13, 15, 15, 14, 16, 10, 14, 15, 12, 13, 18, 17, 19, 17, 17,
        17, 16, 15, 11, 10, 8, 15, 27, 24, 9, 26, 24, 21, 18, 15,
        17, 13, 16, 14, 20, 21, 19, 22, 13, 16, 10, 17, 13, 16, 13
    )
    
    answer(numberList)
}
```

### 실행 결과
```kotlin
1. [[13, 8], [17, 8], [25, 1], [19, 3], [16, 8], [14, 5], [15, 8], [18, 4], [23, 1], [26, 2], [20, 3], [12, 4], [11, 3], [10, 5], [22, 2], [29, 1], [24, 3], [21, 3], [8, 1], [27, 1], [9, 1]]
2. 8
3. [[13, 8], [15, 8], [16, 8], [17, 8]]
최빈값 = 16
고유한 값의 개수 = 21

4. [8, 8, 1, 3, 8, 5, 8, 4, 1, 2, 3, 4, 3, 5, 2, 1, 3, 3, 1, 1, 1]
평균: 3.5714285714285716, 최대: 8, 최소: 1, 표준편차: 2.4798507745317218

5. [8, 9, 10, 10, 10, 10, 10, 11, 11, 11, 12, 12, 12, 12, 13, 13, 13, 13, 13, 13, 13, 13, 14, 14, 14, 14, 14, 15, 15, 15, 15, 15, 15, 15, 15, 16, 16, 16, 16, 16, 16, 16, 16, 17, 17, 17, 17, 17, 17, 17, 17, 18, 18, 18, 18, 19, 19, 19, 20, 20, 20, 21, 21, 21, 22, 22, 23, 24, 24, 24, 25, 26, 26, 27, 29]
5번째로 큰 값: 25, 3번째로 작은 값: 10

6. [8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 29]
고유한 값들 중 5번째로 큰 값: 24, 3번째로 작은 값: 10
```
