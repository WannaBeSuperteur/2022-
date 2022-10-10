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

## 코드 및 실행 결과
### 코드
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

// true, false인 각 경우에 대해 P(y|x)를 계산
// P(y|x) = P(x1=l1|y) * P(x2=l2|y) * ... * P(xn=ln|y) * P(y)를 이용
fun computeProb(
    naiveBayesRows: List<Row>,
    logicalValue: Boolean,
    logicalsOfNewRow: List<Boolean>
): Double {
    
    // P(y) 를 계산
    val n = naiveBayesRows.size
    val nLogical = naiveBayesRows.count {
        it.target == logicalValue
    }
    val py = nLogical / n.toDouble()
    println("1. prob of (y = ${logicalValue}) = ${py}")
    
    // P(x1=l|y), P(x2|y), ..., P(xn|y) 를 계산
    val cols = naiveBayesRows[0].values.size
    val pxy = (0 until cols).map { idx ->
        val y = naiveBayesRows.filter { // y에 해당하는 행들
            it.target == logicalValue
        }
        val xy = y.filter { // x[i]=l[i], y에 모두 해당하는 행들
            it.values[idx] == logicalsOfNewRow[idx]
        }
        
        // P(x[i]=l[i] | y) = P(x[i]=l[i], y) / P(y)
        xy.size / y.size.toDouble()
    }
    pxy.forEachIndexed { idx, it ->
        println("2. prob of (x[${idx}] = ${logicalsOfNewRow[idx]}) with (y = ${logicalValue}) : ${it}")
    }
    
    // P(x1=l1|y) * P(x2=l2|y) * ... * P(xn=ln|y) 를 계산
    val pxyProduct = pxy.fold (1.0) { acc, e ->
        acc * e
    }
    println("3. product of these probs = ${pxyProduct}")
    
    // P(x1=l1|y) * P(x2=l2|y) * ... * P(xn=ln|y) * P(y) 를 계산
    val finalProb = pxyProduct * py
    println("4. final probability of (target = ${logicalValue}) : ${finalProb}\n")
    
    return finalProb
}

// Naive Bayes 알고리즘을 이용하여 new_rows의 클래스를 출력
fun findClassOfNewRows(naiveBayesTest: NaiveBayesTest) {
    naiveBayesTest.new_rows.forEach {
        val probTrue = computeProb(naiveBayesTest.rows, true, it.values)
        val probFalse = computeProb(naiveBayesTest.rows, false, it.values)
        
        println("${it.values}의 확률: ${probTrue}, ${probFalse}\n최종 분류: ${probTrue >= probFalse}\n")
    }
}

fun main() {
    
    // 임시 데이터
    val tempNaiveBayesTest = NaiveBayesTest(
        listOf<Row> (
            Row(listOf(true, true, false, true), true),
            Row(listOf(true, true, false, true), true),
            Row(listOf(true, true, false, false), true),
            Row(listOf(true, false, false, false), true),
            Row(listOf(true, true, false, true), true),
            Row(listOf(true, true, true, true), true),
            Row(listOf(false, true, false, true), true),
            Row(listOf(true, false, true, false), false),
            Row(listOf(false, false, true, false), false),
            Row(listOf(false, true, true, true), false),
            Row(listOf(false, true, false, false), false),
            Row(listOf(false, false, true, false), false)
        ),
        listOf<Row> (
            Row(listOf(false, false, false, false), false),
            Row(listOf(false, false, false, true), false),
            Row(listOf(false, false, true, true), false),
            Row(listOf(false, true, true, false), false),
            Row(listOf(true, false, false, true), false),
            Row(listOf(true, false, true, true), false),
            Row(listOf(true, true, true, false), false)
        )
    )
    
    // new_rows의 클래스 계산 및 출력
    findClassOfNewRows(tempNaiveBayesTest)
}
```

### 실행 결과
```kotlin
1. prob of (y = true) = 0.5833333333333334
2. prob of (x[0] = false) with (y = true) : 0.14285714285714285
2. prob of (x[1] = false) with (y = true) : 0.14285714285714285
2. prob of (x[2] = false) with (y = true) : 0.8571428571428571
2. prob of (x[3] = false) with (y = true) : 0.2857142857142857
3. product of these probs = 0.004997917534360682
4. final probability of (target = true) : 0.0029154518950437313

1. prob of (y = false) = 0.4166666666666667
2. prob of (x[0] = false) with (y = false) : 0.8
2. prob of (x[1] = false) with (y = false) : 0.6
2. prob of (x[2] = false) with (y = false) : 0.2
2. prob of (x[3] = false) with (y = false) : 0.8
3. product of these probs = 0.07680000000000001
4. final probability of (target = false) : 0.03200000000000001

[false, false, false, false]의 확률: 0.0029154518950437313, 0.03200000000000001
최종 분류: false

1. prob of (y = true) = 0.5833333333333334
2. prob of (x[0] = false) with (y = true) : 0.14285714285714285
2. prob of (x[1] = false) with (y = true) : 0.14285714285714285
2. prob of (x[2] = false) with (y = true) : 0.8571428571428571
2. prob of (x[3] = true) with (y = true) : 0.7142857142857143
3. product of these probs = 0.012494793835901706
4. final probability of (target = true) : 0.007288629737609329

