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

## 코드 및 실행 결과
### 코드
```kotlin
import java.util.Random
import kotlin.math.*

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

// 두 점 간의 거리
fun computeDist(point0: Point, point1: Point): Double {
    val dx = point0.x - point1.x
    val dy = point0.y - point1.y
    val dz = point0.z - point1.z
    return sqrt(dx * dx + dy * dy + dz * dz)
}

// 주어진 데이터에 대해 K-means Clustering 알고리즘 실행
// (단, 표준화는 하지 않음)
fun runKMeans(kMeansTest: KMeansTest) {
    
    // K개의 군집의 초기 중심 지점을 임의로 지정
    val K = kMeansTest.K
    val clusterCentroids = (0 until K).map {
        Point(Random().nextInt(9) + 1.0, Random().nextInt(9) + 1.0, Random().nextInt(9) + 1.0)
    }.toMutableList()
    println("1. 클러스터 중심의 초기값 :\n${clusterCentroids}\n")
    
    // 각 점들의 할당된 군집
    val n = kMeansTest.points.size
    val assignedCluster = (0 until n).map { -1 }.toMutableList()
    
    // 각 점들을 가장 가까운 군집에 할당 -> 각 군집별 중심점을 평균으로 갱신 반복
    var count = 1
    while (true) {
        
        // 군집 할당 갱신 여부 판정용
        val oldAssignedCluster = assignedCluster.map { it }
        
        // 각 점들을 가장 가까운 군집에 할당
        println(" << ${count}번째 군집 할당 갱신 >>\n")
        kMeansTest.points.forEachIndexed { idx, pt ->
            val dists = mutableListOf<Double>()
            val closestCluster = (0 until K).fold (0) { acc, e ->
                val oldDist = computeDist(pt, clusterCentroids[acc])
                val newDist = computeDist(pt, clusterCentroids[e])
                dists.add(round(newDist * 100.0) / 100.0)
                
                if (newDist < oldDist) { e } else { acc }
            }
            assignedCluster[idx] = closestCluster
            
            println("2. 점 ${pt}의 클러스터 : ${closestCluster}, 각 클러스터 간 거리 : ${dists}")
        }
        println("3. 군집 할당 갱신 : ${assignedCluster}\n")
        
        // 군집 할당 미갱신 시 종료
        if (assignedCluster == oldAssignedCluster) {
            println("군집 할당이 이전과 동일\n최종 군집 할당 : ${assignedCluster}")
            break
        }
        
        // 각 군집별 중심점을 평균으로 갱신
        println(" << ${count}번째 군집 중심점 갱신 >>\n")
        (0 until K).forEachIndexed { k, _ ->
            
            // 해당 군집의 점들만 필터링
            val pointsInCurrentCluster = kMeansTest.points.filterIndexed { idx, _ ->
                assignedCluster[idx] == k
            }
            println("4. 군집 ${k} 의 점들 :")
            pointsInCurrentCluster.forEach {
                println(it)
            }
            
            // 해당 점들의 x, y, z좌표의 평균
            val nCurrent = pointsInCurrentCluster.size
            val xMean = pointsInCurrentCluster.sumOf { it.x } / nCurrent.toDouble()
            val yMean = pointsInCurrentCluster.sumOf { it.y } / nCurrent.toDouble()
            val zMean = pointsInCurrentCluster.sumOf { it.z } / nCurrent.toDouble()
            
            clusterCentroids[k] = Point(xMean, yMean, zMean)
            println("5. 군집 ${k}의 중심점 갱신 : (${xMean}, ${yMean}, ${zMean})\n")
        }
        
        println("6. 갱신된 중심점들 :")
        clusterCentroids.forEachIndexed { idx, it ->
            println("군집 ${idx} : ${it}")
        }
        print("\n")
        
        count++
    }
}

fun main() {
    
    // 임시 데이터
    val tempKMeansTest = KMeansTest(
        listOf<Point> (
            Point(7.4, 2.5, 2.8),
            Point(7.2, 1.3, 4.5),
            Point(6.5, 3.6, 3.1),
            Point(5.7, 2.8, 3.7),
            Point(7.9, 2.0, 1.3),
            Point(4.2, 6.9, 3.4),
            Point(1.8, 5.5, 3.6),
            Point(4.4, 5.0, 1.7),
            Point(3.6, 7.5, 2.4),
            Point(4.0, 1.2, 8.9),
            Point(3.2, 3.5, 7.1),
            Point(2.2, 4.1, 5.6),
            Point(3.5, 2.0, 6.4)
        ), 3
    )
    
    // K-means Clustering 알고리즘 실행
    runKMeans(tempKMeansTest)
}
```

