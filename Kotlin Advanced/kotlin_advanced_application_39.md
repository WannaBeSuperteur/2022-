## 요구사항 분석
기존 데이터 목록인 ```rows```, 새로운 데이터 목록인 ```new_rows```가 주어졌을 때, 새로운 데이터 목록의 각 행에 대해 그 행의 각 값을 이용하여 Naive Bayes 알고리즘으로 그 행의 예측 대상 값의 true, false를 예측하시오.

### 데이터 클래스
```kotlin
// 주어지는 데이터
data class NaiveBayesTest (
    val rows: List<Row>, // 기존 데이터 목록
    val new_rows: List<Row> // 새로운 데이터 목록
)

// 각 데이터
data class Row (
    val values: List<Boolean>, // 각 행의 feature를 나타내는 값들
    val target: Boolean // 예측 대상 값
)
```
