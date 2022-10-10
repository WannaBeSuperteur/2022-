## 요구사항 분석
기존 3차원 점들의 집합 ```points```와 새로운 3차원 점들의 집합 ```new_points```, ```N```의 값이 주어졌을 때, k-Nearest Neighbor 방법을 이용하여 ```new_points```의 각 점들이 어떤 분류에 속하는지 구하시오.

단, 가장 가까이 있는 ```N```개의 기존 3차원 점들을 이용하며, 각 기존 점들의 가중치는 거리에 반비례한다. 또한, 거리를 계산하기 전에 먼저 ```x```, ```y```, ```z``` 값을 각각 ```points```의 점들의 해당 값들의 평균과 표준편차를 이용하여 표준정규분포로 표준화한다.

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

## 코드 및 실행 결과
### 코드
```kotlin
import kotlin.math.*

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

// 표준편차 구하기 (예제 34의 함수를 그대로 사용)
fun computeStandardDeviation(values: List<Double>): Double {
    val n = values.size
    val mean = values.sum() / n.toDouble()
    val sumPart = (0 until n).sumOf {
        (values[it] - mean) * (values[it] - mean)
    }
    
    val v = sumPart / n.toDouble()
    return sqrt(v)
}

// 평균 및 표준편차 구하기
fun computeMeanAndSd(values: List<Double>): List<Double> {
    val mean = values.sum() / values.size.toDouble()
    val std = computeStandardDeviation(values)
    return listOf(mean, std)
}

// 표준화된 점으로 매핑
fun normalizePoints(
    points: List<Point>,
    xMeanAndStd: List<Double>,
    yMeanAndStd: List<Double>,
    zMeanAndStd: List<Double>
): List<Point> {
    return points.map {
        Point(
            (it.x - xMeanAndStd[0]) / xMeanAndStd[1],
            (it.y - yMeanAndStd[0]) / yMeanAndStd[1],
            (it.z - zMeanAndStd[0]) / zMeanAndStd[1],
            it.classNo
        )
    }
}

// 거리 계산
fun computeDist(point0: Point, point1: Point): Double {
    val xDist = point0.x - point1.x
    val yDist = point0.y - point1.y
    val zDist = point0.z - point1.z
    return sqrt(xDist * xDist + yDist * yDist + zDist * zDist)
}

// 새로운 점들의 분류 계산 및 출력
fun classifyNewPoints(kNNTest: KNNTest) {
    
    // val nPts = kNNTest.points.size
    // val nNew = kNNTest.new_points.size
    
    // 기존 점들의 위치의 평균 및 표준편차 구하기
    val xMeanAndStd = computeMeanAndSd(kNNTest.points.map { it.x })
    val yMeanAndStd = computeMeanAndSd(kNNTest.points.map { it.y })
    val zMeanAndStd = computeMeanAndSd(kNNTest.points.map { it.z })
    
    println("1. mean and std")
    println("x : ${xMeanAndStd}")
    println("y : ${yMeanAndStd}")
    println("z : ${zMeanAndStd}")
    
    // 기존 점 및 새로운 점들의 위치를 표준화
    val ptsNormalized = normalizePoints(
        kNNTest.points, xMeanAndStd, yMeanAndStd, zMeanAndStd
    )
    println("\n2. normalized points")
    ptsNormalized.forEach { println(it) }
    
    val newPtsNormalized = normalizePoints(
        kNNTest.new_points, xMeanAndStd, yMeanAndStd, zMeanAndStd
    )
    println("\n3. normalized new points")
    newPtsNormalized.forEach { println(it) }
    
    // 각각의 새로운 점들에 대해, 가장 가까운 기존 점 N개 찾기
    newPtsNormalized.forEach { npn ->
        
        // 가장 가까운 기존 점 N개
        val pointsSorted = ptsNormalized.sortedBy { pn ->
            computeDist(npn, pn)
        }.take(kNNTest.N)
        
        println("\n4. sorted ${kNNTest.N} points:")
        println("new point: ${npn}")
        pointsSorted.forEach { println(it) }
        
        // 가중치와 클래스로 매핑
        val pointsWeightAndClass = pointsSorted.map { pn ->
            val dist = computeDist(npn, pn)
            listOf(1.0 / dist, pn.classNo)
        }
        println("\n5. weight and class of sorted ${kNNTest.N} points:")
        pointsWeightAndClass.forEach { println(it) }
        
        // 클래스 할당
        val weightClasses = (0..1).map { cl ->
            val mapped = pointsWeightAndClass.filter { pwc ->
                pwc[1] == cl
            }.map {
                it[0].toDouble() // 숫자 형태로 바꾸지 않으면 오류 발생 !!
            }
            println("mapped (${cl}) : ${mapped}")
            mapped.sum()
        }
        println("\n6. weight of each class : ${weightClasses}")
        val finalClass = (0..1).fold ( 0 ) { acc, e ->
            if (weightClasses[e] > weightClasses[acc]) { e } else { acc }
        }
        
        // 클래스 출력
        val originalX = npn.x * xMeanAndStd[1] + xMeanAndStd[0]
        val originalY = npn.y * yMeanAndStd[1] + yMeanAndStd[0]
        val originalZ = npn.z * zMeanAndStd[1] + zMeanAndStd[0]
        println("\n7. (${originalX}, ${originalY}, ${originalZ}) 의 최종 분류 : ${finalClass}")
    }
}

fun main() {
    
    // 임시 데이터
    val tempKNNTest = KNNTest (
        listOf<Point> (
            Point(1.4, 2.5, 6.8, 0),
            Point(3.6, 2.1, 6.0, 0),
            Point(2.8, 4.5, 7.4, 0),
            Point(3.1, 2.7, 5.9, 0),
            Point(1.1, 5.3, 9.7, 0),
            Point(8.4, 6.1, 3.7, 1),
            Point(9.1, 7.5, 3.0, 1),
            Point(4.9, 6.8, 5.1, 1),
            Point(5.3, 7.4, 5.5, 1),
            Point(9.3, 8.2, 2.7, 1)
        ),
        listOf<Point> (
            Point(1.3, 2.9, 6.0, -1),
            Point(3.1, 3.3, 5.6, -1),
            Point(7.4, 6.2, 1.8, -1),
            Point(7.0, 8.3, 2.5, -1),
            Point(4.7, 5.3, 6.6, -1),
            Point(6.0, 4.4, 5.9, -1)
        ),
        3
    )
    
    // 새로운 점들의 분류 계산
    classifyNewPoints(tempKNNTest)
}
```

