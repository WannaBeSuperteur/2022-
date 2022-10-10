## 요구사항 분석
기존 3차원 점들의 집합 ```points```와 새로운 3차원 점들의 집합 ```new_points```, ```N```의 값이 주어졌을 때, k-Nearest Neighbor 방법을 이용하여 ```new_points```의 각 점들이 어떤 분류에 속하는지 구하시오.

단, 가장 가까이 있는 ```N```개의 기존 3차원 점들을 이용하며, 각 기존 점들의 가중치는 거리에 반비례한다.

### 데이터 클래스
```kotlin
// 주어지는 데이터
data class KNNTest (
    val points: List<Point>, // 기존 3차원 점들의 집합
    val new_points: List<Point>, // 새로운 3차원 점들의 집합
    val N: Int
)

// 점
data class Point (
    val x: Double,
    val y: Double,
    val z: Double,
    val classNo: Int // 분류
)
```
