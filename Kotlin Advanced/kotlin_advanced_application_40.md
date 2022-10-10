## 요구사항 분석
데이터 점들의 목록과 K의 값이 주어졌을 때, K-means Clustering으로 해당 점을 군집화한 결과를 출력하시오.

### 데이터 클래스
```kotlin
// 주어지는 데이터
data class KMeansTest (
    val points: List<Point>, // 데이터 점들의 목록
    val K: Int // 군집의 개수 K의 값
)

// 점
data class Point (
    val x: Double, // 점의 x좌표
    val y: Double, // 점의 y좌표
    val z: Double // 점의 z좌표
)
```
