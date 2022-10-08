## 요구사항 분석
주어진 데이터에 대해, 이를 최소제곱법(Least Square Method)를 이용하여 추정한 선형 회귀식 ```y = ax + b```을 구하시오.

### 데이터 클래스
```kotlin
// 숫자 데이터
data class NumericData (
    val x_values: List<Double>, // 숫자 데이터의 x값
    val y_values: List<Double> // 숫자 데이터의 y값
)
```