### 실행 결과
```kotlin
1. mean and std
x : [4.9, 2.9230121450312176]
y : [5.31, 2.145437018418392]
z : [5.58, 2.0193068117549644]

2. normalized points
Point(x=-1.1973949564149418, y=-1.3097564626117624, z=0.6041677237446186, classNo=0)
Point(x=-0.4447466980969784, y=-1.4961986636952873, z=0.20799216719077032, classNo=0)
Point(x=-0.7184369738489652, y=-0.3775454571941378, z=0.901299391160005, classNo=0)
Point(x=-0.61580312044197, y=-1.21653536207, z=0.15847022262153945, classNo=0)
Point(x=-1.3000288098219368, y=-0.0046610550270880236, z=2.0403041162523183, classNo=0)
Point(x=1.1973949564149418, y=0.36822334713996174, z=-0.9310125579015435, classNo=1)
Point(x=1.4368739476979298, y=1.0207710509322991, z=-1.2776661698861609, classNo=1)
Point(x=0.0, y=0.6944971990361304, z=-0.2377053339323092, classNo=1)
Point(x=0.13684513787599314, y=0.9741605006614181, z=-0.03961755565538486, classNo=1)
Point(x=1.505296516635927, y=1.3470449028284674, z=-1.4262320035938538, classNo=1)

3. normalized new points
Point(x=-1.23160624088394, y=-1.1233142615282374, z=0.20799216719077032, classNo=-1)
Point(x=-0.61580312044197, y=-0.9368720604447126, z=0.009904388913845995, classNo=-1)
Point(x=0.8552821117249584, y=0.4148338974108432, z=-1.8719295047169333, classNo=-1)
Point(x=0.7184369738489649, y=1.3936554530993492, z=-1.525275892732316, classNo=-1)
Point(x=-0.06842256893799673, y=-0.0046610550270880236, z=0.5051238346061564, classNo=-1)
Point(x=0.37632412915898156, y=-0.42415600746501886, z=0.15847022262153945, classNo=-1)

4. sorted 3 points:
new point: Point(x=-1.23160624088394, y=-1.1233142615282374, z=0.20799216719077032, classNo=-1)
Point(x=-1.1973949564149418, y=-1.3097564626117624, z=0.6041677237446186, classNo=0)
Point(x=-0.61580312044197, y=-1.21653536207, z=0.15847022262153945, classNo=0)
Point(x=-0.4447466980969784, y=-1.4961986636952873, z=0.20799216719077032, classNo=0)

5. weight and class of sorted 3 points:
[2.27692877824892, 0]
[1.6005510332503687, 0]
[1.1484464998396315, 0]
mapped (0) : [2.27692877824892, 1.6005510332503687, 1.1484464998396315]
mapped (1) : []

6. weight of each class : [5.02592631133892, 0.0]

7. (1.2999999999999998, 2.9, 6.0) 의 최종 분류 : 0

4. sorted 3 points:
new point: Point(x=-0.61580312044197, y=-0.9368720604447126, z=0.009904388913845995, classNo=-1)
Point(x=-0.61580312044197, y=-1.21653536207, z=0.15847022262153945, classNo=0)
Point(x=-0.4447466980969784, y=-1.4961986636952873, z=0.20799216719077032, classNo=0)
Point(x=-1.1973949564149418, y=-1.3097564626117624, z=0.6041677237446186, classNo=0)

5. weight and class of sorted 3 points:
[3.157806699134148, 0]
[1.6193502454525928, 0]
[1.097351277144443, 0]
mapped (0) : [3.157806699134148, 1.6193502454525928, 1.097351277144443]
mapped (1) : []

6. weight of each class : [5.874508221731184, 0.0]

7. (3.1, 3.3, 5.6) 의 최종 분류 : 0

4. sorted 3 points:
new point: Point(x=0.8552821117249584, y=0.4148338974108432, z=-1.8719295047169333, classNo=-1)
Point(x=1.1973949564149418, y=0.36822334713996174, z=-0.9310125579015435, classNo=1)
Point(x=1.4368739476979298, y=1.0207710509322991, z=-1.2776661698861609, classNo=1)
Point(x=1.505296516635927, y=1.3470449028284674, z=-1.4262320035938538, classNo=1)

5. weight and class of sorted 3 points:
[0.9977384735998112, 1]
[0.971947282600401, 1]
[0.8191817959004049, 1]
mapped (0) : []
mapped (1) : [0.9977384735998112, 0.971947282600401, 0.8191817959004049]

6. weight of each class : [0.0, 2.788867552100617]

7. (7.4, 6.2, 1.7999999999999998) 의 최종 분류 : 1

4. sorted 3 points:
new point: Point(x=0.7184369738489649, y=1.3936554530993492, z=-1.525275892732316, classNo=-1)
Point(x=1.505296516635927, y=1.3470449028284674, z=-1.4262320035938538, classNo=1)
Point(x=1.4368739476979298, y=1.0207710509322991, z=-1.2776661698861609, classNo=1)
Point(x=1.1973949564149418, y=0.36822334713996174, z=-0.9310125579015435, classNo=1)

5. weight and class of sorted 3 points:
[1.2587530256505925, 1]
[1.1813820718495853, 1]
[0.7822866379173267, 1]
mapped (0) : []
mapped (1) : [1.2587530256505925, 1.1813820718495853, 0.7822866379173267]

6. weight of each class : [0.0, 3.2224217354175044]

7. (7.0, 8.3, 2.5) 의 최종 분류 : 1

4. sorted 3 points:
new point: Point(x=-0.06842256893799673, y=-0.0046610550270880236, z=0.5051238346061564, classNo=-1)
Point(x=-0.7184369738489652, y=-0.3775454571941378, z=0.901299391160005, classNo=0)
Point(x=0.0, y=0.6944971990361304, z=-0.2377053339323092, classNo=1)
Point(x=0.13684513787599314, y=0.9741605006614181, z=-0.03961755565538486, classNo=1)

5. weight and class of sorted 3 points:
[1.1797272315029883, 0]
[0.9780920179541822, 1]
[0.8780820373698838, 1]
mapped (0) : [1.1797272315029883]
mapped (1) : [0.9780920179541822, 0.8780820373698838]

6. weight of each class : [1.1797272315029883, 1.856174055324066]

7. (4.7, 5.3, 6.6) 의 최종 분류 : 1

4. sorted 3 points:
new point: Point(x=0.37632412915898156, y=-0.42415600746501886, z=0.15847022262153945, classNo=-1)
Point(x=0.0, y=0.6944971990361304, z=-0.2377053339323092, classNo=1)
Point(x=-0.61580312044197, y=-1.21653536207, z=0.15847022262153945, classNo=0)
Point(x=-0.7184369738489652, y=-0.3775454571941378, z=0.901299391160005, classNo=0)

5. weight and class of sorted 3 points:
[0.803229714415468, 1]
[0.7875770105305687, 0]
[0.7553961286467623, 0]
mapped (0) : [0.7875770105305687, 0.7553961286467623]
mapped (1) : [0.803229714415468]

6. weight of each class : [1.542973139177331, 0.803229714415468]

7. (6.0, 4.4, 5.9) 의 최종 분류 : 0
```