### 실행 결과
```kotlin
1. 클러스터 중심의 초기값 :
[Point(x=8.0, y=4.0, z=6.0), Point(x=4.0, y=7.0, z=9.0), Point(x=8.0, y=7.0, z=5.0)]

 << 1번째 군집 할당 갱신 >>

2. 점 Point(x=7.4, y=2.5, z=2.8)의 클러스터 : 0, 각 클러스터 간 거리 : [3.58, 8.38, 5.04]
2. 점 Point(x=7.2, y=1.3, z=4.5)의 클러스터 : 0, 각 클러스터 간 거리 : [3.19, 7.94, 5.78]
2. 점 Point(x=6.5, y=3.6, z=3.1)의 클러스터 : 0, 각 클러스터 간 거리 : [3.29, 7.25, 4.17]
2. 점 Point(x=5.7, y=2.8, z=3.7)의 클러스터 : 0, 각 클러스터 간 거리 : [3.47, 6.97, 4.96]
2. 점 Point(x=7.9, y=2.0, z=1.3)의 클러스터 : 0, 각 클러스터 간 거리 : [5.11, 9.97, 6.22]
2. 점 Point(x=4.2, y=6.9, z=3.4)의 클러스터 : 2, 각 클러스터 간 거리 : [5.44, 5.6, 4.12]
2. 점 Point(x=1.8, y=5.5, z=3.6)의 클러스터 : 1, 각 클러스터 간 거리 : [6.82, 6.02, 6.53]
2. 점 Point(x=4.4, y=5.0, z=1.7)의 클러스터 : 2, 각 클러스터 간 거리 : [5.7, 7.58, 5.28]
2. 점 Point(x=3.6, y=7.5, z=2.4)의 클러스터 : 2, 각 클러스터 간 거리 : [6.68, 6.63, 5.14]
2. 점 Point(x=4.0, y=1.2, z=8.9)의 클러스터 : 0, 각 클러스터 간 거리 : [5.68, 5.8, 8.05]
2. 점 Point(x=3.2, y=3.5, z=7.1)의 클러스터 : 1, 각 클러스터 간 거리 : [4.95, 4.06, 6.3]
2. 점 Point(x=2.2, y=4.1, z=5.6)의 클러스터 : 1, 각 클러스터 간 거리 : [5.81, 4.82, 6.51]
2. 점 Point(x=3.5, y=2.0, z=6.4)의 클러스터 : 0, 각 클러스터 간 거리 : [4.94, 5.66, 6.87]
3. 군집 할당 갱신 : [0, 0, 0, 0, 0, 2, 1, 2, 2, 0, 1, 1, 0]

 << 1번째 군집 중심점 갱신 >>

4. 군집 0 의 점들 :
Point(x=7.4, y=2.5, z=2.8)
Point(x=7.2, y=1.3, z=4.5)
Point(x=6.5, y=3.6, z=3.1)
Point(x=5.7, y=2.8, z=3.7)
Point(x=7.9, y=2.0, z=1.3)
Point(x=4.0, y=1.2, z=8.9)
Point(x=3.5, y=2.0, z=6.4)
5. 군집 0의 중심점 갱신 : (6.028571428571429, 2.1999999999999997, 4.385714285714286)

4. 군집 1 의 점들 :
Point(x=1.8, y=5.5, z=3.6)
Point(x=3.2, y=3.5, z=7.1)
Point(x=2.2, y=4.1, z=5.6)
5. 군집 1의 중심점 갱신 : (2.4, 4.366666666666666, 5.433333333333333)

4. 군집 2 의 점들 :
Point(x=4.2, y=6.9, z=3.4)
Point(x=4.4, y=5.0, z=1.7)
Point(x=3.6, y=7.5, z=2.4)
5. 군집 2의 중심점 갱신 : (4.066666666666667, 6.466666666666666, 2.5)

6. 갱신된 중심점들 :
군집 0 : Point(x=6.028571428571429, y=2.1999999999999997, z=4.385714285714286)
군집 1 : Point(x=2.4, y=4.366666666666666, z=5.433333333333333)
군집 2 : Point(x=4.066666666666667, y=6.466666666666666, z=2.5)

 << 2번째 군집 할당 갱신 >>

2. 점 Point(x=7.4, y=2.5, z=2.8)의 클러스터 : 0, 각 클러스터 간 거리 : [2.12, 5.95, 5.19]
2. 점 Point(x=7.2, y=1.3, z=4.5)의 클러스터 : 0, 각 클러스터 간 거리 : [1.48, 5.77, 6.36]
2. 점 Point(x=6.5, y=3.6, z=3.1)의 클러스터 : 0, 각 클러스터 간 거리 : [1.96, 4.78, 3.81]
2. 점 Point(x=5.7, y=2.8, z=3.7)의 클러스터 : 0, 각 클러스터 간 거리 : [0.97, 4.04, 4.19]
2. 점 Point(x=7.9, y=2.0, z=1.3)의 클러스터 : 0, 각 클러스터 간 거리 : [3.61, 7.28, 6.01]
2. 점 Point(x=4.2, y=6.9, z=3.4)의 클러스터 : 2, 각 클러스터 간 거리 : [5.14, 3.71, 1.01]
2. 점 Point(x=1.8, y=5.5, z=3.6)의 클러스터 : 1, 각 클러스터 간 거리 : [5.42, 2.24, 2.7]
2. 점 Point(x=4.4, y=5.0, z=1.7)의 클러스터 : 2, 각 클러스터 간 거리 : [4.21, 4.28, 1.7]
2. 점 Point(x=3.6, y=7.5, z=2.4)의 클러스터 : 2, 각 클러스터 간 거리 : [6.16, 4.52, 1.14]
2. 점 Point(x=4.0, y=1.2, z=8.9)의 클러스터 : 1, 각 클러스터 간 거리 : [5.05, 4.96, 8.29]
2. 점 Point(x=3.2, y=3.5, z=7.1)의 클러스터 : 1, 각 클러스터 간 거리 : [4.13, 2.04, 5.54]
2. 점 Point(x=2.2, y=4.1, z=5.6)의 클러스터 : 1, 각 클러스터 간 거리 : [4.44, 0.37, 4.32]
2. 점 Point(x=3.5, y=2.0, z=6.4)의 클러스터 : 1, 각 클러스터 간 거리 : [3.24, 2.78, 5.96]
3. 군집 할당 갱신 : [0, 0, 0, 0, 0, 2, 1, 2, 2, 1, 1, 1, 1]

 << 2번째 군집 중심점 갱신 >>

4. 군집 0 의 점들 :
Point(x=7.4, y=2.5, z=2.8)
Point(x=7.2, y=1.3, z=4.5)
Point(x=6.5, y=3.6, z=3.1)
Point(x=5.7, y=2.8, z=3.7)
Point(x=7.9, y=2.0, z=1.3)
5. 군집 0의 중심점 갱신 : (6.94, 2.44, 3.0800000000000005)

4. 군집 1 의 점들 :
Point(x=1.8, y=5.5, z=3.6)
Point(x=4.0, y=1.2, z=8.9)
Point(x=3.2, y=3.5, z=7.1)
Point(x=2.2, y=4.1, z=5.6)
Point(x=3.5, y=2.0, z=6.4)
5. 군집 1의 중심점 갱신 : (2.94, 3.2599999999999993, 6.32)

4. 군집 2 의 점들 :
Point(x=4.2, y=6.9, z=3.4)
Point(x=4.4, y=5.0, z=1.7)
Point(x=3.6, y=7.5, z=2.4)
5. 군집 2의 중심점 갱신 : (4.066666666666667, 6.466666666666666, 2.5)

6. 갱신된 중심점들 :
군집 0 : Point(x=6.94, y=2.44, z=3.0800000000000005)
군집 1 : Point(x=2.94, y=3.2599999999999993, z=6.32)
군집 2 : Point(x=4.066666666666667, y=6.466666666666666, z=2.5)

 << 3번째 군집 할당 갱신 >>

2. 점 Point(x=7.4, y=2.5, z=2.8)의 클러스터 : 0, 각 클러스터 간 거리 : [0.54, 5.73, 5.19]
2. 점 Point(x=7.2, y=1.3, z=4.5)의 클러스터 : 0, 각 클러스터 간 거리 : [1.84, 5.03, 6.36]
2. 점 Point(x=6.5, y=3.6, z=3.1)의 클러스터 : 0, 각 클러스터 간 거리 : [1.24, 4.81, 3.81]
2. 점 Point(x=5.7, y=2.8, z=3.7)의 클러스터 : 0, 각 클러스터 간 거리 : [1.43, 3.83, 4.19]
2. 점 Point(x=7.9, y=2.0, z=1.3)의 클러스터 : 0, 각 클러스터 간 거리 : [2.07, 7.17, 6.01]
2. 점 Point(x=4.2, y=6.9, z=3.4)의 클러스터 : 2, 각 클러스터 간 거리 : [5.24, 4.83, 1.01]
2. 점 Point(x=1.8, y=5.5, z=3.6)의 클러스터 : 2, 각 클러스터 간 거리 : [6.0, 3.7, 2.7]
2. 점 Point(x=4.4, y=5.0, z=1.7)의 클러스터 : 2, 각 클러스터 간 거리 : [3.86, 5.15, 1.7]
2. 점 Point(x=3.6, y=7.5, z=2.4)의 클러스터 : 2, 각 클러스터 간 거리 : [6.1, 5.81, 1.14]
2. 점 Point(x=4.0, y=1.2, z=8.9)의 클러스터 : 1, 각 클러스터 간 거리 : [6.64, 3.47, 8.29]
2. 점 Point(x=3.2, y=3.5, z=7.1)의 클러스터 : 1, 각 클러스터 간 거리 : [5.59, 0.86, 5.54]
2. 점 Point(x=2.2, y=4.1, z=5.6)의 클러스터 : 1, 각 클러스터 간 거리 : [5.62, 1.33, 4.32]
2. 점 Point(x=3.5, y=2.0, z=6.4)의 클러스터 : 1, 각 클러스터 간 거리 : [4.8, 1.38, 5.96]
3. 군집 할당 갱신 : [0, 0, 0, 0, 0, 2, 2, 2, 2, 1, 1, 1, 1]

 << 3번째 군집 중심점 갱신 >>

4. 군집 0 의 점들 :
Point(x=7.4, y=2.5, z=2.8)
Point(x=7.2, y=1.3, z=4.5)
Point(x=6.5, y=3.6, z=3.1)
Point(x=5.7, y=2.8, z=3.7)
Point(x=7.9, y=2.0, z=1.3)
5. 군집 0의 중심점 갱신 : (6.94, 2.44, 3.0800000000000005)

4. 군집 1 의 점들 :
Point(x=4.0, y=1.2, z=8.9)
Point(x=3.2, y=3.5, z=7.1)
Point(x=2.2, y=4.1, z=5.6)
Point(x=3.5, y=2.0, z=6.4)
5. 군집 1의 중심점 갱신 : (3.225, 2.7, 7.0)

4. 군집 2 의 점들 :
Point(x=4.2, y=6.9, z=3.4)
Point(x=1.8, y=5.5, z=3.6)
Point(x=4.4, y=5.0, z=1.7)
Point(x=3.6, y=7.5, z=2.4)
5. 군집 2의 중심점 갱신 : (3.5, 6.225, 2.775)

6. 갱신된 중심점들 :
군집 0 : Point(x=6.94, y=2.44, z=3.0800000000000005)
군집 1 : Point(x=3.225, y=2.7, z=7.0)
군집 2 : Point(x=3.5, y=6.225, z=2.775)

 << 4번째 군집 할당 갱신 >>

2. 점 Point(x=7.4, y=2.5, z=2.8)의 클러스터 : 0, 각 클러스터 간 거리 : [0.54, 5.93, 5.39]
2. 점 Point(x=7.2, y=1.3, z=4.5)의 클러스터 : 0, 각 클러스터 간 거리 : [1.84, 4.9, 6.4]
2. 점 Point(x=6.5, y=3.6, z=3.1)의 클러스터 : 0, 각 클러스터 간 거리 : [1.24, 5.17, 4.0]
2. 점 Point(x=5.7, y=2.8, z=3.7)의 클러스터 : 0, 각 클러스터 간 거리 : [1.43, 4.13, 4.17]
2. 점 Point(x=7.9, y=2.0, z=1.3)의 클러스터 : 0, 각 클러스터 간 거리 : [2.07, 7.41, 6.28]
2. 점 Point(x=4.2, y=6.9, z=3.4)의 클러스터 : 2, 각 클러스터 간 거리 : [5.24, 5.62, 1.16]
2. 점 Point(x=1.8, y=5.5, z=3.6)의 클러스터 : 2, 각 클러스터 간 거리 : [6.0, 4.63, 2.02]
2. 점 Point(x=4.4, y=5.0, z=1.7)의 클러스터 : 2, 각 클러스터 간 거리 : [3.86, 5.9, 1.86]
2. 점 Point(x=3.6, y=7.5, z=2.4)의 클러스터 : 2, 각 클러스터 간 거리 : [6.1, 6.66, 1.33]
2. 점 Point(x=4.0, y=1.2, z=8.9)의 클러스터 : 1, 각 클러스터 간 거리 : [6.64, 2.54, 7.94]
2. 점 Point(x=3.2, y=3.5, z=7.1)의 클러스터 : 1, 각 클러스터 간 거리 : [5.59, 0.81, 5.12]
2. 점 Point(x=2.2, y=4.1, z=5.6)의 클러스터 : 1, 각 클러스터 간 거리 : [5.62, 2.23, 3.77]
2. 점 Point(x=3.5, y=2.0, z=6.4)의 클러스터 : 1, 각 클러스터 간 거리 : [4.8, 0.96, 5.57]
3. 군집 할당 갱신 : [0, 0, 0, 0, 0, 2, 2, 2, 2, 1, 1, 1, 1]

군집 할당이 이전과 동일
최종 군집 할당 : [0, 0, 0, 0, 0, 2, 2, 2, 2, 1, 1, 1, 1]
```
