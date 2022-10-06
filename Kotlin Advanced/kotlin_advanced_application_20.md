## 요구사항 분석
주어진 숫자 데이터를 이용하여, 다음에 이어질 ```N```개의 숫자 데이터를 분석하시오. ```howto```의 값에 따른 분석 방법은 다음과 같다.
* ```last K``` : 주어진 숫자 데이터 길이보다 작은 ```K```의 값에 대해서, 마지막 ```K```개의 값의 평균을 그대로 적용하여 다음에 이어질 ```N```개의 숫자를 예측한다.
* ```linear``` : 주어진 숫자 데이터 중 첫 번째와 마지막 데이터 사이의 평균변화량을 그대로 적용하여, 다음에 이어질 ```N```개의 숫자를 예측한다.
* ```season K``` : 주어진 숫자 데이터 길이의 절반보다 작은 ```K```의 값을 시즌의 주기로 하여, 마찬가지로 평균변화량을 통해 예측한다.
  * 예를 들어 ```5```, ```10```, ```15```번째 값이 주어진 상태에서 ```20```번째 값을 예측할 때 ```K```의 값이 ```5```이면, 시즌의 주기상 동일한 위치에 있는 값들 중 첫 번째인 ```5```번째 값과 ```15```번째 값의 평균변화량을 적용한다.
* ```exponential``` : 주어진 숫자 데이터 중 첫 번째와 마지막 데이터 사이의 평균변화량 대신 평균 증가율(기하 평균)을 그대로 적용하여, 다음에 이어질 ```N```개의 숫자를 예측한다.

### 데이터 클래스
```kotlin
// 데이터 정보
data class NumericData (
    val values: List<Double>, // 값의 목록
    val howto: String, // 예측 방법을 나타내는 howto 값
    val N: Int, // 예측해야 하는 다음 값의 개수
)
```

## 코드 및 실행 결과
### 코드
```kotlin
import kotlin.math.*

// 데이터 정보
data class NumericData (
    val values: List<Double>, // 값의 목록
    val howto: String, // 예측 방법을 나타내는 howto 값
    val N: Int, // 예측해야 하는 다음 값의 개수
)

// 데이터 분석 및 예측
fun forecast(numericData: NumericData) {
    val N = numericData.N
    val forecasted = mutableListOf<Double>()
    val original = numericData.values
    val howTo = numericData.howto
    
    println("\nforecasting ${N} future values with ${original} and ${howTo} :")
    
    when (howTo.substringBefore(" ")) {
        "last" -> {
            val K = howTo.substringAfter(" ").toInt()
            val fixed = original.takeLast(K).sum() / K
            
            repeat(N) { idx -> // 반복
                println("${idx + 1} : ${fixed}")
            }
        }
        "linear" -> {
            val firstValue = original.first() // 첫 번째 값
            val lastValue = original.last() // 마지막 값
            val avgDiff = (lastValue - firstValue) / (original.size - 1)
            
            repeat(N) { idx -> // 반복
                println("${idx + 1} : ${lastValue + (idx + 1) * avgDiff}")
            }
        }
        "season" -> {
            val K = howTo.substringAfter(" ").toInt()
            
            // 각 시즌 위치에 대한 평균변화량 구하기
            val lastValues = mutableListOf<Double>()
            val avgDiffs = mutableListOf<Double>()
            
            (0 until K).forEach {
                val firstValue = original[it] // 시즌의 해당 위치 중 첫 번째 값
                val lastIndex = (original.size - it - 1) / K * K + it
                val lastValue = original[lastIndex] // 시즌의 해당 위치 중 마지막 값
                val avgDiff = (lastValue - firstValue) / ((lastIndex - it) / K)
                
                lastValues.add(lastValue)
                avgDiffs.add(avgDiff)
            }
            
            repeat(N) { idx -> // 반복
                val mod = (original.size + idx) % K
                println("${idx + 1} : ${lastValues[mod] + (idx / K + 1) * avgDiffs[mod]}")
            }
        }
        "exponential" -> {
            val firstValue = original.first() // 첫 번째 값
            val lastValue = original.last() // 마지막 값
            val avgDiff = (lastValue / firstValue).pow(1 / (original.size - 1.0))
            
            repeat(N) { idx -> // 반복
                println("${idx + 1} : ${lastValue * avgDiff.pow(idx + 1)}")
            }
        }
    }
}

fun main() {
    
    // 임시 데이터 목록
    val numericData = listOf<NumericData> (
        NumericData(listOf(1.2, 1.3, 2.4, 3.5, 3.6, 4.4), "last 1", 3),
        NumericData(listOf(1.2, 1.3, 2.4, 3.5, 3.6, 4.4), "last 2", 3),
        NumericData(listOf(1.2, 1.3, 2.4, 3.5, 3.6, 4.4), "last 3", 3),
        NumericData(listOf(1.0, 2.1, 3.6, 5.5), "linear", 4),
        NumericData(listOf(10.5, 12.0, 16.8, 19.6, 7.8, 11.3, 14.5, 17.0, 6.5, 10.8, 12.8, 13.6), "season 4", 8),
        NumericData(listOf(3.0, 4.0, 12.6, 5.8, 7.9, 16.5, 8.9, 12.4, 30.7, 12.0, 19.0, 45.6), "season 3", 6),
        NumericData(listOf(1.0, 2.5, 3.4, 5.6, 16.0), "exponential", 4),
        NumericData(listOf(25.6, 34.4, 51.4, 82.0, 143.5, 234.2, 291.6), "exponential", 2)
    )
    
    numericData.forEach {
        forecast(it)
    }
}
```

### 실행 결과
```

forecasting 3 future values with [1.2, 1.3, 2.4, 3.5, 3.6, 4.4] and last 1 :
1 : 4.4
2 : 4.4
3 : 4.4

forecasting 3 future values with [1.2, 1.3, 2.4, 3.5, 3.6, 4.4] and last 2 :
1 : 4.0
2 : 4.0
3 : 4.0

forecasting 3 future values with [1.2, 1.3, 2.4, 3.5, 3.6, 4.4] and last 3 :
1 : 3.8333333333333335
2 : 3.8333333333333335
3 : 3.8333333333333335

forecasting 4 future values with [1.0, 2.1, 3.6, 5.5] and linear :
1 : 7.0
2 : 8.5
3 : 10.0
4 : 11.5

forecasting 8 future values with [10.5, 12.0, 16.8, 19.6, 7.8, 11.3, 14.5, 17.0, 6.5, 10.8, 12.8, 13.6] and season 4 :
1 : 4.5
2 : 10.200000000000001
3 : 10.8
4 : 10.599999999999998
5 : 2.5
6 : 9.600000000000001
7 : 8.8
8 : 7.599999999999998

forecasting 6 future values with [3.0, 4.0, 12.6, 5.8, 7.9, 16.5, 8.9, 12.4, 30.7, 12.0, 19.0, 45.6] and season 3 :
1 : 15.0
2 : 24.0
3 : 56.6
4 : 18.0
5 : 29.0
6 : 67.6

forecasting 4 future values with [1.0, 2.5, 3.4, 5.6, 16.0] and exponential :
1 : 32.0
2 : 64.0
3 : 128.0
4 : 256.0

forecasting 2 future values with [25.6, 34.4, 51.4, 82.0, 143.5, 234.2, 291.6] and exponential :
1 : 437.40000000000003
2 : 656.1
```
