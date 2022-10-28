## 요구사항 분석
다음의 계산식을 만족시키는, 10보다 크지 않은 음이 아닌 정수 ```a```, ```b```, ```c```의 값의 순서쌍을 모두 찾아서 출력하는 함수 ```printSolutions(formula)```를 작성하시오.

계산식 예시:
```kotlin
a + bc = ab + c
```

출력 예시:
```kotlin
(a, b, c) = (0, 0, 0)
(a, b, c) = (1, 1, 1)
(a, b, c) = (1, 2, 1)
...
```

단, 정렬 순서는 출력 결과의 각 정수의 값들을 기준으로 오름차순이며, 정수는 a, b, c가 아닐 수도 있다. 또한, 연산 기호로는 ```+```, ```*```(생략) 의 2가지만 사용할 수 있다.

## 코드 및 실행 결과
### 코드
```kotlin
import kotlin.math.*

// 수식의 값 계산
fun computeFormula(formula: String, mapping: Map<Char, Int>): Int {
    val formulaSplit = formula.split(" + ")
    
    // + 기호로 구분한 각 서브 수식에 대해
    return formulaSplit.sumOf {
        
        // 숫자의 개수 및 계수 구하기
        val countNum = it.count { it.isDigit() }
        val number = if (countNum == 0) {
            1
        } else {
            it.substring(0, countNum).toInt()
        }
        
        // 알파벳의 값 곱해 나가기
        it.substring(countNum).fold (number) { acc, e ->
            acc * mapping[e]!!
        }
    }
}

// 조건을 만족시키는 순서쌍 출력
fun printSolutions(formula: String) {
    println("\n1. formula = ${formula}")
    
    // 알파벳의 집합 찾기
    val alphabets = Regex("[^a-z]").replace(formula, "")
    val alphabetSet = alphabets.toSet()
    println("2. alphabets = ${alphabets}, set = ${alphabetSet}")
    
    // 좌변과 우변으로 나누기
    val left = formula.substringBefore(" = ")
    val right = formula.substringAfter(" = ")
    
    // 전체 경우의 수는 0 ~ 10이므로 각 알파벳마다 11가지 -> 11^n 가지가 됨
    // 각각의 케이스마다 좌변과 우변을 계산하여 일치하면 출력
    val n = alphabetSet.size
    val cases = (11.0).pow(n.toDouble()).toInt()
    println("3. total cases = ${cases}\n")
    
    val alphabetList = alphabetSet.toList()
    
    (0 until cases).forEach { c ->
        
        // 각 알파벳의 값을 설정 및 값의 mapping 생성
        val alphabetValues = (0 until n).map { a ->
            (c / (11.0).pow(n - 1 - a.toDouble()).toInt()) % 11
        }
        val alphabetToValue = (0 until n).fold ( mapOf<Char, Int>() ) { acc, e ->
            acc + mapOf(alphabetList[e] to alphabetValues[e])
        }
        
        // 좌변 및 우변 계산
        val leftResult = computeFormula(left, alphabetToValue)
        val rightResult = computeFormula(right, alphabetToValue)
        
        // 좌변과 우변의 값이 일치하는 경우 출력
        if (leftResult == rightResult) {
            val alphabetPrint = "(${alphabetList.joinToString(", ")})"
            val valuePrint = "(${alphabetValues.joinToString(", ")})"
            println("${alphabetPrint} = ${valuePrint}")
        }
    }
}

fun main() {
    
    // 수식 목록
    val tempFormulaList = listOf(
        "a + b = 5",
        "a + b = ab",
        "x + y = xy",
        "a + b + c = 4",
        "a + b + c = abc",
        "x + y + z = xyz",
        "abc = 12",
        "2a + b = ab",
        "3a + 2b + c = abc",
        "12a + 11b + 10c = 6abc",
        "ab + cd = ac + bd + 1",
        "xy + yz + xz = xyz"
    )
    
    // 각 목록에 대해 계산 결과 출력하기
    tempFormulaList.forEach {
        printSolutions(it)
    }
}
```