1. prob of (y = false) = 0.4166666666666667
2. prob of (x[0] = false) with (y = false) : 0.8
2. prob of (x[1] = false) with (y = false) : 0.6
2. prob of (x[2] = false) with (y = false) : 0.2
2. prob of (x[3] = true) with (y = false) : 0.2
3. product of these probs = 0.019200000000000002
4. final probability of (target = false) : 0.008000000000000002

[false, false, false, true]의 확률: 0.007288629737609329, 0.008000000000000002
최종 분류: false

1. prob of (y = true) = 0.5833333333333334
2. prob of (x[0] = false) with (y = true) : 0.14285714285714285
2. prob of (x[1] = false) with (y = true) : 0.14285714285714285
2. prob of (x[2] = true) with (y = true) : 0.14285714285714285
2. prob of (x[3] = true) with (y = true) : 0.7142857142857143
3. product of these probs = 0.002082465639316951
4. final probability of (target = true) : 0.001214771622934888

1. prob of (y = false) = 0.4166666666666667
2. prob of (x[0] = false) with (y = false) : 0.8
2. prob of (x[1] = false) with (y = false) : 0.6
2. prob of (x[2] = true) with (y = false) : 0.8
2. prob of (x[3] = true) with (y = false) : 0.2
3. product of these probs = 0.07680000000000001
4. final probability of (target = false) : 0.03200000000000001

[false, false, true, true]의 확률: 0.001214771622934888, 0.03200000000000001
최종 분류: false

1. prob of (y = true) = 0.5833333333333334
2. prob of (x[0] = false) with (y = true) : 0.14285714285714285
2. prob of (x[1] = true) with (y = true) : 0.8571428571428571
2. prob of (x[2] = true) with (y = true) : 0.14285714285714285
2. prob of (x[3] = false) with (y = true) : 0.2857142857142857
3. product of these probs = 0.004997917534360682
4. final probability of (target = true) : 0.0029154518950437313

1. prob of (y = false) = 0.4166666666666667
2. prob of (x[0] = false) with (y = false) : 0.8
2. prob of (x[1] = true) with (y = false) : 0.4
2. prob of (x[2] = true) with (y = false) : 0.8
2. prob of (x[3] = false) with (y = false) : 0.8
3. product of these probs = 0.20480000000000007
4. final probability of (target = false) : 0.08533333333333336

[false, true, true, false]의 확률: 0.0029154518950437313, 0.08533333333333336
최종 분류: false

1. prob of (y = true) = 0.5833333333333334
2. prob of (x[0] = true) with (y = true) : 0.8571428571428571
2. prob of (x[1] = false) with (y = true) : 0.14285714285714285
2. prob of (x[2] = false) with (y = true) : 0.8571428571428571
2. prob of (x[3] = true) with (y = true) : 0.7142857142857143
3. product of these probs = 0.07496876301541024
4. final probability of (target = true) : 0.043731778425655975

1. prob of (y = false) = 0.4166666666666667
2. prob of (x[0] = true) with (y = false) : 0.2
2. prob of (x[1] = false) with (y = false) : 0.6
2. prob of (x[2] = false) with (y = false) : 0.2
2. prob of (x[3] = true) with (y = false) : 0.2
3. product of these probs = 0.0048000000000000004
4. final probability of (target = false) : 0.0020000000000000005

[true, false, false, true]의 확률: 0.043731778425655975, 0.0020000000000000005
최종 분류: true

1. prob of (y = true) = 0.5833333333333334
2. prob of (x[0] = true) with (y = true) : 0.8571428571428571
2. prob of (x[1] = false) with (y = true) : 0.14285714285714285
2. prob of (x[2] = true) with (y = true) : 0.14285714285714285
2. prob of (x[3] = true) with (y = true) : 0.7142857142857143
3. product of these probs = 0.012494793835901706
4. final probability of (target = true) : 0.007288629737609329

1. prob of (y = false) = 0.4166666666666667
2. prob of (x[0] = true) with (y = false) : 0.2
2. prob of (x[1] = false) with (y = false) : 0.6
2. prob of (x[2] = true) with (y = false) : 0.8
2. prob of (x[3] = true) with (y = false) : 0.2
3. product of these probs = 0.019200000000000002
4. final probability of (target = false) : 0.008000000000000002

[true, false, true, true]의 확률: 0.007288629737609329, 0.008000000000000002
최종 분류: false

1. prob of (y = true) = 0.5833333333333334
2. prob of (x[0] = true) with (y = true) : 0.8571428571428571
2. prob of (x[1] = true) with (y = true) : 0.8571428571428571
2. prob of (x[2] = true) with (y = true) : 0.14285714285714285
2. prob of (x[3] = false) with (y = true) : 0.2857142857142857
3. product of these probs = 0.029987505206164094
4. final probability of (target = true) : 0.017492711370262388

1. prob of (y = false) = 0.4166666666666667
2. prob of (x[0] = true) with (y = false) : 0.2
2. prob of (x[1] = true) with (y = false) : 0.4
2. prob of (x[2] = true) with (y = false) : 0.8
2. prob of (x[3] = false) with (y = false) : 0.8
3. product of these probs = 0.051200000000000016
4. final probability of (target = false) : 0.02133333333333334

[true, true, true, false]의 확률: 0.017492711370262388, 0.02133333333333334
최종 분류: false
```
