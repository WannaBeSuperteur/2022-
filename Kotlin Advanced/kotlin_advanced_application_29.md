## 요구사항 분석
주어진 데이터에 대해, 이를 최소제곱법(Least Square Method)를 이용하여 추정한 선형 회귀식 ```y = ax + b```을 구하시오. 단, 여기서 데이터의 각 원소에 대한 ```x``` 값은 해당 원소의 인덱스와 같고, ```y``` 값은 해당 원소의 값과 같다.

### 데이터 클래스
```kotlin
// 숫자 데이터
data class NumericData (
    val values: List<Double> // 숫자 데이터
)
```