### 실행 결과
```kotlin

1. formula = a + b = 5
2. alphabets = ab, set = [a, b]
3. total cases = 121

(a, b) = (0, 5)
(a, b) = (1, 4)
(a, b) = (2, 3)
(a, b) = (3, 2)
(a, b) = (4, 1)
(a, b) = (5, 0)

1. formula = a + b = ab
2. alphabets = abab, set = [a, b]
3. total cases = 121

(a, b) = (0, 0)
(a, b) = (2, 2)

1. formula = x + y = xy
2. alphabets = xyxy, set = [x, y]
3. total cases = 121

(x, y) = (0, 0)
(x, y) = (2, 2)

1. formula = a + b + c = 4
2. alphabets = abc, set = [a, b, c]
3. total cases = 1331

(a, b, c) = (0, 0, 4)
(a, b, c) = (0, 1, 3)
(a, b, c) = (0, 2, 2)
(a, b, c) = (0, 3, 1)
(a, b, c) = (0, 4, 0)
(a, b, c) = (1, 0, 3)
(a, b, c) = (1, 1, 2)
(a, b, c) = (1, 2, 1)
(a, b, c) = (1, 3, 0)
(a, b, c) = (2, 0, 2)
(a, b, c) = (2, 1, 1)
(a, b, c) = (2, 2, 0)
(a, b, c) = (3, 0, 1)
(a, b, c) = (3, 1, 0)
(a, b, c) = (4, 0, 0)

1. formula = a + b + c = abc
2. alphabets = abcabc, set = [a, b, c]
3. total cases = 1331

(a, b, c) = (0, 0, 0)
(a, b, c) = (1, 2, 3)
(a, b, c) = (1, 3, 2)
(a, b, c) = (2, 1, 3)
(a, b, c) = (2, 3, 1)
(a, b, c) = (3, 1, 2)
(a, b, c) = (3, 2, 1)

1. formula = x + y + z = xyz
2. alphabets = xyzxyz, set = [x, y, z]
3. total cases = 1331

(x, y, z) = (0, 0, 0)
(x, y, z) = (1, 2, 3)
(x, y, z) = (1, 3, 2)
(x, y, z) = (2, 1, 3)
(x, y, z) = (2, 3, 1)
(x, y, z) = (3, 1, 2)
(x, y, z) = (3, 2, 1)

1. formula = abc = 12
2. alphabets = abc, set = [a, b, c]
3. total cases = 1331

(a, b, c) = (1, 2, 6)
(a, b, c) = (1, 3, 4)
(a, b, c) = (1, 4, 3)
(a, b, c) = (1, 6, 2)
(a, b, c) = (2, 1, 6)
(a, b, c) = (2, 2, 3)
(a, b, c) = (2, 3, 2)
(a, b, c) = (2, 6, 1)
(a, b, c) = (3, 1, 4)
(a, b, c) = (3, 2, 2)
(a, b, c) = (3, 4, 1)
(a, b, c) = (4, 1, 3)
(a, b, c) = (4, 3, 1)
(a, b, c) = (6, 1, 2)
(a, b, c) = (6, 2, 1)

1. formula = 2a + b = ab
2. alphabets = abab, set = [a, b]
3. total cases = 121

(a, b) = (0, 0)
(a, b) = (2, 4)
(a, b) = (3, 3)

1. formula = 3a + 2b + c = abc
2. alphabets = abcabc, set = [a, b, c]
3. total cases = 1331

(a, b, c) = (0, 0, 0)
(a, b, c) = (1, 2, 7)
(a, b, c) = (1, 6, 3)
(a, b, c) = (2, 1, 8)
(a, b, c) = (2, 4, 2)
(a, b, c) = (3, 10, 1)
(a, b, c) = (6, 1, 4)
(a, b, c) = (6, 2, 2)
(a, b, c) = (9, 4, 1)

1. formula = 12a + 11b + 10c = 6abc
2. alphabets = abcabc, set = [a, b, c]
3. total cases = 1331

(a, b, c) = (0, 0, 0)
(a, b, c) = (1, 4, 4)
(a, b, c) = (1, 6, 3)

1. formula = ab + cd = ac + bd + 1
2. alphabets = abcdacbd, set = [a, b, c, d]
3. total cases = 14641

(a, b, c, d) = (0, 0, 1, 1)
(a, b, c, d) = (0, 1, 2, 1)
(a, b, c, d) = (0, 2, 3, 1)
(a, b, c, d) = (0, 3, 4, 1)
(a, b, c, d) = (0, 4, 5, 1)
(a, b, c, d) = (0, 5, 6, 1)
(a, b, c, d) = (0, 6, 7, 1)
(a, b, c, d) = (0, 7, 8, 1)
(a, b, c, d) = (0, 8, 9, 1)
(a, b, c, d) = (0, 9, 10, 1)
(a, b, c, d) = (1, 0, 1, 2)
(a, b, c, d) = (1, 1, 0, 0)
(a, b, c, d) = (1, 1, 2, 2)
(a, b, c, d) = (1, 2, 1, 0)
(a, b, c, d) = (1, 2, 3, 2)
(a, b, c, d) = (1, 3, 2, 0)
(a, b, c, d) = (1, 3, 4, 2)
(a, b, c, d) = (1, 4, 3, 0)
(a, b, c, d) = (1, 4, 5, 2)
(a, b, c, d) = (1, 5, 4, 0)
(a, b, c, d) = (1, 5, 6, 2)
(a, b, c, d) = (1, 6, 5, 0)
(a, b, c, d) = (1, 6, 7, 2)
(a, b, c, d) = (1, 7, 6, 0)
(a, b, c, d) = (1, 7, 8, 2)
(a, b, c, d) = (1, 8, 7, 0)
(a, b, c, d) = (1, 8, 9, 2)
(a, b, c, d) = (1, 9, 8, 0)
(a, b, c, d) = (1, 9, 10, 2)
(a, b, c, d) = (1, 10, 9, 0)
(a, b, c, d) = (2, 0, 1, 3)
(a, b, c, d) = (2, 1, 0, 1)
(a, b, c, d) = (2, 1, 2, 3)
(a, b, c, d) = (2, 2, 1, 1)
(a, b, c, d) = (2, 2, 3, 3)
(a, b, c, d) = (2, 3, 2, 1)
(a, b, c, d) = (2, 3, 4, 3)
(a, b, c, d) = (2, 4, 3, 1)
(a, b, c, d) = (2, 4, 5, 3)
(a, b, c, d) = (2, 5, 4, 1)
(a, b, c, d) = (2, 5, 6, 3)
(a, b, c, d) = (2, 6, 5, 1)
(a, b, c, d) = (2, 6, 7, 3)
(a, b, c, d) = (2, 7, 6, 1)
(a, b, c, d) = (2, 7, 8, 3)
(a, b, c, d) = (2, 8, 7, 1)
(a, b, c, d) = (2, 8, 9, 3)
(a, b, c, d) = (2, 9, 8, 1)
(a, b, c, d) = (2, 9, 10, 3)
(a, b, c, d) = (2, 10, 9, 1)
(a, b, c, d) = (3, 0, 1, 4)
(a, b, c, d) = (3, 1, 0, 2)
(a, b, c, d) = (3, 1, 2, 4)
(a, b, c, d) = (3, 2, 1, 2)
(a, b, c, d) = (3, 2, 3, 4)
(a, b, c, d) = (3, 3, 2, 2)
(a, b, c, d) = (3, 3, 4, 4)
(a, b, c, d) = (3, 4, 3, 2)
(a, b, c, d) = (3, 4, 5, 4)
(a, b, c, d) = (3, 5, 4, 2)
(a, b, c, d) = (3, 5, 6, 4)
(a, b, c, d) = (3, 6, 5, 2)
(a, b, c, d) = (3, 6, 7, 4)
(a, b, c, d) = (3, 7, 6, 2)
(a, b, c, d) = (3, 7, 8, 4)
(a, b, c, d) = (3, 8, 7, 2)
(a, b, c, d) = (3, 8, 9, 4)
(a, b, c, d) = (3, 9, 8, 2)
(a, b, c, d) = (3, 9, 10, 4)
(a, b, c, d) = (3, 10, 9, 2)
(a, b, c, d) = (4, 0, 1, 5)
(a, b, c, d) = (4, 1, 0, 3)
(a, b, c, d) = (4, 1, 2, 5)
(a, b, c, d) = (4, 2, 1, 3)
(a, b, c, d) = (4, 2, 3, 5)
(a, b, c, d) = (4, 3, 2, 3)
(a, b, c, d) = (4, 3, 4, 5)
(a, b, c, d) = (4, 4, 3, 3)
(a, b, c, d) = (4, 4, 5, 5)
(a, b, c, d) = (4, 5, 4, 3)
(a, b, c, d) = (4, 5, 6, 5)
(a, b, c, d) = (4, 6, 5, 3)
(a, b, c, d) = (4, 6, 7, 5)
(a, b, c, d) = (4, 7, 6, 3)
(a, b, c, d) = (4, 7, 8, 5)
(a, b, c, d) = (4, 8, 7, 3)
(a, b, c, d) = (4, 8, 9, 5)
(a, b, c, d) = (4, 9, 8, 3)
(a, b, c, d) = (4, 9, 10, 5)
(a, b, c, d) = (4, 10, 9, 3)
(a, b, c, d) = (5, 0, 1, 6)
(a, b, c, d) = (5, 1, 0, 4)
(a, b, c, d) = (5, 1, 2, 6)
(a, b, c, d) = (5, 2, 1, 4)
(a, b, c, d) = (5, 2, 3, 6)
(a, b, c, d) = (5, 3, 2, 4)
(a, b, c, d) = (5, 3, 4, 6)
(a, b, c, d) = (5, 4, 3, 4)
(a, b, c, d) = (5, 4, 5, 6)
(a, b, c, d) = (5, 5, 4, 4)
(a, b, c, d) = (5, 5, 6, 6)
(a, b, c, d) = (5, 6, 5, 4)
(a, b, c, d) = (5, 6, 7, 6)
(a, b, c, d) = (5, 7, 6, 4)
(a, b, c, d) = (5, 7, 8, 6)
(a, b, c, d) = (5, 8, 7, 4)
(a, b, c, d) = (5, 8, 9, 6)
(a, b, c, d) = (5, 9, 8, 4)
(a, b, c, d) = (5, 9, 10, 6)
(a, b, c, d) = (5, 10, 9, 4)
(a, b, c, d) = (6, 0, 1, 7)
(a, b, c, d) = (6, 1, 0, 5)
(a, b, c, d) = (6, 1, 2, 7)
(a, b, c, d) = (6, 2, 1, 5)
(a, b, c, d) = (6, 2, 3, 7)
(a, b, c, d) = (6, 3, 2, 5)
(a, b, c, d) = (6, 3, 4, 7)
(a, b, c, d) = (6, 4, 3, 5)
(a, b, c, d) = (6, 4, 5, 7)
(a, b, c, d) = (6, 5, 4, 5)
(a, b, c, d) = (6, 5, 6, 7)
(a, b, c, d) = (6, 6, 5, 5)
(a, b, c, d) = (6, 6, 7, 7)
(a, b, c, d) = (6, 7, 6, 5)
(a, b, c, d) = (6, 7, 8, 7)
(a, b, c, d) = (6, 8, 7, 5)
(a, b, c, d) = (6, 8, 9, 7)
(a, b, c, d) = (6, 9, 8, 5)
(a, b, c, d) = (6, 9, 10, 7)
(a, b, c, d) = (6, 10, 9, 5)
(a, b, c, d) = (7, 0, 1, 8)
(a, b, c, d) = (7, 1, 0, 6)
(a, b, c, d) = (7, 1, 2, 8)
(a, b, c, d) = (7, 2, 1, 6)
(a, b, c, d) = (7, 2, 3, 8)
(a, b, c, d) = (7, 3, 2, 6)
(a, b, c, d) = (7, 3, 4, 8)
(a, b, c, d) = (7, 4, 3, 6)
(a, b, c, d) = (7, 4, 5, 8)
(a, b, c, d) = (7, 5, 4, 6)
(a, b, c, d) = (7, 5, 6, 8)
(a, b, c, d) = (7, 6, 5, 6)
(a, b, c, d) = (7, 6, 7, 8)
(a, b, c, d) = (7, 7, 6, 6)
(a, b, c, d) = (7, 7, 8, 8)
(a, b, c, d) = (7, 8, 7, 6)
(a, b, c, d) = (7, 8, 9, 8)
(a, b, c, d) = (7, 9, 8, 6)
(a, b, c, d) = (7, 9, 10, 8)
(a, b, c, d) = (7, 10, 9, 6)
(a, b, c, d) = (8, 0, 1, 9)
(a, b, c, d) = (8, 1, 0, 7)
(a, b, c, d) = (8, 1, 2, 9)
(a, b, c, d) = (8, 2, 1, 7)
(a, b, c, d) = (8, 2, 3, 9)
(a, b, c, d) = (8, 3, 2, 7)
(a, b, c, d) = (8, 3, 4, 9)
(a, b, c, d) = (8, 4, 3, 7)
(a, b, c, d) = (8, 4, 5, 9)
(a, b, c, d) = (8, 5, 4, 7)
(a, b, c, d) = (8, 5, 6, 9)
(a, b, c, d) = (8, 6, 5, 7)
(a, b, c, d) = (8, 6, 7, 9)
(a, b, c, d) = (8, 7, 6, 7)
(a, b, c, d) = (8, 7, 8, 9)
(a, b, c, d) = (8, 8, 7, 7)
(a, b, c, d) = (8, 8, 9, 9)
(a, b, c, d) = (8, 9, 8, 7)
(a, b, c, d) = (8, 9, 10, 9)
(a, b, c, d) = (8, 10, 9, 7)
(a, b, c, d) = (9, 0, 1, 10)
(a, b, c, d) = (9, 1, 0, 8)
(a, b, c, d) = (9, 1, 2, 10)
(a, b, c, d) = (9, 2, 1, 8)
(a, b, c, d) = (9, 2, 3, 10)
(a, b, c, d) = (9, 3, 2, 8)
(a, b, c, d) = (9, 3, 4, 10)
(a, b, c, d) = (9, 4, 3, 8)
(a, b, c, d) = (9, 4, 5, 10)
(a, b, c, d) = (9, 5, 4, 8)
(a, b, c, d) = (9, 5, 6, 10)
(a, b, c, d) = (9, 6, 5, 8)
(a, b, c, d) = (9, 6, 7, 10)
(a, b, c, d) = (9, 7, 6, 8)
(a, b, c, d) = (9, 7, 8, 10)
(a, b, c, d) = (9, 8, 7, 8)
(a, b, c, d) = (9, 8, 9, 10)
(a, b, c, d) = (9, 9, 8, 8)
(a, b, c, d) = (9, 9, 10, 10)
(a, b, c, d) = (9, 10, 9, 8)
(a, b, c, d) = (10, 1, 0, 9)
(a, b, c, d) = (10, 2, 1, 9)
(a, b, c, d) = (10, 3, 2, 9)
(a, b, c, d) = (10, 4, 3, 9)
(a, b, c, d) = (10, 5, 4, 9)
(a, b, c, d) = (10, 6, 5, 9)
(a, b, c, d) = (10, 7, 6, 9)
(a, b, c, d) = (10, 8, 7, 9)
(a, b, c, d) = (10, 9, 8, 9)
(a, b, c, d) = (10, 10, 9, 9)

1. formula = xy + yz + xz = xyz
2. alphabets = xyyzxzxyz, set = [x, y, z]
3. total cases = 1331

(x, y, z) = (0, 0, 0)
(x, y, z) = (0, 0, 1)
(x, y, z) = (0, 0, 2)
(x, y, z) = (0, 0, 3)
(x, y, z) = (0, 0, 4)
(x, y, z) = (0, 0, 5)
(x, y, z) = (0, 0, 6)
(x, y, z) = (0, 0, 7)
(x, y, z) = (0, 0, 8)
(x, y, z) = (0, 0, 9)
(x, y, z) = (0, 0, 10)
(x, y, z) = (0, 1, 0)
(x, y, z) = (0, 2, 0)
(x, y, z) = (0, 3, 0)
(x, y, z) = (0, 4, 0)
(x, y, z) = (0, 5, 0)
(x, y, z) = (0, 6, 0)
(x, y, z) = (0, 7, 0)
(x, y, z) = (0, 8, 0)
(x, y, z) = (0, 9, 0)
(x, y, z) = (0, 10, 0)
(x, y, z) = (1, 0, 0)
(x, y, z) = (2, 0, 0)
(x, y, z) = (2, 3, 6)
(x, y, z) = (2, 4, 4)
(x, y, z) = (2, 6, 3)
(x, y, z) = (3, 0, 0)
(x, y, z) = (3, 2, 6)
(x, y, z) = (3, 3, 3)
(x, y, z) = (3, 6, 2)
(x, y, z) = (4, 0, 0)
(x, y, z) = (4, 2, 4)
(x, y, z) = (4, 4, 2)
(x, y, z) = (5, 0, 0)
(x, y, z) = (6, 0, 0)
(x, y, z) = (6, 2, 3)
(x, y, z) = (6, 3, 2)
(x, y, z) = (7, 0, 0)
(x, y, z) = (8, 0, 0)
(x, y, z) = (9, 0, 0)
(x, y, z) = (10, 0, 0)
```
