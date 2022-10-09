## 요구사항 분석
어느 유튜버의 일일 구독자 수의 추이가 숫자 값의 목록으로 주어졌을 때, ```구독자 폭증 기간```은 다음과 같이 구할 수 있다.
* 해당 유튜버의 ```N```일 간의 구독자 수에 대해, 해당 기간 동안 하루라도 구독자 수가 감소한 적이 없는 기간을 모두 찾는다.
* 해당 기간 각각에 대해, 다음과 같이 구한 값을 ```증가 지표```라고 한다.
  * 해당 기간 마지막 날의 구독자 수를 해당 기간 첫날의 구독자 수로 나눈 값을 ```A```라고 한다.
  * 해당 기간의 이웃한 두 날짜 중, ```(나중 날짜의 구독자 수) / (처음 날짜의 구독자 수)```의 최댓값과 최솟값을 각각 ```B```, ```C```라고 한다.
  * 증가 지표는 ```(A + 3 * B + 2 * C) / 6```의 값과 같다.
* 해당 기간들 중 ```증가 지표```가 최대인 기간을 ```구독자 폭증 기간```으로 한다.

데이터가 아래와 같이 주어졌을 때, 구독자 폭증 기간을 구하시오.

### 데이터 클래스
```kotlin
// 구독자 수 추이 정보
data class SubscriberCount (
    val N: Int, // N의 값
    val subscribers: List<Int> // 일별 구독자 수
)
```

## 코드 및 실행 결과
### 코드
```kotlin
// 구독자 수 추이 정보
data class SubscriberCount (
    val N: Int, // N의 값
    val subscribers: List<Int> // 일별 구독자 수
)

// 구독자 폭증 기간 출력 함수
fun findSubscriberGreatIncreasing(trend: List<Int>, N: Int) {
    val days = trend.size
    
    // 증가 시 해당 인덱스 값, 감소 시 -1
    val isIncreasing = (0 until days - N + 1).map { it }.toMutableList()
    var trendIdx = 0
    
    // 각각의 N일 동안의 증가 여부 판단
    (0 until days - N + 1).forEach {
       
        // 첫번째 증가 여부 (0 ~ N-1일) 처리
        if (it == 0) {
            for (i in 0 until N-1) {
                if (trend[i] > trend[i+1]) {
                    isIncreasing[i] = -1
                }
            }
        }
        
        // 나머지 증가 여부 처리
        else {
            if (trend[it + N - 2] > trend[it + N - 1]) {
                (it until it + N - 1).forEach { d ->
                    if (d < isIncreasing.size) {
                        isIncreasing[d] = -1
                    }
                }
            }
        }
    }
    println("1. 증가 여부: ${isIncreasing}\n")
    
    // 증가 지표 계산
    var greatIncreasingStartIndex = 0
    var maxIncrement = 0.0
    
    isIncreasing.filter { it >= 0 }.forEachIndexed { idx, it ->
        val subList = trend.subList(it, it + N)
        val A = trend[it + N - 1] / trend[it].toDouble()
        
        val B = (0 until N - 1).maxOf { d ->
            subList[d + 1] / subList[d].toDouble()
        }
        val C = (0 until N - 1).minOf { d ->
            subList[d + 1] / subList[d].toDouble()
        }
        
        // 증가 지표
        val increment = (A + 3 * B + 2 * C) / 6.0
        println(
            "${it}일 ~ ${it + N - 1}일: 증가 지표 = ${String.format("%.4f", increment)} " +
            "구독자수=${subList}, " +
            "A=${String.format("%.4f", A)}, B=${String.format("%.4f", B)}, C=${String.format("%.4f", C)}"
        )
        
        if (increment > maxIncrement) {
            greatIncreasingStartIndex = it
            maxIncrement = increment
        }
    }
    
    // 증가 지표 최대 기간 출력
    println(
        "\n증가 지표 최대 기간 : " + 
        "${greatIncreasingStartIndex}일 ~ ${greatIncreasingStartIndex + N - 1}일"
    )
}

fun main() {
    
    // 임시 구독자 추이 정보
    val tempSubscriberCount = SubscriberCount(
        4,
        listOf(
            814, 812, 813, 828, 839, 856, 854, 857, 858, 857,
            860, 860, 889, 896, 896, 904, 901, 899, 903, 907,
            913, 911, 915, 918, 919, 919, 920, 927, 926, 929
        )
    )
    
    // 구독자 폭증 기간 출력
    findSubscriberGreatIncreasing(
        tempSubscriberCount.subscribers, tempSubscriberCount.N
    )
}
```

### 실행 결과
```kotlin
1. 증가 여부: [-1, 1, 2, -1, -1, -1, -1, -1, -1, 9, 10, 11, 12, -1, -1, -1, -1, 17, -1, -1, -1, 21, 22, 23, 24, -1, -1]

1일 ~ 4일: 증가 지표 = 1.0152 구독자수=[812, 813, 828, 839], A=1.0333, B=1.0185, C=1.0012
2일 ~ 5일: 증가 지표 = 1.0234 구독자수=[813, 828, 839, 856], A=1.0529, B=1.0203, C=1.0133
9일 ~ 12일: 증가 지표 = 1.0231 구독자수=[857, 860, 860, 889], A=1.0373, B=1.0337, C=1.0000
10일 ~ 13일: 증가 지표 = 1.0238 구독자수=[860, 860, 889, 896], A=1.0419, B=1.0337, C=1.0000
11일 ~ 14일: 증가 지표 = 1.0238 구독자수=[860, 889, 896, 896], A=1.0419, B=1.0337, C=1.0000
12일 ~ 15일: 증가 지표 = 1.0073 구독자수=[889, 896, 896, 904], A=1.0169, B=1.0089, C=1.0000
17일 ~ 20일: 증가 지표 = 1.0074 구독자수=[899, 903, 907, 913], A=1.0156, B=1.0066, C=1.0044
21일 ~ 24일: 증가 지표 = 1.0040 구독자수=[911, 915, 918, 919], A=1.0088, B=1.0044, C=1.0011
22일 ~ 25일: 증가 지표 = 1.0024 구독자수=[915, 918, 919, 919], A=1.0044, B=1.0033, C=1.0000
23일 ~ 26일: 증가 지표 = 1.0009 구독자수=[918, 919, 919, 920], A=1.0022, B=1.0011, C=1.0000
24일 ~ 27일: 증가 지표 = 1.0053 구독자수=[919, 919, 920, 927], A=1.0087, B=1.0076, C=1.0000

증가 지표 최대 기간 : 10일 ~ 13일
```
