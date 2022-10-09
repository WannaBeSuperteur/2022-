## 요구사항 분석
숫자 값들의 배열이 주어졌을 때, 해당 숫자 값을 다음과 같이 처리하시오.
* 해당 값들에 자연로그를 취한다. 즉, ```x -> ln(x + 100.0)``` 으로 값을 바꾼다.
* 해당 값을 평균 ```M```, 표준편차 ```S```인 분포로 표준화한 다음, 그 표준화한 값을 가장 가까운 정수로 반올림한다.
* 해당 반올림한 값이 상위 ```A1```% 이내이면 ```B1```등급, 상위 ```A1```% ~ ```A2```% 사이이면 ```B2```등급, 상위 ```A2```% ~ ```A3```% 사이이면 ```B3```등급, 이런 식으로 등급화한다.
* 배열에 있는 각 값에 대하여 해당 등급을 구하는 함수를 작성하시오.

### 데이터 클래스
```kotlin
// 숫자 값 배열 및 각종 정보
data class NumericValues (
    val values: List<Double>, // 값들
    val M: Double, // 평균
    val S: Double, // 표준편차
    val mapping: Map<Double, String> // A1->B1, A2->B2, A3->B3, ... 의 맵핑
)
```

## 코드 및 실행 결과
### 코드
```kotlin
import kotlin.math.*

// 숫자 값 배열 및 각종 정보
data class NumericValues (
    val values: List<Double>, // 값들
    val M: Double, // 평균
    val S: Double, // 표준편차
    val mapping: Map<Double, String> // A1->B1, A2->B2, A3->B3, ... 의 맵핑
)

// 표준편차 구하기
fun computeStandardDeviation(values: List<Double>): Double {
    val n = values.size
    val mean = values.sum() / n.toDouble()
    val sumPart = (0 until n).sumOf {
        (values[it] - mean) * (values[it] - mean)
    }
    
    val v = sumPart / n.toDouble()
    return sqrt(v)
}

// 배열을 등급으로 매핑하는 함수
fun mapToClass(numericValues: NumericValues): List<String> {
    val M = numericValues.M
    val S = numericValues.S
    val vals = numericValues.values
    val n = vals.size
    
    // 자연로그 취하기
    val logedValues = vals.map {
        ln(it + 100.0)
    }
    println("1. ${logedValues.take(10)}")
    
    // 평균 0, 표준편차 1로 먼저 표준화하기
    val mean = logedValues.sum() / n.toDouble()
    val std = computeStandardDeviation(logedValues)
    
    val normalizedValues = logedValues.map {
        (it - mean) / std
    }
    println("2. ${normalizedValues.take(10)}")
    
    // 평균 M, 표준편차 S인 분포로 표준화한 후
    val reNormalizedValues = normalizedValues.map {
        it * S + M
    }
    println("3. ${reNormalizedValues.take(10)}")
    
    // 가장 가까운 정수로 반올림
    val reNormalized2 = reNormalizedValues.map {
        it.roundToInt()
    }
    println("4. ${reNormalized2.take(10)}")
    
    // 순위 (기준: 해당 값보다 큰 값의 비율)
    val rank = reNormalized2.map { v0 ->
        val rankPercent = reNormalized2.count { v1 ->
            v1 > v0
        } / n.toDouble() * 100.0
        
        // 아주 약간의 오차를 방지하기 위한 반올림
        round(rankPercent * 1000.0) / 1000.0
    }
    println("5. ${rank.take(10)}")
    
    // 등급 (정렬을 위해 먼저 리스트로 변환)
    val classMap = numericValues.mapping.toList().sortedBy { it.first }
    println("6. 등급 매핑 = ${classMap}")
    
    val finalClass = rank.map { r ->
        val idx = classMap.count { c ->
            c.first < r
        }
        classMap[idx].second
    }
    println("7. 최종 등급 = ${finalClass.take(10)}\n")
    
    return finalClass
}

fun main() {
    val testNumericValues = NumericValues(
        listOf(
            10.4, 10.75, 10.35, 9.15, 6.45, 12.7, 8.1, 15.0, 17.95, 11.75,
            12.5, 12.45, 11.35, 11.4, 7.2, 5.1, 4.45, 4.65, 11.35, 11.85,
            11.4, 11.25, 11.0, 11.15, 13.5, 16.5, 16.65, 14.5, 13.5, 12.55,
            12.15, 12.2, 12.15, 10.45, 7.75, 7.6, 4.5, 19.75, 17.15, 14.5,
            13.5, 13.35, 13.0, 13.75, 13.9, 15.7, 11.25, 11.1, 8.85, 15.9,
            14.1, 11.7, 12.65, 17.35, 19.15, 7.45, 6.8, 7.15, 10.05, 13.3
        ),
        30.0, 7.5,
        mapOf<Double, String>(15.0 to "A", 40.0 to "B", 70.0 to "C", 90.0 to "D", 100.0 to "F")
    )
    
    // 배열을 등급으로 매핑
    val mapping = mapToClass(testNumericValues)
    println(mapping)
}
```

### 실행 결과
```kotlin
1. [4.704110133842995, 4.707275408925245, 4.703657132702745, 4.692723082995953, 4.667675391339489, 4.724729421045731, 4.683056724645162, 4.74493212836325, 4.770260805853684, 4.716264233493678]
2. [-0.36958208043453655, -0.2693506756935111, -0.3839267864890474, -0.7301637274000787, -1.523322380285081, 0.2833469912529832, -1.036258032335295, 0.923084670092472, 1.725141002995555, 0.015288886446672598]
3. [27.228134396740977, 27.979869932298666, 27.120549101332145, 24.52377204449941, 18.57508214786189, 32.12510243439738, 22.22806475748529, 36.92313502569354, 42.93855752246666, 30.114666648350045]
4. [27, 28, 27, 25, 19, 32, 22, 37, 43, 30]
5. [70.0, 68.333, 70.0, 76.667, 90.0, 35.0, 80.0, 15.0, 3.333, 48.333]
6. 등급 매핑 = [(15.0, A), (40.0, B), (70.0, C), (90.0, D), (100.0, F)]
7. 최종 등급 = [C, C, C, D, D, B, D, A, A, C]

[C, C, C, D, D, B, D, A, A, C, B, B, C, C, D, F, F, F, C, C, C, C, C, C, B, A, A, B, B, B, C, C, C, C, D, D, F, A, A, B, B, B, B, B, B, A, C, C, D, A, B, C, B, A, A, D, D, D, D, B]
```
