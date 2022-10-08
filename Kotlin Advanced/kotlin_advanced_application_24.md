## 요구사항 분석
주어진 데이터의 왜도(skew)와 첨도(kurtosis)를 계산하여 각각 출력하시오. 단, 정규분포의 왜도는 0, 첨도는 3이다.

### 데이터 클래스
```kotlin
// 자료
data class Data (
    val values: List<Int> // 값들의 목록
)
```

## 코드 및 실행 결과
### 코드
```kotlin
import kotlin.math.*

// 자료
data class Data (
    val values: List<Int> // 값들의 목록
)

// 표준편차 계산
fun computeStandardDeviation(values: List<Int>): Double {
    val n = values.size
    val mean = values.sum() / n.toDouble()
    
    val sumPart = values.sumOf {
        (it - mean) * (it - mean)
    }
    val mainPart = sumPart / (n - 1)
    
    return Math.sqrt(mainPart)
}

// 왜도 계산
fun computeSkew(values: List<Int>): Double {
    val n = values.size
    val mean = values.sum() / n.toDouble()
    
    val sumPart = values.sumOf {
        (it - mean).pow(3.0)
    }
    val sd = computeStandardDeviation(values)
    
    return sumPart / (n * sd.pow(3.0))
}

// 첨도 계산
fun computeKurtosis(values: List<Int>): Double {
    val n = values.size
    val mean = values.sum() / n.toDouble()
    
    val sumPart = values.sumOf {
        (it - mean).pow(4.0)
    }
    val sd = computeStandardDeviation(values)
    
    return sumPart / (n * sd.pow(4.0))
}

fun main() {
    val tempData = listOf<Data> (
        Data(listOf(2, 3, 3, 3, 3, 4, 5, 7, 10, 15)),
        Data(listOf(2, 4, 7, 8, 9, 9, 9, 10, 10, 10)),
        Data(listOf(1, 3, 4, 5, 5, 6, 6, 7, 9, 10)),
        Data(listOf(0, 1, 3, 5, 12, 52, 88, 90, 93, 95)),
        Data(listOf(0, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377)),
        Data(listOf(1, 2, 4, 8, 16, 32, 64, 128, 256, 512, 1024, 2048, 4096)),
        Data(listOf(40, 75, 80, 88, 90, 90, 92, 93, 95, 95, 95, 98, 98, 98, 100, 100)),
        Data(listOf(20, 31, 36, 40, 45, 55, 60, 62, 64, 65, 66, 68, 70, 75, 80, 88)),
        Data(listOf(5, 10, 10, 12, 15, 15, 15, 20, 23, 25, 30, 45, 47, 50, 88, 97)),
        Data(listOf(10, 20, 25, 27, 28, 30, 32, 33, 38, 48)),
        Data(listOf(3, 6, 7, 8, 9, 9, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 11, 11, 13, 16)),
        Data(listOf(4, 10, 15, 16, 17, 17, 17, 18, 18, 18, 18, 18, 20, 25, 30))
    )
    
    // 각 데이터에 대해서 왜도 및 첨도 계산
    tempData.forEach {
        println("데이터: ${it.values}")
        println("왜도: ${computeSkew(it.values)}")
        println("첨도: ${computeKurtosis(it.values)}\n")
    }
}
```

### 실행 결과
```kotlin
데이터: [2, 3, 3, 3, 3, 4, 5, 7, 10, 15]
왜도: 1.2086110853247418
첨도: 3.0899290513302895

데이터: [2, 4, 7, 8, 9, 9, 9, 10, 10, 10]
왜도: -1.0364699682797671
첨도: 2.51179125380764

데이터: [1, 3, 4, 5, 5, 6, 6, 7, 9, 10]
왜도: 0.041377109121875325
첨도: 1.9776424520658922

데이터: [0, 1, 3, 5, 12, 52, 88, 90, 93, 95]
왜도: 0.1358218381131904
첨도: 0.9430259992061141

데이터: [0, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377]
왜도: 1.629932591617173
첨도: 4.542051669974011

데이터: [1, 2, 4, 8, 16, 32, 64, 128, 256, 512, 1024, 2048, 4096]
왜도: 1.9151776225634343
첨도: 5.58978366918668

데이터: [40, 75, 80, 88, 90, 90, 92, 93, 95, 95, 95, 98, 98, 98, 100, 100]
왜도: -2.2499868683536506
첨도: 7.691058041662896

데이터: [20, 31, 36, 40, 45, 55, 60, 62, 64, 65, 66, 68, 70, 75, 80, 88]
왜도: -0.41947342821197636
첨도: 2.104976068162772

데이터: [5, 10, 10, 12, 15, 15, 15, 20, 23, 25, 30, 45, 47, 50, 88, 97]
왜도: 1.2232587244267539
첨도: 3.2717150429403645

데이터: [10, 20, 25, 27, 28, 30, 32, 33, 38, 48]
왜도: -0.024742866776800623
첨도: 2.5650738471154035

데이터: [3, 6, 7, 8, 9, 9, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 11, 11, 13, 16]
왜도: -0.29305186470115807
첨도: 5.600038124833613

데이터: [4, 10, 15, 16, 17, 17, 17, 18, 18, 18, 18, 18, 20, 25, 30]
왜도: -0.12781583341288874
첨도: 3.816609176855868
```
